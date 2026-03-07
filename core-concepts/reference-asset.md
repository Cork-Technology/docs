---
hidden: true
---

# Reference Asset

{% hint style="info" %}
**TLDR:** Onchain Pegged Assets maintain a value relative to another reference, such as fiat currencies, staked cryptocurrencies, or real-world assets. These assets are essential for DeFi’s growth, providing new use cases and financial opportunities.
{% endhint %}

### Introduction

A **Pegged Asset** in DeFi is a token or digital asset designed to maintain a specific value relative to another reference—often a fiat currency, another cryptocurrency, or a basket of assets. The key idea is that the token’s value _follows_ or _mirrors_ the value of something else, known as its “peg.” If the peg is effectively held, holders expect the token’s price to stay consistent with the reference price or rate.

***

### Types of Pegged (or Correlated) Assets

#### 1. **Stablecoins**

* **Definition**: Stablecoins aim to keep their value pegged to a fiat currency like the US Dollar.
* **Mechanisms**:
  1. **Fiat-Collateralized**: Backed 1:1 by fiat reserves. Examples: USDC, USDT.
  2. **Crypto-Collateralized**: Overcollateralized by other cryptocurrencies. Example: DAI (backed by ETH and other assets).
  3. **Algorithmic**: Maintain peg through algorithmic controls, arbitrage incentives, and sometimes partial collateral. These can be more volatile.
* **Use Cases**: Price stability in DeFi trading pairs, lending platforms, and as a store of value.

#### 2. **Liquid Staking Tokens**

* **Definition**: Liquid staking tokens represent staked assets in a proof-of-stake (PoS) blockchain (e.g., ETH 2.0, Solana), while preserving the liquidity of the staked position.
* **Mechanisms**:
  * When users stake their tokens via a protocol (e.g., Lido, Rocket Pool), they receive a derivative token that tracks the staked asset’s value plus accrued staking rewards.
  * These derivative tokens can be traded or used in DeFi (lending, yield farming) without having to “unstake” directly from the blockchain protocol.
* **Pegged or Correlated?**: They are _correlated_ to the underlying staked asset (e.g., ETH), often at a 1:1 ratio plus staking yield. However, slight deviations can occur depending on market liquidity, protocol fees, or redemption constraints.

#### 3. **Liquid&#x20;**_**Re**_**staking Tokens**

* **Definition**: Liquid _r&#x65;_&#x73;taking builds on liquid staking by allowing the _already-staked_ asset or its staking derivative to be re-staked again, potentially compounding yields.
* **Mechanisms**:
  * Protocols like EigenLayer on Ethereum enable users to delegate staked ETH (or derivative tokens) to multiple services or security layers.
  * In return, they might receive a new derivative token that represents the re-staked position and associated rewards.
* **Pegged or Correlated?**: Similar to liquid staking tokens, these are correlated to the original staking token but may incorporate additional complexities or risks (e.g., slashing on multiple layers).

#### 4. **Real-World Assets (RWAs)**

* **Definition**: These are tokenized versions of traditionally “offchain” assets, such as real estate, bonds, invoices, or commodities. The token aims to track the value of the real-world asset it represents.
* **Mechanisms**:
  * Issuers typically lock legal rights to the underlying asset within a trusted entity or legal framework, and then mint tokens to represent fractionalized ownership or claims.
  * The peg or correlation depends on the issuer’s ability to maintain backing and redeemability (e.g., if each token corresponds to a share of a real-world bond, holders may be entitled to returns and principal redemption).
* **Use Cases**: Providing on-chain liquidity for traditionally illiquid real-world assets, enabling global, 24/7 markets and fractional ownership.

***

### Conclusion

In short, _pegged assets_ are foundational to many DeFi applications. From stablecoins that track fiat currencies, to derivatives that mirror staked tokens, to real-world assets represented onchain—all of these forms of “pegged” or “correlated” assets help expand the use cases around DeFi.
