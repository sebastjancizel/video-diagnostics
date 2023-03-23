<template>
	<div class="navbar">
		<div class="video-list">
			<div v-for="video in videoList" :key="video.id" class="video-tile" @click="onVideoChange(video.src)">
				{{ video.title }}
			</div>
		</div>
	</div>
</template>


<script>
export default {
	name: 'Navbar',
	data() {
		return {
			selectedVideo: '',
			videoList: [
				{ id: 1, title: 'Shuffleboard', src: '/src/assets/test.mp4' },
				{ id: 2, title: 'GT Video', src: '/src/assets/test2.mp4' },
				{ id: 3, title: 'Compressed Video', src: '/src/assets/compression.mp4' },
			],
		};
	},
	methods: {
		onVideoChange(src) {
			console.log(src);
			this.$emit('video-selected', src);
		},
	},
};
</script>

<style scoped>
.navbar {
	position: fixed;
	left: 0;
	top: 0;
	height: 100%;
	width: 250px;
	padding: 10px;
	background-color: var(--vt-c-black);
	color: var(--vt-c-text-dark-1);
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	border-right: 1px solid var(--vt-c-divider-dark-1);
}

h3 {
	margin-bottom: 10px;
}

.video-list {
  width: 100%;
}

.video-tile {
  width: 100%;
  background-color: var(--vt-c-black-soft);
  color: var(--vt-c-text-dark-1);
  padding: 10px;
  border-radius: 3px;
  cursor: pointer;
  margin-bottom: 10px;
}

.video-tile:hover {
  background-color: var(--vt-c-indigo-hover);
  color: var(--vt-c-text-dark-1);
}
</style>