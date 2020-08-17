<template>
  <v-app>
    <v-main>
      <video-wrapper/>
    </v-main>
  </v-app>
</template>

<script>
import VideoWrapper from '@/components/VideoWrapper';

export default {
  name: 'App',

  components: {
    VideoWrapper
  },

  data: () => ({
    //
  }),
};
</script>

<style>
html, body {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background-color: black;
}
</style>
