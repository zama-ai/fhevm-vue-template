<script setup lang="ts">
import { ref, watchEffect } from 'vue';
import Connect from './components/Connect.vue';
import { init } from './fhevmjs';

const isInitialized = ref(false);

watchEffect(async () => {
  await init();
  isInitialized.value = true;
});
</script>

<template>
  <h1>fhevmjs</h1>
  <div v-if="isInitialized"><Connect /></div>
  <p className="read-the-docs">
    <a href="https://docs.zama.ai/fhevm">See the documentation for more information</a>
  </p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
