---
description: Cork Swap Token (cST) — the tokenized risk instrument for pricing, hedging, and trading onchain risk
---

# Swap Token

{% hint style="info" %}
**TL;DR:** The Cork Swap Token (cST) is a tokenized risk instrument that allows market participants to price, hedge, and trade the risk associated with a Reference Asset.
{% endhint %}

### Introduction

Many assets in DeFi carry specific forms of risk — duration, illiquidity, credit, and price deviation risks. These risks affect numerous market participants and asset types, including institutional users, vaults, and asset issuers. The Cork Swap Token is a DeFi primitive that allows you to price, hedge, and trade such risks.



***

### How Swap Tokens Work

On Cork, every market pairs a **Reference Asset** (e.g., an RWA credit fund) with a Collateral Asset (e.g., sUSDS).

A **Swap Token** is a tokenized derivative that grants the holder the right, at any time before a set expiry date, to immediately exchange a specified **Reference Asset** plus the **Swap Token** for a **Collateral Asset** at pre-defined terms.

$$
Swap\ Token + Reference\ Asset = Collateral\ Asset
$$

***

### A Solution for Duration Risk

A major use case for Swap Tokens is to act as a liquidity facility to hedge against duration risk. Certain assets, such as RWAs with long redemption windows (e.g., 40+ days), are difficult to leverage in DeFi due to the lack of secondary liquidity, while the asset carries significant duration risk. To make such assets composable with lending markets, vaults, and other integrations that may require more immediate liquidity, a Cork Pool can be configured with the RWA as a Reference Asset and a liquid Collateral Asset. This means that the previously illiquid RWA can be instantly swapped to a liquid collateral through the use of the Swap Token. As a result, it can become composable with the rest of DeFi, solving the Duration Risk challenge of the RWA.&#x20;



***

### Applications of Swap Tokens

Swap Tokens can provide coverage for a wide range of risks and use cases including:

* **Looping**: Swap Tokens can be used in looping trades to shield against liquidation events and provide instant liquidity in particular to illiquid assets (e.g., Vault Tokens, RWAs)
* **Duration Risk:** In addition to RWAs, other market participants — including vaults and bridges that may face significant duration mismatches in their business — can leverage Cork to gain a more capital-efficient liquidity buffer to support smooth functioning in volatile market conditions.
* **Price deviation risk:** Temporary or permanent loss of value relative to the Collateral Asset can be hedged with Cork Swap Tokens.
* **Credit risk**: Instruments with credit risk, such as credit funds or certain stablecoins, can be hedged.

