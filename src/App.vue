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
  data() {
    return {
      selectedVideo: '',
    }
  },
  methods: {
    changeClippedVideo(src) {
      this.$refs.videoCompare.setClippedVideoSrc(src);
    },
  },
};
</script>


<style scoped>
#app{
  display: grid;
  grid-template-columns: auto auto;
}
</style>
