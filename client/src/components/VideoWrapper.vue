<template>
  <div>
    <video id="video" autoplay muted/>
    <v-btn @click="playPause" fab fixed bottom left color="primary">
      <v-icon v-if="playing">mdi-pause</v-icon>
      <v-icon v-else>mdi-play</v-icon>
    </v-btn>
  </div>
</template>

<script>
import {pipelines} from 'media-stream-library';

/** * @type {HTMLVideoElement|null} */
let el = null;
let pipeline = null;

export default {
  name: 'VideoWrapper',
  data() {
    return {
      config: {
        ws: {uri: `ws://192.168.0.2:8854/`},
        rtsp: {uri: `rtsp://192.168.0.6:554/s0`},
      },
      playing: false
    };
  },
  async mounted() {
    el = document.getElementById('video');
    el.addEventListener('play', () => {
      this.playing = true;
    });
    el.addEventListener('pause', () => {
      this.playing = false;
    });
    window.v1 = el;
    pipeline = new pipelines.Html5VideoPipeline({
      ...this.config,
      mediaElement: el
    });
    await this.play();
  },
  methods: {
    async play() {
      console.log('play');
      await pipeline.ready;
      console.log('playing');
      pipeline.rtsp.play();
      el.play();
    },
    async playPause() {
      if (this.playing) {
        el.pause();
      } else {
        await this.recreate();
      }
    },
    async recreate() {
      pipeline = new pipelines.Html5VideoPipeline({
        ...this.config,
        mediaElement: el
      });
      await this.play();
    }
  }
}
</script>

<style scoped>
  video {
    max-width: 100vw;
    max-height: 100vh;
  }
</style>
