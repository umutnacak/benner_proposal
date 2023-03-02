<script>
    import TonConnect from "@tonconnect/sdk";
    import QrCode from "svelte-qrcode";

    const connector = new TonConnect();
    let universalLink;
    let connectPromise = connect();

    async function connect() {
        connector.restoreConnection();

        const unsubscribe = connector.onStatusChange((walletInfo) => {
            console.log(walletInfo);
        });

        const walletsList = await connector.getWallets();
        return walletsList;
    }

    function connectTonKeeper() {
        const walletConnectionSource = {
            universalLink: "https://app.tonkeeper.com/ton-connect",
            bridgeUrl: "https://bridge.tonapi.io/bridge",
        };

        universalLink = connector.connect(walletConnectionSource);
    }

    function handleClick() {
        connectTonKeeper();
    }
</script>

{#await connectPromise}
    <p>Loading list...</p>
{:then walletList}
    <h4>Supported Wallets</h4>
    {#each walletList as wallet}
        <p>
            {wallet.name}
        </p>
    {/each}
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}

<p>
    <button on:click={handleClick}>Connect Tonkeeper</button>
</p>

{#if universalLink}
    <span>Please Scan the QR Code</span>
    <p>
        <QrCode value={universalLink} />
    </p>
{/if}
