<script setup lang="ts">
import { getAddress } from 'ethers';
import { ref } from 'vue';
import { getInstance } from '../fhevmjs';

const toHexString = (bytes: Uint8Array) => bytes.reduce((str, byte) => str + byte.toString(16).padStart(2, '0'), '');

const handles = ref<Uint8Array[] | null>(null);
const inputProof = ref<Uint8Array | null>(null);

const instance = getInstance();

const encrypt = async () => {
  const enc = await instance
    .createEncryptedInput(
      getAddress('0x309cf2aae85ad8a1db70ca88cfd4225bf17a7456'),
      getAddress('0x309cf2aae85ad8a1db70ca88cfd4225bf17a7482')
    )
    .add64(1337)
    .encrypt();
  handles.value = enc.handles;
  inputProof.value = enc.inputProof;
};

const { publicKey } = instance.generateKeypair();
const eip712 = instance.createEIP712(publicKey, '0x309cf2aae85ad8a1db70ca88cfd4225bf17a7482');
</script>

<template>
  <main>
    <button class="button" @click="encrypt">Encrypt 1337</button>
    <dl>
      <dt class="title">This is an encryption of 1337:</dt>
      <dd class="dd" v-if="handles">
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
