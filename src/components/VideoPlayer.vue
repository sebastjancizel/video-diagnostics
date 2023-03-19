<template>
	<div class="videoplayer">
	  <div class="video-container1">
		<video-player
		  id="video1"
		  src="/src/assets/test.mp4"
		  controls
		  :loop="true"
		  :volume="0.1"
		  muted
		/>
	  </div>
	  <div class="video-container2">
		<video-player
		  id="video2"
		  src="/src/assets/test2.mp4"
		  controls
		  :loop="true"
		  :volume="0.1"
		  muted
		/>
	  </div>
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
.videoplayer {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.video-container1 {
  width: 100%;
  height: 100%;
  position: absolute;
  z-index: 2;
}

.video-container2 {
  width: 100%;
  height: 100%;
  position: absolute;
  z-index: 0;
}



</style>

