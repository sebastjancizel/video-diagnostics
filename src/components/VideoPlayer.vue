<template>
	<div class="container">
		<div class="video-compare-container" ref="container">
			<video class="video-main" :class="{ 'fullscreen': isFullscreen }" autoplay muted ref="mainVideo">
				<source :src="mainVideoSrc" type="video/mp4">
			</video>
			<div class="video-clipper" ref="clipper">
				<video class="video-clipped" :class="{ 'fullscreen': isFullscreen }" autoplay muted ref="clippedVideo">
					<source :src="clippedVideoSrc" type="video/mp4">
				</video>
			</div>
			<div class="split-line" ref="splitLine"></div>
		</div>
		<div class="button-container">
			<button style="grid-column: 1" class="video-button" @click="swapVideos">Swap Videos</button>
			<button style="grid-column: 2" class="video-button" @click="resumeVideos">Resume Videos</button>
			<button style="grid-column: 3" class="video-button" @click="pauseVideos">Pause Videos</button>
			<button style="grid-column: 4" class="video-button" @click="toggleFullscreen">Toggle Fullscreen</button>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			clippedVideoSrc: '/src/assets/videos/shuffleboard.mp4',
			mainVideoSrc: '/src/assets/videos/bunny-gt.mp4',
			isFullscreen: false,
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
		setClippedVideoSrc(src) {
			this.updateVideoSource(this.mainVideo, this.mainVideoSrc);
			this.updateVideoSource(this.clippedVideo, src);
			this.clippedVideoSrc = src;
		},
		updateVideoSource(videoElement, src) {
			videoElement.pause();
			videoElement.src = src;
			videoElement.load();
			videoElement.play();
		},

		syncVideos() {
			this.mainVideo.currentTime = 0;
			this.clippedVideo.currentTime = 0;
			this.mainVideo.play();
			this.clippedVideo.play();
		},

		swapVideos() {
			const tempSrc = this.mainVideoSrc;
			this.mainVideoSrc = this.clippedVideoSrc;
			this.clippedVideoSrc = tempSrc;

			// Update the video sources
			this.updateVideoSource(this.mainVideo, this.mainVideoSrc);
			this.updateVideoSource(this.clippedVideo, this.clippedVideoSrc);

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

	}
}
</script>

<style scoped>
.container {
	display: grid;
	grid-column: 2;
	grid-template-rows: calc(100vh - 100px) 50px 50px;
	height: 100vh;
}
.video-compare-container {
	/* background: var(--vt-c-black-soft); */
	margin: 0 auto;
	position: absolute;
	display: inline-block;
	top: 0;
	width: 100%;
	padding-top: 42.3%;
	max-width: 80vw;
	overflow: hidden;
	grid-row: 1;
}

.video-main {
	width: 100%;
	position: absolute;
	top: 0;
	height: 100%;
	max-height: 100vh;
}

.video-clipper {
	top: 0;
	bottom: 0;
	width: 50%;
	position: absolute;
	overflow: hidden;
	max-height: 100vh;
}

.video-clipped {
	width: 200%;
	height: 100%;
	z-index: 1;
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
	background-color: var(--vt-c-indigo-hover);
	color: var(--vt-c-text-dark-1);
	box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
}

.button-container {
	width: auto;
	display: grid;
	gap: 10px;
	padding: 0 0px;
	box-sizing: border-box;
	z-index: 4;
	grid-row: 3;
	grid-template-columns: 4;
}

</style>

