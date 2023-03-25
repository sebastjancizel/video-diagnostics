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
      videoList: [],
    };
  },
  async mounted() {
    this.populateVideoList();
  },
  methods: {
    async populateVideoList() {
      // Import all mp4 files in /src/assets/videos folder
      const videoImports = import.meta.glob('/src/assets/videos/*.mp4');

      // Iterate through the imports and create the video objects
      let id = 1;
      for (const [src, importFunction] of Object.entries(videoImports)) {
        const file = await importFunction();
        const title = src.split('/').pop().replace('.mp4', '');
        this.videoList.push({ "id": id, "title": title, "src": src });
        id++;
      }
    },
    onVideoChange(src) {
      console.log(src);
      this.$emit('video-selected', src);
    },
  },
};
</script>


<style scoped>
.navbar {
	grid-column: 1;
	height: 100%;
	width: var(--navbar-width);
	padding: 10px;
	background-color: var(--vt-c-black);
	color: var(--vt-c-text-dark-1);
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
  border-radius: 6px;
  cursor: pointer;
  margin-bottom: 10px;
}

.video-tile:hover {
  background-color: var(--vt-c-indigo);
  color: var(--vt-c-text-dark-1);
}
</style>