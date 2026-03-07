# Reference Asset

{% hint style="info" %}
**TL;DR:** The Reference Asset (REF) is the asset whose risk is being priced and hedged in a Cork Pool. It is the asset that Cork Swap Token holders deposit alongside their cST when exercising the Swap operation.
{% endhint %}

### Introduction

Every Cork Pool pairs a [**Collateral Asset**](collateral-asset.md) with a **Reference Asset**. The Reference Asset is the asset whose liquidity, duration, or credit risk a market participant wants to manage. It could be a vault token, a liquid staking token, a stablecoin, or a tokenized real-world asset.

The Reference Asset is not deposited to create positions — that role belongs to the Collateral Asset. Instead, the Reference Asset enters the pool only when a Cork Swap Token is exercised.

***

### How It Works

Cork Pools need to identify which asset's risk is being priced and hedged. The Reference Asset fills this role — it is the target of the Swap mechanism.

$$
Cork\ Swap\ Token + Reference\ Asset \rightarrow Collateral\ Asset
$$

When a cST holder exercises via the Swap operation, they deposit the Reference Asset and transfer their cST into the Cork Pool. In return, they receive the Collateral Asset (minus an exercise fee). The cST is held by the pool and may be returned to arbitrageurs through the Repurchase mechanism. The Reference Asset remains in the pool until expiry, when it is distributed proportionally to Cork Principal Token holders alongside any remaining Collateral Asset.

If no swaps are exercised, the pool holds only Collateral Asset at expiry.

***

### Key Properties

- **Not deposited on pool creation** — The Collateral Asset funds the pool. The Reference Asset enters only through exercised swaps.
- **Defines the risk being priced** — The cST price reflects the market's assessment of the Reference Asset's risk relative to the Collateral Asset. If the Reference Asset is perceived as risky, cST becomes more valuable.
- **Exchange Rate adjusted** — A rate oracle and constraint parameters (min, max, rate-of-change limits) govern the exchange rate between the Reference Asset and Collateral Asset. For yield-bearing pairs, this rate adjusts to account for yield differentials, preventing arbitrage at the expense of cPT holders.
- **Recoverable post-exercise** — Once Reference Asset enters the pool through exercised swaps, it can be recovered before expiry through the Repurchase mechanism, or it remains for cPT holders to redeem at expiry via Withdraw.

***

### Types of Reference Assets

Cork Pools can be configured for a wide range of Reference Assets:

**Vault Tokens** — ERC-4626 vault shares, bridge vault tokens, or yield aggregator tokens. These often have limited redemption liquidity or high utilization rates, making instant exits difficult. A Cork Pool provides guaranteed liquidity through the Collateral Asset.

**Liquid Staking & Restaking Tokens** — Tokens like wstETH, ezETH, or other LST/LRT derivatives that track an underlying staked asset. While generally correlated to the base asset, they can deviate during periods of market stress or slashing events.

**Stablecoins** — Fiat-pegged tokens (USDC, USDT, DAI) or algorithmic stablecoins where depeg risk exists. Cork Pools allow holders to hedge against temporary or permanent loss of peg.

**Real-World Assets (RWAs)** — Tokenized bonds, credit funds, or other traditional financial instruments brought onchain. These often carry duration risk (long redemption windows of 40+ days), making them difficult to use as collateral in DeFi without a liquidity facility like Cork.

***

### Relationship to Other Components

- **CorkPoolManager** — Tracks Reference Asset balances per pool. The Reference Asset enters through the Swap operation and exits through Repurchase or Withdraw.
- **Cork Swap Token (cST)** — The cST enables exchange of REF for CA. Without the Reference Asset, the cST has no exercise target.
- **Cork Principal Token (cPT)** — At expiry, cPT holders receive their proportional share of both the remaining Collateral Asset and any Reference Asset in the pool.
- **Exchange Rate** — Determines how many Reference Asset units correspond to each cST when exercising. The rate oracle provides fundamental pricing, while constraint parameters protect against manipulation.

***

### Example

Consider a Cork Pool with **sUSDS** as the Collateral Asset and a **tokenized credit fund** (RWA) as the Reference Asset. The credit fund has a 45-day redemption window.

1. A vault manager holds 1,000 units of the credit fund but needs the ability to exit instantly if required by depositors.
2. They purchase **1,000 cST** from the market, paying a premium to a liquidity provider.
3. If the credit fund enters a liquidity crisis (e.g., redemption queue grows, secondary market dries up), the vault manager exercises via the Swap operation: they deposit credit fund tokens alongside their 1,000 cST (the Reference Asset amount determined by the current exchange rate) and receive the equivalent value in sUSDS immediately, minus the exercise fee.
4. The credit fund tokens now sit in the Cork Pool. Arbitrageurs may Repurchase them if secondary market conditions improve. Otherwise, they are distributed to cPT holders at expiry via Withdraw.

The Reference Asset (credit fund token) is the risk being managed. The Cork Pool transforms an illiquid, duration-locked asset into something with guaranteed instant liquidity.
