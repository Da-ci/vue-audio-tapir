<template>
  <!-- eslint-disable-next-line vue/max-attributes-per-line -->
  <!-- eslin-disable-no-trailing-spaces -->
  <div class="text-center font-sans rounded-lg"
    :style="{ 'background-color': 'white !important', 'margin-top': '30px', 'margin-bottom': '30px', 'max-width': '600px', }">
    <div>
      <div>
        <div class="text-sm font-bold">Durée : {{ recordedTime }}</div>
        <!-- <div class="text-sm font-bold">{{ successMessage }}</div>
        <div class="text-sm">{{ instructionMessage }}</div>
        <div class="text-sm text-red-400">{{ errorMessage }}</div> -->
      </div>
    </div>
    <figure class="mt-8" style="width: 100%">
      <div class="flex flex-nowrap">
        <div style="margin-top: -15px; margin-right: 10px;">
          <icon-button :style="{ 'border-color': '#242331' }" :class="buttonClass" v-if="recording" name="stop"
            @click="toggleRecording" />
          <icon-button :style="{ 'border-color': '#242331' }" :class="buttonClass" v-else name="mic"
            @click="toggleRecording" />
        </div>
        <audio controls :src="recordedAudio" type="audio/mpeg" class="mx-right" style="width: 100%">
          Your browser does not support the
          <code>audio</code> element.
        </audio>
      </div>
      <!-- <figcaption class="text-sm">Écoutez votre enregistrement avant de le soumettre.</figcaption> -->
      <figcaption>
        <div class="text-sm font-bold">{{ successMessage }}</div>
        <div class="text-sm">{{ instructionMessage }}</div>
        <div class="text-sm text-red-400">{{ errorMessage }}</div>
      </figcaption>
    </figure>
    <submit-button @submit="sendData" :color="buttonColor" style="display: none;" />
  </div>

</template>

<script>
import IconButton from "./IconButton.vue";
import SubmitButton from "./SubmitButton.vue";
import Recorder from "../lib/recorder";
import convertTimeMMSS from "../lib/utils";
import "../styles/app.css";

const INSTRUCTION_MESSAGE = "Cliquez sur l'icône pour commencer à enregistrer le message.";
const INSTRUCTION_MESSAGE_STOP = "Cliquez à nouveau sur l'icône pour arrêter l'enregistrement.";
const ERROR_MESSAGE = "L'utilisation du microphone a échoué. Veuillez rafraîchir et réessayer et autoriser l'utilisation d'un microphone.";
const SUCCESS_MESSAGE = "Message enregistré avec succès !";
const SUCCESS_MESSAGE_SUBMIT = "Le message audio a été soumis avec succès ! Merci !";
const ERROR_SUBMITTING_MESSAGE = "Erreur de soumission du message audio ! Veuillez réessayer plus tard.";

export default {
  name: "TapirWidget",
  props: {
    time: { type: Number, default: 1 }, // in minutes
    bitRate: { type: Number, default: 128 },
    sampleRate: { type: Number, default: 44100 },
    backendEndpoint: { type: String, default: "" },
    buttonColor: { type: String, default: "green" },
    audioFormat: { type: String, default: "MP3" }, // can be MP3 or WAV

    // callback functions
    afterRecording: { type: Function },
    successfulUpload: { type: Function },
    failedUpload: { type: Function },

  },
  components: {
    IconButton,
    SubmitButton,
  },
  data() {
    return {
      recording: false,
      recordedAudio: null,
      recordedBlob: null,
      recorder: null,
      successMessage: null,
      errorMessage: null,
      instructionMessage: INSTRUCTION_MESSAGE,
    };
  },
  computed: {
    buttonClass() {
      return "mx-auto h-14 w-14 fill-current text-black cursor-pointer rounded-50 border-2 m-4 p-2";
    },
    recordedTime() {
      if (this.time && this.recorder?.duration >= this.time * 60) {
        this.toggleRecording();
      }
      return convertTimeMMSS(this.recorder?.duration) ? convertTimeMMSS(this.recorder?.duration) : '00:00';
    },

  },
  beforeUnmount() {
    if (this.recording) {
      this.stopRecorder();
    }
  },
  methods: {
    toggleRecording() {
      this.recording = !this.recording;
      if (this.recording) {
        this.initRecorder();
      } else {
        this.stopRecording();
      }
    },
    initRecorder() {
      this.recorder = new Recorder({
        micFailed: this.micFailed,
        bitRate: this.bitRate,
        sampleRate: this.sampleRate,
        format: this.audioFormat,
      });
      this.recorder.start();
      this.successMessage = null;
      this.errorMessage = null;
      this.instructionMessage = INSTRUCTION_MESSAGE_STOP;
    },
    stopRecording() {
      this.recorder.stop();
      const recordList = this.recorder.recordList();
      this.recordedAudio = recordList[0].url;
      this.recordedBlob = recordList[0].blob;
      if (this.recordedAudio) {
        this.successMessage = SUCCESS_MESSAGE;
        this.instructionMessage = null;
      }
      if (this.afterRecording) {
        this.afterRecording();
      }
    },
    async sendData() {
      if (!this.recordedBlob) {
        return;
      }

      try {
        const response = await fetch(this.backendEndpoint, {
          method: "POST",
          body: this.recordedBlob,
          mode: "cors",
          credentials: "same-origin",
          header: {
            "content-type": "audio/mpeg",
          },
        });
        if (!response.ok) {
          throw new Error("Error sending data!");
        }
        this.errorMessage = null;
        this.successMessage = SUCCESS_MESSAGE_SUBMIT;
        if (this.successfulUpload) {
          this.successfulUpload();
        }
      } catch (error) {
        this.successMessage = null;
        this.errorMessage = ERROR_SUBMITTING_MESSAGE;
        if (this.failedUpload) {
          this.failedUpload();
        }
      }
    },
    micFailed() {
      this.recording = false;
      this.instructionMessage = INSTRUCTION_MESSAGE;
      this.errorMessage = ERROR_MESSAGE;
    },
  },
};
</script>
