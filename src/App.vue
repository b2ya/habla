<script setup>
import { ref, onMounted } from "vue";
const transcript = ref("");
const timeNow = ref("");
const isRecording = ref(false);
const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition;
const sr = new Recognition();
onMounted(() => {
  sr.continuous = true;
  sr.interimResults = true;
  sr.onstart = () => {
    console.log("SR Started");
    isRecording.value = true;
  };
  sr.onend = () => {
    console.log("SR Stopped");
    isRecording.value = false;
  };
  sr.onresult = evt => {
    for (let i = 0; i < evt.results.length; i++) {
      const result = evt.results[i];
      if (result.isFinal) CheckForCommand(result);
    }
    const t = Array.from(evt.results)
      .map(result => result[0])
      .map(result => result.transcript)
      .join("");

    transcript.value = t;
  };
});
const CheckForCommand = result => {
  const t = result[0].transcript;
  if (
    t.includes("para la grabación") ||
    t.includes("para grabacion") ||
    t.includes("para la grabacion")
  ) {
    sr.stop();
  } else if (
    t.includes("qué hora es") ||
    t.includes("dime la hora") ||
    t.includes("qué hora es ahora")
  ) {
    sr.stop();
    timeNow.value = new Date().toLocaleTimeString();
    setTimeout(() => sr.start(), 1000);
  }
};
const ToggleMic = () => {
  if (isRecording.value) {
    sr.stop();
  } else {
    sr.start();
  }
};
</script>

<template>
  <div class="app">
    <button :class="`mic`" @click="ToggleMic">Record</button>
    <div class="transcript" v-text="transcript"></div>

    <div class="">
      <span class="" v-text="timeNow"></span>
    </div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Fira sans", sans-serif;
}
body {
  background: #281936;
  color: #fff;
}
</style>
