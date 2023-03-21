<template>
  <div id="app">
    <Navbar @video-selected="changeClippedVideo"></Navbar>
    <VideoCompare ref="videoCompare"></VideoCompare>
  </div>
</template>

<script>
import Navbar from './components/Navbar.vue';
import VideoCompare from './components/VideoPlayer.vue';

export default {
  name: 'App',
  components: {
    Navbar,
    VideoCompare,
  },
  methods: {
    changeClippedVideo(src) {
      this.$refs.videoCompare.setClippedVideoSrc(src);
    },
  },
};
</script>
