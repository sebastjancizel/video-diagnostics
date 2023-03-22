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
				{ id: 1, title: 'Video 1', src: '/src/assets/compression.mp4' },
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

select {
  width: 100%;
  background-color: var(--vt-c-black-soft);
  color: var(--vt-c-text-dark-1);
  border: 1px solid var(--vt-c-divider-dark-1);
  padding: 5px;
  border-radius: 3px;
}

select:focus {
  outline: none;
  border-color: var(--vt-c-indigo);

}
</style>