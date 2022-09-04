<script setup lang="ts">
import { onMounted, ref } from "vue";

onMounted(() => {
  video = document.getElementById("video") as HTMLVideoElement;
});
let video = undefined;
let mediaStream = undefined;
let mediaRecorder = undefined;
const videoBuffer = [];
const start = async () => {
  if (!navigator.mediaDevices || !navigator.mediaDevices.getDisplayMedia) {
    alert("当前浏览器不支持录屏");
  }
  mediaStream = await navigator.mediaDevices.getDisplayMedia();
  mediaRecorder = new MediaRecorder(mediaStream, {
    mimeType: "video/webm;codecs=vp9",
    audioBitsPerSecond: 128000, //音频码率
    videoBitsPerSecond: 250000000, //视频码率
  });
  mediaRecorder.ondataavailable = (e: any) => {
    console.log(e);
    if (e.data.size > 0) {
      videoBuffer.push(e.data);
    }
  };
  mediaRecorder.start(1000);
  video.srcObject = mediaStream;
};
const stop = () => {
  if (mediaStream) {
    for (let track of mediaStream.getTracks()) {
      track.stop();
    }
  }
};
</script>

<template>
  <button @click="start">开始录制</button>
  <button @click="stop">结束录制</button>
  <video
    id="video"
    ref="video"
    autoplay
    style="width: 600px; height: 400px; border: 1px solid #efefef"
  ></video>
</template>

<style scoped></style>
