---
description: How to Swap reference assets for collateral assets using Cork Swap Tokens
---

# Exercise Swaps

Cork [**Swap Tokens**](../../core-concepts/swap-token.md) provide protection against credit and duration risks.

Swap [**reference asset**](../../core-concepts/reference-asset.md) into [**collateral asset**](../../core-concepts/collateral-asset.md), by exercising your Cork [**Swap Token**](../../core-concepts/swap-token.md) (cST) against a [**Cork Pool**](../../core-concepts/cork-pool.md)**.**



This **"Swap /** **Exercise" operation** on Cork:

* Exercises an amount of your Cork [**Swap Token**](../../core-concepts/swap-token.md) (i.e. reduces the token's circulating supply)
* Releases an equivalent amount of [**Collateral Asset**](../../core-concepts/collateral-asset.md) from one of many [**Cork Pools**](../../core-concepts/cork-pool.md)
  * This amount is 1:1 as the Cork [**Swap Token**](../../core-concepts/swap-token.md) exercised, minus a nominal swap fee.
* Locks up "some fair amount" of your [**Reference Asset**](../../core-concepts/reference-asset.md) in exchange.
  * This amount is determined by the **Exchange or Swap Rate**, which is a dynamic rate based on the current relative valuation between the [**Reference Asset**](../../core-concepts/reference-asset.md) and [**Collateral Asset**](../../core-concepts/collateral-asset.md)**.**

***

## Swap Rate

The **Swap Rate** is a dynamic exchange rate that applies to each [**Cork Pool**](../../core-concepts/cork-pool.md)**,** based on the current relative valuation between its [**Reference Asset**](../../core-concepts/reference-asset.md) and [**Collateral Asset**](../../core-concepts/collateral-asset.md)**.** This dynamic **Swap Rate** is driven by a **Rate Oracle**, that ensures its liveliness.

* If either asset is backed by underlying assets, the **Rate Oracle** is configured to reference the value of the underlying assets as reported by each asset’s fundamental oracle, which is typically operated by the asset issuer.
* Price Feeds (market-based) operated by Chainlink Oracles, are then applied to determine the prevailing relative valuation between the [**Reference Asset**](../../core-concepts/reference-asset.md) and [**Collateral Asset**](../../core-concepts/collateral-asset.md) to obtain the incoming **Swap Rate**.
* As this **Swap Rate** can vary greatly, depending on what's reported by 3rd-party oracles operating outside of Cork's control, an upper and lower limit is applied by the Cork Protocol to ensure that the **Swap Rate** is range-bound or constrained.
* The upper limit (i.e. ceiling) is referred to as the **Max-Swap-Rate**, while the lower limit (i.e. floor) is referred to as the **Min-Swap-Rate**.

Learn more about rate limits [here](../../core-concepts/cork-pool.md#exchange-rate).

***

## How Swap Amounts are specified&#x20;

When exercising your cover, you may choose to specify either the:

* amount of [**Reference Asset**](../../core-concepts/reference-asset.md) to swap/spend, or the
* amount of [**Swap Token**](../../core-concepts/swap-token.md) to exercise/spend, or the
* net amount of [**Collateral Asset**](../../core-concepts/collateral-asset.md) to receive (after fees).

Either way, you will need to account for the **Variation Margin**, which is caused by the difference between the last observed **Swap Rate**, and the final **Swap Rate** during settlement (i.e. at the transaction confirmation block height).

Continue reading below to learn more, or jump straight to the individual guides (sub-pages) tailored to each scenario.

***

## Variation Margin

The **Swap Rate** of each [**Cork Pool**](../../core-concepts/cork-pool.md) is dynamic and driven by a **Rate Oracle.**

* If the [**Reference Asset**](../../core-concepts/reference-asset.md) has lost its relative value against the [**Collateral Asset**](../../core-concepts/collateral-asset.md), the **Swap Rate** decreases/falls.
  * As a result, exercising each [**Swap Token**](../../core-concepts/swap-token.md)—including those operations pending in the mempool—would consume or **spend a greater amount** of the [**Reference Asset**](../../core-concepts/reference-asset.md) per token exercised.
  * Conversely, due to the inverse relationship, swapping each [**Reference Asset**](../../core-concepts/reference-asset.md)—including those operations pending in the mempool—would consume or spend a smaller amount of the [**Swap Token**](../../core-concepts/swap-token.md), per asset exercised.
* If the [**Reference Asset**](../../core-concepts/reference-asset.md) has improved in its relative value against the [**Collateral Asset**](../../core-concepts/collateral-asset.md), the Swap Rate increases/rises.
  * As a result, swapping each [**Reference Asset**](../../core-concepts/reference-asset.md) —including those operations pending in the mempool—would consume or **spend a greater amount** of the Cork [**Swap Token**](../../core-concepts/swap-token.md), per asset swapped.&#x20;
  * Conversely, due to the inverse relationship, exercising each [**Swap Token**](../../core-concepts/swap-token.md)—including those operations pending in the mempool—would consume or spend a smaller amount of the [**Reference Asset**](../../core-concepts/reference-asset.md) per token exercised.

Changes in the **Swap Rate** between the time an operation is submitted (or at the last observed block height) and at the time it is settled (i.e., the transaction confirmation block height) can lead to **differences in the final settlement amounts**.

We refer to the difference between the **preview amount** and the actual **settlement amount** as the [**Variation Margin**](https://www.investopedia.com/terms/v/variationmargin.asp). Because this margin is determined by the prevailing Swap Rate at each transaction's execution block height, it is inherently influenced by each transaction's priority-gas fees and prevailing network conditions.

***

## How this matters

As there is a chance that a **greater spending amount** is required for each swap / exercise operation to succeed, users must provision an additional amount, called the **Spare Allowance**, within each submitted transaction.

{% hint style="info" %}
This **Spare Allowance Requirement** applies when submitting either via:

* a multisig wallet like safe.global
* an EoA wallet or MPC wallet
* a smart contract (including vaults and wrappers) while specifying a **fixed** amount.
{% endhint %}

<details>

<summary>The <strong>Spare Allowance</strong> amount recommended depends on each use-case:</summary>

* If you are trying to ensure that your swap [**Reference Asset**](../../core-concepts/reference-asset.md) operation is confirmed _within the near future_,
  * Take the **Current-Swap-Rate** minus the **Current-Rate-Change-Balance,** and multiply this by the amount of [**Reference Asset**](../../core-concepts/reference-asset.md)**,** to determine the total amount of [**Swap Token**](../../core-concepts/swap-token.md) that must be provisioned within each submitted transaction.
* If you are trying to ensure that your exercise [**Swap Token**](../../core-concepts/swap-token.md) operation is confirmed _within the near future_,
  * Take the amount of [**Swap Token**](../../core-concepts/swap-token.md)**,** and divide this by **Current-Swap-Rate** plus the **Current-Rate-Change-Balance,** to determine the total amount of [**Swap Token**](../../core-concepts/swap-token.md) that must be provisioned within each submitted transaction.
* If you are trying to fully hedge against a vault's position in the [**Reference Asset**](../../core-concepts/reference-asset.md)**,**
  * Take the **Min-Swap-Rate** and multiply this by the amount of [**Reference Asset**](../../core-concepts/reference-asset.md)**,** to determine the gross amount of [**Swap Token**](../../core-concepts/swap-token.md) that must be held.
  * To obtain a more precise net amount, you also have to consider the **Decaying Swap Fee** in the next section.
* If you are applying a partial hedge, meaning allowing a haircut on your vault's position in the [**Reference Asset**](../../core-concepts/reference-asset.md)**,** and **exercising swaps automatically by operating a bot,**
  * Take the **Current-Swap-Rate** minus the **Max-Rate-Change-Capacity,** and multiply this by the amount of [**Reference Asset**](../../core-concepts/reference-asset.md)**,** to determine the approximate worst-case swap value of your position, denominated in [**Collateral Asset**](../../core-concepts/collateral-asset.md).
  * To obtain a more precise amount, you also have to consider the **Decaying Swap Fee** in the next section.
* If you are applying a partial hedge, meaning allowing a haircut on your vault's position in the [**Reference Asset**](../../core-concepts/reference-asset.md)**, but exercising swaps manually,**
  * Take the **Current-Swap-Rate** minus the **Max-Rate-Change-Capacity,** minus **the Max-Rate-Change-Per-Day for every 24 hours of delay** (in exercising your swaps)**,** and multiply this by the amount of [**Reference Asset**](../../core-concepts/reference-asset.md)**,** to determine the approximate worst-case swap value of your position, denominated in [**Collateral Asset**](../../core-concepts/collateral-asset.md).
  * To obtain a more precise amount, you also have to consider the **Decaying Swap Fee** in the next section.

</details>



