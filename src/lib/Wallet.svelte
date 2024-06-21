<script lang="ts">
    import { wallet } from "starknet";
    import { connect, disconnect } from "starknetkit"
    import { WebWalletConnector } from "starknetkit/webwallet"
    let isConnected = false;
    let node_url = 'Loading...';
    let chain_id = 'Loading...';

    async function connectWallet() {
    const { wallet } = await connect({
        connectors: [
            new WebWalletConnector({
            url: "https://web.argent.xyz",
            }),
        ],
    })
    if (wallet.isConnected) {
            isConnected = true;
            node_url = wallet.provider.channel.nodeUrl;
            chain_id = wallet.provider.channel.chainId;
        } else {

            chain_id = 'Failed to connect';
        }
        wallet.provider.channel.setChainId("0x534e5f5345504f4c4941");
        wallet.provider.channel.nodeUrl = "http://localhost:5050";

        chain_id = await wallet.provider.getChainId();
        node_url = await wallet.provider.channel.nodeUrl;
    }
</script>

<style>
    .wallet {
        font-family: Arial, sans-serif;
        padding: 20px;
        border: 1px solid #14d33e;
        border-radius: 8px;
        max-width: 400px;
        margin: 0 auto;
        text-align: center;
    }

    button {
        padding: 10px 20px;
        font-size: 16px;
        margin-top: 20px;
        cursor: pointer;
    }

    .status {
        margin-top: 10px;
    }

    .connected {
        color: green;
    }

    .disconnected {
        color: red;
    }
</style>

<div class="wallet">
    <h1>Argent Wallet</h1>
    <p><strong>Chain id:</strong> {chain_id}</p>
    <p><strong>Node url:</strong> {node_url}</p>
    <button on:click={connectWallet}>Connect Wallet</button>
    <div class="status">
        {#if isConnected}
            <span class="connected">🟢 Connected</span>
        {:else}
            <span class="disconnected">🔴 Not Connected</span>
        {/if}
    </div>
</div>