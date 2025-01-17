# vue-audio-tapir [![npm](https://img.shields.io/npm/v/vue-audio-tapir)](https://www.npmjs.com/package/vue-audio-tapir) [![npm](https://img.shields.io/npm/l/vue-audio-tapir)](https://www.npmjs.com/package/vue-audio-tapir) ![Snyk Vulnerabilities for GitHub Repo](https://img.shields.io/snyk/vulnerabilities/github/tderflinger/vue-audio-tapir)

<div style="text-align: right"><a href="./README-zh.md">中文</a></div>
<br/>

Audio recorder component for Vue.js 3. It enables to record, play and send audio messages to a server.
There is a corresponding serverless backend function called [audio-tapir-function](https://github.com/tderflinger/audio-tapir-function).
You can use both in combination to send you an email with the recorded audio message.

<img src="./docs/the-tapir-color-new.png" style="display: block; margin: 40px auto; width: 200px" />

## Demo

[Demo page](https://tderflinger.github.io/vue-audio-tapir/) of Audio Tapir,
you can record and play audio, but submitting is disabled.

## Use Case

The most popular use case of vue-audio-tapir is to deliver an
audio contact message directly from a website to your email inbox.

<img src="./docs/screenshot.png" style="display: block; margin: 40px auto; width: 300px" />
 
## Architecture Overview

<img src="./docs/architecture-overview.png" style="display: block; margin: 40px auto; width: 550px" />

The developer includes the vue-audio-tapir component in his Vue 3 website. When the user of the website
wants to leave a voice message, he records his message and submits it. Vue-audio-tapir contacts the
backend server, a Netlify serverless function and uploads the audio data. The serverless backend function
sends the audio data to an email address using the email service Sendgrid.
Finally the email with the voice message arrives at the destination, probably the email application of the
website owner.

## Installation

In a Vue 3 application install with:

```bash
npm i vue-audio-tapir --save
```

or
```bash
yarn add vue-audio-tapir --save
```

## Example Usage

```html
<template>
  <tapir-widget :time="2" backendEndpoint="https://your-endpoint.com/.netlify/functions/audio-message" 
                buttonColor="green" audioFormat="MP3"/>
</template>

<script>
import TapirWidget from 'vue-audio-tapir';
import 'vue-audio-tapir/dist/vue-audio-tapir.css';

export default {
  name: 'App',
  components: {
    TapirWidget,
  }
}
</script>
```

## Properties

| Name             |  Type    | Description                                       | 
|------------------|----------|---------------------------------------------------|
| time             | Number   | Maximum recording time in minutes                 |
| bitRate          | Number   | bit rate of recording                             |
| sampleRate       | Number   | sample rate of recording                          |
| backendEndpoint  | String   | URL of the service that receives the data as POST |
| buttonColor      | String   | color code of the buttons for theming             |
| audioFormat      | String   | file format of audio recording (MP3 or WAV)       |
| afterRecording   | Function | callback function when recording is finished.     |
| successfulUpload | Function | callback function that is called when data is uploaded successfully |
| failedUpload     | Function | callback function that is called when upload failed. |

The data sent to the server is encoded either in the MP3 or WAV format.
For MP3 encoding the [lamejs](https://github.com/zhuker/lamejs) library is used.

MP3 encoding saves space but needs more processing power on the client device. WAV has
bigger file size but the encoding is faster and needs less power.

## Dependencies

- [Vue 3](https://v3.vuejs.org/)
- [Tailwindcss](https://tailwindcss.com/)
- [lamejs](https://github.com/zhuker/lamejs)

## More Information

Follow me on Twitter for updates: [@tderflinger](https://twitter.com/tderflinger)

Your can read more about the Audio Tapir project on my blog: 

https://www.tderflinger.com/en/easily-receive-audio-messages-from-users

## Further Reading

- [Web Audio API on MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [WAV File Format](https://en.wikipedia.org/wiki/WAV)
- [MP3 File Format](https://en.wikipedia.org/wiki/MP3)

## References

This project has been inspired by [vue-audio-recorder](https://github.com/grishkovelli/vue-audio-recorder)
by Gennady Grishkovtsov.

## License

MIT License
