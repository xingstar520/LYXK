<script setup lang="ts">
import FaceSlider from './components/FaceSlider.vue';
</script>

<template>
  <div class="center-container">
    <FaceSlider />
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Rubik', sans-serif;
  background: linear-gradient(135deg, #ffccd5, #ff8fa3);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>