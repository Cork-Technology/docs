---
description: How to Mint Swap Token and Principal Token to earn a premium or yield
---

# Mint Tokens

Provide cover against credit and duration risks, by depositing [**collateral assets**](../../core-concepts/collateral-asset.md) into a [**Cork Pool**](../../core-concepts/cork-pool.md) to mint shares of Cork [**Principal Token**](../../core-concepts/principal-token.md) (cPT) and Cork [**Swap Token**](../../core-concepts/swap-token.md) (cST).

This **"Mint" operation** on Cork:

* deposits [**Collateral Asset**](../../core-concepts/collateral-asset.md) into one of many [**Cork Pools**](../../core-concepts/cork-pool.md)
* mints share tokens (i.e. increases circulating supply of cPT & cST tokens of that Cork Pool)

***

## How it works

At maturity, [**Principal Tokens**](../../core-concepts/principal-token.md) can be redeemed for underlying assets — typically the [**Collateral Asset**](../../core-concepts/collateral-asset.md), or alternatively some [**Reference Asset**](../../core-concepts/reference-asset.md), **only if** [**Cork Swap Token**](../../core-concepts/swap-token.md) holders have exercised their cover (and these are not repurchased _by arbitragers before the Cork_ [_Swap Token_](../../core-concepts/swap-token.md)_'s expiry date)._

This mechanism is how [**Principal Token**](../../core-concepts/principal-token.md) holders underwrite duration and credit risks.

{% hint style="info" %}
Cork [**Principal Tokens**](../../core-concepts/principal-token.md) are expected to start at a **discounted price** the moment they are minted, and gradually move toward the value of **1** [**Collateral Asset**](../../core-concepts/collateral-asset.md) as they approach maturity — _as long as Cork_ [_Swap Tokens_](../../core-concepts/swap-token.md) _are not exercised (or exercised tokens are fully repurchased by arbitragers before the Cork_ [_Swap Token_](../../core-concepts/swap-token.md)_'s expiry date)._
{% endhint %}

***

## How to obtain Cork Tokens

You may choose to **mint Cork** [**Principal Tokens**](../../core-concepts/principal-token.md) **&** [**Swap Tokens**](../../core-concepts/swap-token.md)**,** or you may choose to **purchase them from a resale market** (such as Airswap OTC):

* **If minting principal tokens:**
  * You deposit and lock up one [Collateral Asset](../../core-concepts/collateral-asset.md) for each Cork [Principal Token](../../core-concepts/principal-token.md) to be minted. As part of this process, you receive an equivalent amount of Cork [Swap Token](../../core-concepts/swap-token.md).
  * This lock applies until the expiry of the [Cork Pool](../../core-concepts/cork-pool.md) (which coincides with the expiry of the Cork [Swap Token](../../core-concepts/swap-token.md), and maturity of the Cork [Principal Token](../../core-concepts/principal-token.md)).
    * To redeem the Cork [Principal Token](../../core-concepts/principal-token.md) early, you would have to obtain and burn the equivalent amount of Cork [Swap Token](../../core-concepts/swap-token.md) too, in order to unlock and withdraw collateral assets early. This is called an **"Unwind" operation**.
  * Your "yield" or premium comes from the sale of your minted Cork [Swap Tokens](../../core-concepts/swap-token.md) on a resale market (such as Airswap).
* **If buying principal tokens:**
  * Before maturity, Cork [**Principal Tokens**](../../core-concepts/principal-token.md) are sold at a discount (from their expected value at maturity).
  * At maturity, Cork [**Principal Tokens**](../../core-concepts/principal-token.md) can be redeemed for an equivalent amount of [**Collateral Asset**](../../core-concepts/collateral-asset.md) (or [**Reference Asset**](../../core-concepts/reference-asset.md)) from the associated [**Cork Pool**](../../core-concepts/cork-pool.md).
  * Increased demand for Cork [**Principal Tokens**](../../core-concepts/principal-token.md) on the resale market can drive up their price, reducing the discount (on your purchase) and resulting in a lower implied fixed yield rate to maturity (YTM).
  * Increased supply of Cork [**Principal Tokens**](../../core-concepts/principal-token.md) on the resale market can lower their price, increasing the discount (on your purchase) and resulting in a higher implied fixed yield rate to maturity (YTM).

***

## Mint Both Principal & Swap Token

🎥 **Watch the video below** to learn how to use the dApp to **mint both principal & swap tokens** and **resell the swap tokens** (on Airswap) to earn a premium:

Coming Soon

***

## Buy Principal Token only

🎥 **Watch the video below** to learn how to use the dApp to **buy principal tokens** (on Airswap) at a discount, to earn an implied fixed yield:

Coming Soon
