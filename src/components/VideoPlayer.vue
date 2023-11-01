<template>
  <div class="container">
    <div class="video-compare-container" ref="container">
      <video class="video-main" :class="{ 'fullscreen': isFullscreen }" autoplay muted ref="mainVideo" preload="auto">
        <source :src="mainVideoSrc" type="video/mp4">
      </video>
      <div class="video-clipper" ref="clipper">
        <video class="video-clipped" :class="{ 'fullscreen': isFullscreen }" autoplay muted ref="clippedVideo"
          preload="auto">
          <source :src="clippedVideoSrc" type="video/mp4">
        </video>
      </div>
      <div class="split-line" ref="splitLine"></div>
    </div>
    <div class="video-labels" ref="videoLabels">
      <label>
        <input type="radio" name="video-selection" value="LEFT" v-model="selectedVideo" />
        <span class="video-label">LEFT: {{ videoLabels.clipped }}</span>
      </label>
      <label>
        <input type="radio" name="video-selection" value="RIGHT" v-model="selectedVideo" />
        <span class="video-label">RIGHT: {{ videoLabels.main }}</span>
      </label>
    </div>
    <div class="button-container">
      <button class="video-button" @click="swapVideos">Swap Videos</button>
      <button class="video-button" @click="resumeVideos">Resume Videos</button>
      <button class="video-button" @click="pauseVideos">Pause Videos</button>
      <button class="video-button" @click="toggleFullscreen">Toggle Fullscreen</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      clippedVideoSrc: '',
      mainVideoSrc: '',
      isFullscreen: false,
      selectedVideo: 'LEFT',
    }
  },
  mounted() {
    this.trackLocation = this.trackLocation.bind(this);
    this.videoContainer = this.$refs.container;
    this.videoClipper = this.$refs.clipper;
    this.clippedVideo = this.$refs.clippedVideo;
    this.mainVideo = this.$refs.mainVideo;
    this.splitLine = this.$refs.splitLine;

    this.videoContainer.addEventListener('mousemove', this.trackLocation, false);
    this.videoContainer.addEventListener('touchstart', this.trackLocation, false);
    this.videoContainer.addEventListener('touchmove', this.trackLocation, false);
    this.mainVideo.addEventListener('ended', this.syncVideos, false);
    this.clippedVideo.addEventListener('ended', this.syncVideos, false);

  },
  computed: {
    videoLabels() {
      return {
        main: this.mainVideoSrc.split('/').pop(),
        clipped: this.clippedVideoSrc.split('/').pop(),
      }
    }
  },
  methods: {
    trackLocation(e) {
      const rect = this.videoContainer.getBoundingClientRect();
      const position = ((e.pageX - rect.left) / this.videoContainer.offsetWidth) * 100;
      if (position <= 100) {
        this.videoClipper.style.width = position + '%';
        this.clippedVideo.style.width = (100 / position) * 100 + '%';
        this.splitLine.style.left = position + '%';
      }
    },

    async setClippedVideoSrc(src) {
      try {
        if (this.selectedVideo === 'LEFT') {
          await this.updateVideoSource(this.clippedVideo, src);
          this.clippedVideoSrc = src;
          this.videoLabels.clipped = this.getFileName(src);
        } else {
          await this.updateVideoSource(this.mainVideo, src);
          this.mainVideoSrc = src;
          this.videoLabels.main = this.getFileName(src);
        }
        await this.syncVideos();
      } catch (error) {
        console.error('Error when setting the video source or syncing:', error);
      }
    },

    updateVideoSource(videoElement, src) {
      videoElement.pause();
      videoElement.src = src;
      videoElement.load();

      // Return a Promise that resolves when the video is ready to play
      return new Promise((resolve) => {
        videoElement.onloadedmetadata = () => {
          resolve();
        };
        videoElement.onerror = () => {
          resolve(); // Resolve even on error to not block the Promise chain
        };
      });
    },

    async syncVideos() {
      console.log('syncing videos');
      this.mainVideo.currentTime = 0;
      this.clippedVideo.currentTime = 0;

      try {
        await this.mainVideo.play();
        await this.clippedVideo.play();
      } catch (error) {
        console.error('Error when trying to play videos:', error);
        // Handle play error, for example by showing a user-friendly message
      }
    },

    swapVideos() {
      const tempSrc = this.mainVideoSrc;
      this.mainVideoSrc = this.clippedVideoSrc;
      this.clippedVideoSrc = tempSrc;

      // Update the video sources
      this.updateVideoSource(this.mainVideo, this.mainVideoSrc);
      this.updateVideoSource(this.clippedVideo, this.clippedVideoSrc);

      this.videoLabels.main = this.getFileName(this.mainVideoSrc);
      this.videoLabels.clipped = this.getFileName(this.clippedVideoSrc);

      // Sync the videos
      this.syncVideos();
    },

    pauseVideos() {
      this.mainVideo.pause();
      this.clippedVideo.pause();
    },

    resumeVideos() {
      this.mainVideo.play();
      this.clippedVideo.play();
    },

    toggleFullscreen() {
      this.isFullscreen = !this.isFullscreen;
      if (document.fullscreenElement || document.webkitFullscreenElement) {
        if (document.exitFullscreen) {
          document.exitFullscreen();
        } else if (document.webkitExitFullscreen) {
          document.webkitExitFullscreen();
        }
      } else {
        const container = this.$refs.container;
        if (container.requestFullscreen) {
          container.requestFullscreen();
        } else if (container.webkitRequestFullscreen) {
          container.webkitRequestFullscreen();
        }
      }
    },

    getFileName(src) {
      return src.split('/').pop();
    }
  }
}
</script>

<style scoped>
.container {
  display: grid;
  grid-column: 2;
  grid-template-rows: auto 50px 50px;
  /* height: 100vh; */
  height: 99vh;
}

.video-compare-container {
  margin: auto;
  /* position: absolute; */
  display: inline-block;
  top: 0;
  width: 95%;
  /* padding-top: 56.25%; */
  height: calc(100vh - 200px);
  max-width: 100vw;
  overflow: hidden;
  grid-row: 1;
}

.video-main {
  width: 100%;
  position: absolute;
  top: 0;
  height: 100%;
  max-height: 100vh;
  object-fit: contain;
  grid-row: 1;
}

.video-clipper {
  top: 0;
  bottom: 0;
  width: 50%;
  position: absolute;
  overflow: hidden;
  max-height: 100vh;
  grid-row: 1;
}

.video-clipped {
  width: 200%;
  height: 100%;
  z-index: 1;
  object-fit: contain;
  grid-row: 1;
}

.split-line {
  position: absolute;
  top: 0;
  bottom: 0;
  width: 0.5px;
  background: #fff;
  z-index: 2;
  grid-row: 1;
}

.video-button {
  width: auto;
  background-color: var(--vt-c-black-soft);
  color: var(--vt-c-text-dark-1);
  padding: 12px 20px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  border: none;
  transition: background-color 0.2s, color 0.2s;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.video-button:hover {
  background-color: var(--vt-c-indigo);
  color: var(--vt-c-text-dark-1);
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
}

.button-container {
  width: auto;
  display: grid;
  gap: 10px;
  padding: 10px;
  box-sizing: border-box;
  z-index: 4;
  grid-row: 3;
  grid-template-columns: repeat(4, 1fr);
}

.video-labels {
  display: flex;
  justify-content: space-between;
  padding: 0 2.5%;
  grid-row: 2;
}

.video-labels label {
  display: flex;
  align-items: center;
  font-family: 'Arial', sans-serif;
  font-size: 16px;
  color: #fff;
  cursor: pointer;
  user-select: none;
}

.video-labels input[type="radio"] {
  display: none;
}

.video-labels .video-label {
  padding: 5px 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  margin-left: 10px;
}

.video-labels input[type="radio"]:checked+.video-label {
  background-color: var(--vt-c-indigo);
  color: #fff;
  border-color: var(--vt-c-indigo);
}
</style>

