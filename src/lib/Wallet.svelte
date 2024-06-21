<script>
    import { connect, disconnect } from 'starknetkit';
    import { WebWalletConnector } from "starknetkit/webwallet";
    import { onMount } from 'svelte';
    
    let public_key = 'Loading...';
    let isConnected = false;
    async function connectWallet() {
        const wallet = await connect({
            connectors: [
                new WebWalletConnector({
                    url: "https://web.argent.xyz",
                }),
            ],
            modalTheme: "light"
        });
        let account = await wallet.connector.account();
        console.log(wallet);
        if (wallet.wallet.isConnected) {
            isConnected = true;
        } else {
            public_key = 'Failed to connect';
        }
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
    <p><strong>Public key:</strong> {public_key}</p>
    <button on:click={connectWallet}>Connect Wallet</button>
    <div class="status">
        {#if isConnected}
            <span class="connected">🟢 Connected</span>
        {:else}
            <span class="disconnected">🔴 Not Connected</span>
        {/if}
    </div>
</div>