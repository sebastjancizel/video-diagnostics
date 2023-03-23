<template>
	<div>
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
.video-compare-container {
	background: var(--vt-c-black-soft);
	margin: 0 auto;
	display: inline-block;
	line-height: 0;
	position: absolute;
	left: 250px;
	/* Width of the navbar */
	top: 0;
	width: calc(100% - 250px);
	/* Subtract the navbar width */
	padding-top: 42.3%;
	max-width: 100vw;
	overflow: hidden;
}

.video-main {
	width: 100%;
	position: absolute;
	top: 0;
	height: 100%;
	max-height: 100vh;
}

.video-main::-webkit-media-controls {
	position: relative;
	z-index: 3;
}

.video-clipper {
	width: 50%;
	position: absolute;
	top: 0;
	bottom: 0;
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
	max-height: 100vh;
}

.video-button {
	width: 30%;
	background-color: var(--vt-c-black-soft);
	color: var(--vt-c-text-dark-1);
	padding: 12px 20px;
	border-radius: 5px;
	cursor: pointer;
	margin-bottom: 10px;
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
	display: flex;
	justify-content: center;
	align-items: center;
	gap: 10px;
	position: fixed;
	bottom: 20px;
	left: 50%;
	transform: translateX(-50%);
	width: calc(80% - 500px);
	/* Subtract the navbar width */
	padding: 0 0px;
	box-sizing: border-box;
	z-index: 4;
}

</style>

