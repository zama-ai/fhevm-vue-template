<script setup lang="ts">
import { ref } from 'vue';
import { BrowserProvider } from 'ethers';
import Devnet from './Devnet.vue';

import { createFhevmInstance } from '../fhevmjs';

const AUTHORIZED_CHAIN_ID = ['0xaa36a7', '0x2328'];

const validNetwork = ref(false);
const connected = ref(false);
const loading = ref(false);
const account = ref<string | null>(null);
const hasWallet = !!window.ethereum;
const provider = new BrowserProvider(window.ethereum);

const refreshNetwork = async () => {
  if (await hasValidNetwork()) {
    validNetwork.value = true;
    loading.value = true;
    await createFhevmInstance();
    loading.value = false;
  }
};

const switchNetwork = async () => {
  try {
    await window.ethereum.request({
      method: 'wallet_switchEthereumChain',
      params: [{ chainId: AUTHORIZED_CHAIN_ID[0] }],
    });
  } catch (e) {
    console.error('No Sepolia chain configured');
  }
};

const connect = ref(async () => {
  if (!provider) {
    return;
  }
  const accounts: string[] = await provider.send('eth_requestAccounts', []);

  if (accounts.length > 0) {
    account.value = accounts[0];
    connected.value = true;
    if (!(await hasValidNetwork())) {
      await switchNetwork();
    }
  }
});

const hasValidNetwork = async (): Promise<boolean> => {
  const currentChainId: string = await window.ethereum.request({ method: 'eth_chainId' });
  return AUTHORIZED_CHAIN_ID.includes(currentChainId.toLowerCase());
};

const refreshAccounts = (accounts: string[]) => {
  if (accounts.length > 0) {
    account.value = accounts[0];
    connected.value = accounts.length > 0;
  }
};

provider
  .send('eth_accounts', [])
  .then(async (accounts: string[]) => {
    refreshAccounts(accounts);
    refreshNetwork();
  })
  .catch(() => {
    // Do nothing
  });
window.ethereum.on('accountsChanged', refreshAccounts);
window.ethereum.on('chainChanged', refreshNetwork);
</script>

<template>
  <div v-if="hasWallet">
    <div v-if="connected">
      <div v-if="validNetwork">
        <div v-if="loading">Loading</div>
        <div v-else>
          <div class="account">Connected with {{ account }}</div>
          <div class="child"><Devnet :account="account" :provider="provider" /></div>
        </div>
      </div>
      <div v-else><button class="button" @click="switchNetwork">Switch to Sepolia</button></div>
    </div>
    <div v-else><button class="button" @click="connect">Connect your wallet</button></div>
  </div>
  <div v-else>No wallet has been found</div>
</template>

<style scoped>
.account {
  width: 100%;
  max-width: 300px;
  text-overflow: ellipsis;
  overflow: hidden;
  margin: 0 auto;
}

.wrongNetwork {
  color: #ff0000;
}

.button {
  border: 1px solid #999;
  background: #eee;
}

.button:hover {
  border: 1px solid #000;
}
</style>
