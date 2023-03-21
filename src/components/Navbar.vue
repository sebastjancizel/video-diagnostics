<template>
	<div class="navbar">
		<h3>Select Video</h3>
		<select v-model="selectedVideo" @change="onVideoChange">
			<option v-for="video in videoList" :key="video.id" :value="video.src">
				{{ video.title }}
			</option>
		</select>
	</div>
</template>

<script>
export default {
	name: 'Navbar',
	data() {
		return {
			selectedVideo: '',
			videoList: [
				{ id: 1, title: 'Video 1', src: '/src/assets/test.mp4' },
				{ id: 2, title: 'Video 2', src: '/src/assets/test2.mp4' },
			],
		};
	},
	methods: {
		onVideoChange() {
			this.$emit('video-selected', this.selectedVideo);
		},
	},
};
</script>

<style scoped>
.navbar {
	display: flex;
	justify-content: center;
	align-items: center;
	padding: 10px;
	background-color: #f8f9fa;
	border-bottom: 1px solid #dee2e6;
}

h3 {
	margin-right: 10px;
}
</style>
