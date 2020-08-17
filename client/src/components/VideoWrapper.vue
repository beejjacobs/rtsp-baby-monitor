<template>
  <div>
    <video id="video" autoplay muted/>
    <v-btn @click="playPause" small fab fixed bottom left color="primary">
      <v-icon>mdi-{{ playing ? 'pause' : 'play' }}</v-icon>
    </v-btn>
    <v-btn @click="mute" small fab fixed bottom right :color="muted ? 'red' : 'blue'">
      <v-icon>mdi-volume-{{ muted ? 'off' : 'high' }}</v-icon>
    </v-btn>
  </div>
</template>

<script>
import {pipelines} from 'media-stream-library';

/** * @type {HTMLVideoElement|null} */
let el = null;
let pipeline = null;
const CHECK_TIME = 60 * 1000;

export default {
  name: 'VideoWrapper',
  data() {
    return {
      config: {
        ws: {uri: `ws://192.168.0.2:8854/`},
        rtsp: {uri: `rtsp://192.168.0.6:554/s0`},
      },
      muted: false,
      playing: false,
      checkId: 0
    };
  },
  async mounted() {
    if (el) {
      return;
    }
    el = document.getElementById('video');
    el.addEventListener('play', () => {
      this.playing = true;
    });
    el.addEventListener('pause', () => {
      this.playing = false;
    });
    el.addEventListener('ended', () => {
      this.playing = false;
    });
    el.addEventListener('volumechange', () => {
      this.muted = el.muted;
    });
    this.checkId = setInterval(() => this.check(), CHECK_TIME);
    window.v1 = el;
    await this.play();
  },
  methods: {
    check() {
      if (!this.playing) {
        console.log('check:play');
        this.play();
      }
    },
    async mute() {
      el.muted = !el.muted;
    },
    async playPause() {
      if (this.playing) {
        clearInterval(this.checkId);
        el.pause();
      } else {
        this.checkId = setInterval(() => this.check(), CHECK_TIME);
        await this.play();
      }
    },
    async play() {
      if (pipeline) {
        pipeline.close();
      }
      pipeline = new pipelines.Html5VideoPipeline({
        ...this.config,
        mediaElement: el
      });
      await pipeline.ready;
      pipeline.rtsp.play();
      try {
        el.play();
      } catch(e) {
        console.error('play error', e);
      }
    }
  }
}
</script>

<style scoped>
  video {
    max-width: 100vw;
    max-height: 100vh;
    margin: 0 auto;
    display: block;
  }
</style>
