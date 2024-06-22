<script lang="ts">
  import { onMount } from 'svelte';
  import { writable } from 'svelte/store';
  import { connect } from 'starknetkit';

  // State variables
  let isConnected = false;
  let chain_id = 'Loading...';
  let inputValue = '';
  let currentBalance = 0;
  let wallet: any = null;

  // Store for wallet
  const walletStore = writable(null);

  // Function to connect the wallet
  async function connectWallet() {
    const { wallet: connectedWallet } = await connect();
    
    if (connectedWallet.isConnected) {
      isConnected = true;
      walletStore.set(connectedWallet);
      wallet = connectedWallet;
      chain_id = wallet.chainId;
    } else {
      chain_id = 'Failed to connect';
    }
  }

  // Function to fetch the balance
  async function fetchBalance() {
    if (wallet == null) {
      console.error("Wallet is not connected");
      return;
    }
    try {
      const tx = await wallet.account.execute({
        contractAddress: "0x7ec39ed1b0a051396104a7951133fe831a056d80702fd47130d5d73e29d3976",
        selector: "get_balance",
        entrypoint: "get_balance",
        calldata: []
      });
      currentBalance = tx;
    } catch (error) {
      console.error('Transaction failed:', error);
      console.error('Error details:', JSON.stringify(error, null, 2));
    }
  }

  // Function to add balance
  async function addBalance() {
    if (wallet == null) {
      console.error("Wallet is not connected");
      return;
    }
    try {
      const tx = await wallet.account.execute({
        contractAddress: "0x7ec39ed1b0a051396104a7951133fe831a056d80702fd47130d5d73e29d3976",
        selector: "add_balance",
        entrypoint: "add_balance",
        calldata: [inputValue]
      });
      console.log('Transaction successful:', tx);
    } catch (error) {
      console.error('Transaction failed:', error);
      console.error('Error details:', JSON.stringify(error, null, 2));
    }
  }
</script>

<main>
  <h1>Welcome to the Argent Wallet App</h1>
  <div class="wallet">
    <h1>Argent Wallet</h1>
    <p><strong>Chain id:</strong> {chain_id}</p>
    <button on:click={connectWallet}>Connect Wallet</button>
    <div class="status">
      {#if isConnected}
        <span class="connected">🟢 Connected</span>
      {:else}
        <span class="disconnected">🔴 Not Connected</span>
      {/if}
    </div>
    <input type="text" bind:value={inputValue} placeholder="Enter value..." />
    <div class="actions">
      <button on:click={fetchBalance}>Check balance</button>
      <button on:click={addBalance}>Add balance</button>
    </div>
    <p><strong>Current contract balance:</strong> {currentBalance}</p>
  </div>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 800px;
    margin: 0 auto;
    color: #fcfcfc;
  }

  h1 {
    margin-bottom: 0.5em;
  }

  .wallet {
    font-family: Arial, sans-serif;
    padding: 20px;
    border: 2px solid #14d33e;
    border-radius: 10px;
    max-width: 400px;
    margin: 0 auto;
    text-align: center;
    background-color: #333;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  }

  button {
    padding: 10px 20px;
    font-size: 16px;
    margin-top: 20px;
    cursor: pointer;
    background-color: #818582;
    border: none;
    border-radius: 5px;
    color: white;
    transition: background-color 0.3s ease;
  }

  button:hover {
    background-color: #12c031;
  }

  .status {
    margin-top: 10px;
  }

  .connected {
    color: #12c031;
  }

  .disconnected {
    color: red;
  }

  input {
    padding: 10px;
    margin-top: 20px;
    border: 1px solid #ccc;
    border-radius: 5px;
    width: calc(100% - 22px); /* Full width minus padding and border */
  }

  .actions {
    display: flex;
    justify-content: space-between;
    margin-top: 20px;
  }

  .actions button {
    flex: 1;
    margin: 0 5px;
  }

  p {
    margin-top: 20px;
    font-size: 18px;
  }
</style>
