<script setup lang="ts">
import { ref } from 'vue';
import { getInstance } from '../fhevmjs.ts';

const toHexString = (bytes: Uint8Array) => bytes.reduce((str, byte) => str + byte.toString(16).padStart(2, '0'), '');

// defineProps<{ msg: string }>();
const instance = getInstance();
const encryption = instance.encrypt32(1337);

const token = instance.generatePublicKey({
  verifyingContract: '0x309cf2aae85ad8a1db70ca88cfd4225bf17a7482',
});
</script>

<template>
  <main>
    <dl>
      <dt class="title">This is an encryption of 1337:</dt>
      <dd class="dd">
        <pre v-if="encryption" class="pre">{{ toHexString(encryption) }}</pre>
      </dd>
      <dt class="title">And this is a EIP-712 token</dt>
      <dd class="dd">
        <pre v-if="token" class="pre"> {{ JSON.stringify(token.eip712) }}</pre>
      </dd>
    </dl>
  </main>
</template>

<style scoped>
.dd {
  margin: 0;
}

.pre {
  white-space: break-spaces;
  word-wrap: break-word;
}

.title {
  font-weight: bold;
}
</style>
