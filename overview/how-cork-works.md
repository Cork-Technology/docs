---
hidden: true
---

# How Cork works

### How Cork Works

Core to the Cork protocol is the [**Peg Stability Module**](../core-concepts/cork-pool.md) and its [**Depeg Swap**](../core-concepts/swap-token.md), a novel asset that prices the risk of a [depeg](https://coinmarketcap.com/academy/glossary/depeg) event, empowering crypto traders and funds to manage pegged-asset risk.

Each Cork market is structured as a **token pair** consisting of a [**redemption asset**](../core-concepts/collateral-asset.md) and a [**pegged asset**](../core-concepts/reference-asset.md). For example, in the `ETH:stETH` market:

* The **redemption asset** is `ETH`
* The **pegged asset** is `stETH`

Each Cork market operates with **fixed-duration terms** and is powered by the [**Peg Stability Module**](../core-concepts/cork-pool.md), which:

* Mints [**Depeg Swaps**](../core-concepts/swap-token.md) and [**Cover Tokens**](../core-concepts/principal-token.md) with a set time to expiry
* Runs an [**Automated Market Maker**](../core-concepts/trade-cpts-and-csts.md) on **Uniswap v4** to determine pricing

Additionally, each market has a [**Cork Vault**](/broken/pages/I8JgHXnahZZTsSQtAobZ), which automates liquidity deployment across consecutive terms. Traders can either buy a [**Depeg Swap**](../core-concepts/swap-token.md) to hedge against a depeg or buy a [**Cover Token**](../core-concepts/principal-token.md) to earn a fixed yield as long as no depeg occurs.

***

### Getting Started with Cork

You’ll find more information here on how to:

✅ [**Buy Depeg Swaps**](../user-guides/buy-and-sell-cover.md) to hedge against a [depeg](https://coinmarketcap.com/academy/glossary/depeg) event

✅ [**Buy Cover Tokens**](../user-guides/provide-liquidity/) to earn a fixed yield (as long as no [depeg](https://coinmarketcap.com/academy/glossary/depeg) occurs)

✅ [**Dive deeper**](/broken/pages/RW09s5q8xXivuy6JpE0g) into how the protocol works and the [FAQ section](../faq/frequently-asked-questions.md)

✅ [**Explore the Cork dApp**](/broken/pages/lgnUogPjD1439TUkXABE) and its features

✅ [**Integrate with the Cork Protocol**](/broken/pages/3tMBsnoDkJP9zhJ1zPaB) and start building



#### **Don’t get screwed—get Cork!** 🚀
