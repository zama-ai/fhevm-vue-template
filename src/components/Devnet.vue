<script setup lang="ts">
import { ref } from 'vue';
import { getInstance } from '../fhevmjs';

const toHexString = (bytes: Uint8Array) => bytes.reduce((str, byte) => str + byte.toString(16).padStart(2, '0'), '');

// defineProps<{ msg: string }>();
const instance = getInstance();
const input = instance.createEncryptedInput(
  '0x309cf2aae85ad8a1db70ca88cfd4225bf17a7482',
  '0xCE835273d4A97d324A11e30BC900c43C1c1269F9'
);
input.add64(1337);
const { handles, inputProof } = input.encrypt();

const { publicKey } = instance.generateKeypair();
const eip712 = instance.createEIP712(publicKey, '0x309cf2aae85ad8a1db70ca88cfd4225bf17a7482');
</script>

<template>
  <main>
    <dl>
      <dt class="title">This is an encryption of 1337:</dt>
      <dd class="dd">
        <pre v-if="handles" class="pre">Handle: {{ toHexString(handles[0]) }}</pre>
        <pre v-if="inputProof" class="pre">Input Proof: {{ toHexString(inputProof) }}</pre>
      </dd>
      <dt class="title">And this is a EIP-712 token</dt>
      <dd class="dd">
        <pre v-if="eip712" class="pre"> {{ JSON.stringify(eip712) }}</pre>
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
