<template>
	<nav class="navbar">
	  <ul>
		<li v-for="(video, index) in videos" :key="index" @click="selectVideo(index)">
		  {{ video.name }}
		</li>
	  </ul>
	</nav>
  </template>

  <script>
  export default {
	props: {
	  videos: Array, // an array of objects that contain information about the videos
	  selectVideo: Function // a function that is called when a video is selected
	}
  }
  </script>

  <style>
  .navbar {
	position: fixed;
	top: 0;
	right: 0;
	width: 200px;
	height: 100%;
	background: #333;
	color: white;
	font-size: 1.2em;
	display: flex;
	justify-content: center;
	align-items: center;
  }

  .navbar ul {
	list-style: none;
	padding: 0;
	margin: 0;
  }

  .navbar li {
	padding: 1rem;
	cursor: pointer;
  }

  .navbar li:hover {
	background: white;
	color: #333;
  }
  </style>
