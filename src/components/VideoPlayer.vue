<template>
	<div class="video-compare-container" ref="container">
		<video class="video-main" loop autoplay muted controls ref="mainVideo">
			<source :src="mainVideoSrc" type="video/mp4">
		</video>
		<div class="video-clipper" ref="clipper">
			<video class="video-clipped" loop autoplay muted controls ref="clippedVideo">
				<source :src="clippedVideoSrc" type="video/mp4">
			</video>
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			mainVideoSrc: '/src/assets/test2.mp4',
			clippedVideoSrc: '/src/assets/test.mp4'
		}
	},
	mounted() {
		this.trackLocation = this.trackLocation.bind(this);
		this.videoContainer = this.$refs.container;
		this.videoClipper = this.$refs.clipper;
		this.clippedVideo = this.$refs.clippedVideo;
		this.mainVideo = this.$refs.mainVideo;
		this.videoContainer.addEventListener('mousemove', this.trackLocation, false);
		this.videoContainer.addEventListener('touchstart', this.trackLocation, false);
		this.videoContainer.addEventListener('touchmove', this.trackLocation, false);
	},
	methods: {
		trackLocation(e) {
			const rect = this.videoContainer.getBoundingClientRect();
			const position = ((e.pageX - rect.left) / this.videoContainer.offsetWidth) * 100;
			if (position <= 100) {
				this.videoClipper.style.width = position + '%';
				this.clippedVideo.style.width = (100 / position) * 100 + '%';
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
	}
}
</script>

<style scoped>
.video-compare-container {
	background: #333;
	margin: 0 auto;
	display: inline-block;
	line-height: 0;
	position: relative;
	width: 90vw;
	padding-top: 42.3%;
}

.video-main {
	width: 100%;
	position: absolute;
	top: 0;
	height: 100%;
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
}

.video-clipped {
	width: 200%;
	height: 100%;
	z-index: 1;
}
</style>
