<template>
  <div class="navbar">
    <div class="search-bar">
      <input type="text" v-model="searchQuery" placeholder="Search videos..." />
    </div>
    <div class="scene-list">
      <div v-for="scene in filteredSceneList" :key="scene.id" class="scene-folder" @click="toggleScene(scene.id)">
        <div class="scene-title">
          <font-awesome-icon class="folder-icon" :icon="scene.isOpen ? 'folder-open' : 'folder'" />{{ scene.title
          }}
        </div>
        <div v-if="scene.isOpen">
          <div v-for="video in scene.videoList" :key="video.id" class="video-tile" @click.stop="onVideoChange(video.src)">
            <font-awesome-icon class="tree-branch" icon="video" />
            {{ video.title }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'


export default {
  name: "Navbar",
  components: {
    FontAwesomeIcon,
  },
  data() {
    return {
      selectedVideo: "",
      sceneList: [],
      searchQuery: "",
    };
  },
  async mounted() {
    this.populateSceneList();
  },
  computed: {
    filteredSceneList() {
      if (!this.searchQuery) {
        return this.sceneList;
      }

      try {
        const regex = new RegExp(this.searchQuery, 'i'); // 'i' for case insensitive

        return this.sceneList.map(scene => ({
          ...scene,
          videoList: scene.videoList.filter(video => regex.test(video.title))
        })).filter(scene => scene.videoList.length > 0);
      } catch (e) {
        // If the regex is invalid, return the full scene list
        return this.sceneList;
      }
    }
  },
  methods: {
    async populateSceneList() {
      // Import all mp4 files in /src/assets/videos folder
      const videoImports = import.meta.glob("/src/assets/videos/*/*.mp4");

      // Iterate through the imports and create the video objects
      let sceneId = 1;
      const scenes = {};

      for (const [src, importFunction] of Object.entries(videoImports)) {
        const file = await importFunction();
        const pathParts = src.split("/");
        const sceneTitle = pathParts[pathParts.length - 2];
        const videoTitle = pathParts.pop().replace(".mp4", "");

        if (!scenes[sceneTitle]) {
          scenes[sceneTitle] = {
            id: sceneId,
            title: sceneTitle,
            isOpen: false,
            videoList: [],
          };
          sceneId++;
        }

        scenes[sceneTitle].videoList.push({
          id: scenes[sceneTitle].videoList.length + 1,
          title: videoTitle,
          src: src,
        });
      }

      this.sceneList = Object.values(scenes);
    },
    toggleScene(sceneId) {
      this.sceneList = this.sceneList.map((scene) =>
        scene.id === sceneId ? { ...scene, isOpen: !scene.isOpen } : scene
      );
    },
    onVideoChange(src) {
      this.$emit("video-selected", src);
    },
  },
};
</script>

<style scoped>
.navbar {
  grid-column: 1;
  height: 100vh;
  width: auto;
  padding: 10px;
  background-color: var(--vt-c-black);
  color: var(--vt-c-text-dark-1);
  border-right: 1px solid var(--vt-c-divider-dark-1);
  overflow-y: auto;
}

h3 {
  margin-bottom: 10px;
}

.scene-list {
  width: 100%;
}


.scene-folder {
  display: flex;
  flex-direction: column;
  width: auto;
  padding: 10px;
  margin-bottom: 10px;
  cursor: pointer;
  background-color: var(--vt-c-black-soft);
  border-radius: 6px;
}

.scene-folder:hover {
  background-color: var(--vt-c-black-mute);
  color: var(--vt-c-text-dark-1);
}

.scene-title {
  display: flex;
  align-items: center;
}

.folder-icon {
  margin-right: 5px;
}

.video-tile {
  display: flex;
  align-items: center;
  width: auto;
  color: var(--vt-c-text-dark-1);
  padding: 5px 10px;
  border-radius: 6px;
  cursor: pointer;
  margin-left: 20px;
  margin-bottom: 5px;
}

.video-tile:hover {
  background-color: var(--vt-c-indigo);
  color: var(--vt-c-text-dark-1);
}

.tree-branch {
  margin-right: 5px;
}

.search-bar {
  margin-bottom: 20px;
}

.search-bar input[type="text"] {
  width: 100%;
  padding: 10px;
  margin: 0;
  background-color: var(--vt-c-black-soft);
  border: 1px solid var(--vt-c-divider-dark-1);
  border-radius: 6px;
  color: var(--vt-c-text-dark-1);
  font-size: 1rem;
}

.search-bar input[type="text"]::placeholder {
  color: var(--vt-c-text-dark-1);
  opacity: 0.7;
}

.search-bar input[type="text"]:focus {
  outline: none;
  border-color: var(--vt-c-indigo);
  box-shadow: 0 0 0 2px rgba(102, 123, 232, 0.3);
}
</style>
