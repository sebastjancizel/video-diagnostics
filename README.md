# video-diagnostic-app

## Project Description
This is a frontend for comparing videos. It features a video player with a comparison slider that allows the user to compare the two videos.

This is a very early version of the app. To be able to use it you have to follow the project set-up below.

## Project Setup

### Install Node.js
First you need to install Node.js. For debian based systems you can use the following command:

```sh
sudo apt install nodejs
```

### Install Dependencies

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

This will run the app locally in development mode. To point the app to the folder with the videos you want to compare, you can symlink the ./src/assets/videos folder to the folder with the videos. For example:

```sh
ln -s ./src/assets/videos /path/to/videos
```
