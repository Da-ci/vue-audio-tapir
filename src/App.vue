<template>
  <!-- eslint-disable max-len -->
  <tapir-widget class="mt-20" :time="2" :successfulUpload="success" :failedUpload="failed" :afterRecording="afterRec"
    :backendEndpoint="backendEndpoint" audioFormat="MP3" />
  <!-- eslint-disable max-len -->
</template>

<script>
/* eslint-disable */
import TapirWidget from './components/TapirWidget.vue';

export default {
  name: 'App',
  components: {
    TapirWidget,
  },
  computed: {
    backendEndpoint() {
      return process.env.VUE_APP_BACKEND_ENDPOINT;
    }
  },
  methods: {
    success() {
      console.log("SUCCESS UPLOAD!!");
    },
    failed() {
      console.log("FAILED!");
    },
    afterRec() {
      console.log("Recorded successfully!");
    },
  },
};
</script>
