<template>
	<div class="video-player-container">
	<video-player
	  src="/src/assets/cat.mp4"
	  controls
	  :loop="true"
	  :volume="0.1"
	  muted
	/>
	</div>
</template>

<script>
import { defineComponent } from 'vue'
import { VideoPlayer } from '@videojs-player/vue'
import 'video.js/dist/video-js.css'

export default defineComponent({
	components: {
	VideoPlayer
	}
})
</script>


<style scoped>
/* styles for the video player container */
.video-player-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

/* styles for the video player */
.video-player {
  /* center the video player */
  margin: 0 auto;
  width: 100%;
  height: 100%;
  object-fit: contain;
}

/* styles for the video player controls */
.vjs-control-bar {
  /* center the controls */
  display: flex;
  justify-content: center;
  align-items: center;
}

.video-js .vjs-big-play-button {
  position: static !important;
}


</style>

