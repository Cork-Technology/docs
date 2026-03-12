---
description: Common questions about Cork Protocol — how it works, key mechanics, and getting started
---

# Frequently Asked Questions

## Protocol Mechanics

#### What is a Cork Pool?

A Cork Pool is the core primitive of Cork Protocol. It pairs a Collateral Asset with a Reference Asset and issues two tokens on deposit: Cork Principal Token and Cork Swap Token. See [Cork Pool](../core-concepts/cork-pool.md) for the full explanation.

#### What is the difference between a Cork Principal Token and a Cork Swap Token?

**Cork Principal Token** represents your principal claim on the pool. At expiry, you redeem Cork Principal Tokens for your proportional share of pool assets. **Cork Swap Token** gives you the right to exercise — exchange Reference Asset + Cork Swap Token for Collateral Asset before expiry. They are complementary: Cork Principal Tokens are for underwriters earning yield, Cork Swap Tokens are for those seeking coverage.

#### Why do Cork Principal Tokens and Cork Swap Tokens use 18 decimals regardless of the underlying token?

All Cork pool share tokens are standardized to 18 decimals for consistency. Even if the Collateral Asset uses different decimals (e.g., USDC with 6), the protocol normalizes internally. This simplifies integration and prevents decimal-mismatch errors.

#### What determines the price of a Cork Swap Token or Cork Principal Token?

Current Cork markets trade through OTC limit orders, where pricing is set between two counterparties and executed onchain. The prices of Cork Swap Tokens and Cork Principal Tokens are inversely related — both add up to 1 Collateral Asset. If the premium (Cork Swap Token price) increases, the Cork Principal Token price decreases proportionally.

#### What is the Exchange Rate?

The Exchange Rate determines how many Reference Assets correspond to each Cork Swap Token when exercising. It accounts for yield differentials between the Collateral Asset and Reference Asset. For yield-bearing pairs (e.g., wstETH/ETH), the rate adjusts continuously to prevent yield from being arbitraged out of the pool at the expense of Cork Principal Token holders.

#### What is the Premium?

The premium is the annualized cost that Cork Swap Token holders pay to Cork Principal Token holders for coverage. For example, a 4% annual premium on a 3-month expiry would cost approximately 0.01 Collateral Asset per Cork Swap Token (4% / 4 quarters).

#### What happens when a Cork Swap Token expires?

At expiry, the Cork Swap Token can no longer be exercised and has no further value. The Cork Principal Token can be used to redeem the remaining assets in the Cork Pool via the Redeem operation.

#### What is a MarketId?

A MarketId is the `keccak256` hash of all 8 fields in the Market struct. It uniquely identifies a Cork Pool. The same Market parameters always produce the same MarketId on any chain. See [CorkPoolManager](../developers/contract-reference/cork-pool-manager.md#marketid-computation) for computation details.

---

## Risk & Coverage

#### What is a price deviation event?

A price deviation event occurs when a pegged asset — such as a stablecoin or liquid staking token — trades below its intended value due to market volatility, liquidity issues, or external shocks. Some deviations are temporary (e.g., USDC during the SVB collapse), while others are permanent (e.g., UST). Cork Swap Tokens allow holders to hedge against such events by guaranteeing exchange of the Reference Asset for the Collateral Asset regardless of market conditions.

#### What is duration risk and how does Cork address it?

Certain assets — vault shares, RWAs, bridge tokens — have limited secondary liquidity and primarily rely on redemption mechanisms with long delays (sometimes 40+ days). This exposes holders to duration risk: the gap between when you request a redemption and when you receive your assets. Cork Pools address this by enabling holders to acquire Cork Swap Tokens, which provide instant exchange of the Reference Asset for a liquid Collateral Asset, bypassing the redemption queue entirely.

#### What happens if the Reference Asset loses value relative to the Collateral Asset?

If the Reference Asset loses value relative to the Collateral Asset, Cork Swap Token holders can exercise their tokens to exchange Reference Asset for Collateral Asset at favorable terms. The Cork Swap Token becomes more valuable as the gap widens. Cork Principal Token holders bear this risk — at expiry, they receive a proportional share of pool assets, which may now include the impaired Reference Asset.

#### What happens if the Collateral Asset is impaired?

Both Cork Principal Token holders and (to some extent) Cork Swap Token holders are exposed to the performance of the Collateral Asset. If the Collateral Asset is impaired, Cork Principal Token holders may face a loss when they redeem. Cork Swap Token holders can still exercise, but they receive an impaired asset, which reduces the effective value of the coverage.

#### What are the risks of holding Cork Principal Tokens?

If there is an impairment of either the Reference Asset or the Collateral Asset, the Cork Principal Token holder bears the loss. If swaps are exercised, the pool's composition shifts from Collateral Asset toward Reference Asset — meaning at expiry, Cork Principal Token holders receive a mix of Collateral Asset and Reference Asset rather than pure Collateral Asset.

---

## Operations

#### What does "Unwind" mean?

Unwind means reversing a prior operation **before expiry**. **Unwind Deposit** returns Cork Principal Token + Cork Swap Token to recover the original Collateral Asset. **Repurchase** (Unwind Exercise) returns Collateral Asset to recover Reference Asset + Cork Swap Token from the pool. These operations are only available before the pool expires — after expiry, the only exit is Redeem.

#### What operations can I do before vs after expiry?

**Before expiry:** Deposit, Unwind Deposit, Exercise, Repurchase, Withdraw (exit Cork Principal Tokens for proportional Collateral Asset + Reference Asset). **After expiry:** Redeem only (same proportional exit, but uses the `redeem` contract function). In user-facing terms, both let Cork Principal Token holders claim their share — the contract distinguishes them based on whether the pool has expired. See the [Operations Table](../developers/contract-reference/cork-pool-manager.md#operations-table) for a complete reference.

#### How do fees work?

Fees are charged on Exercise and Repurchase operations only. They use 18-decimal precision (`1e18 = 1%`) with a maximum of 5%. Fees are configured per pool by governance. See [Fees](../core-concepts/fees.md) for calculation details and examples.

---

## About Cork

#### Why are you building Cork?

For DeFi to become the backbone of finance, trusted by large-scale institutions, it needs risk management infrastructure. We are building Cork to enable the creation of risk markets to help accelerate the adoption of DeFi.

#### Who is the team behind Cork?

Cork Protocol is built by Cork Protocol Inc. (Delaware) and operated by Cork Labs (Cayman Islands). The founding team are serial entrepreneurs with prior DeFi experience. Cork is backed by a16z CSX, OrangeDAO, Road Capital, BitGo, G-20, and Steakhouse Financial. Learn more at [cork.tech/about](https://www.cork.tech/about/).

#### Who would use Cork?

Cork is primarily designed for institutional and protocol users — asset managers, vault curators, lending protocols, and risk managers who need programmable risk infrastructure for their onchain assets.

#### Where is Cork Protocol deployed?

Cork is live on Ethereum mainnet and Sepolia testnet. All contracts use deterministic CREATE2 deployment, so addresses are identical across chains. See [Phoenix Deployments](../smart-contracts/phoenix-deployments.md) for the full address list.

#### Where can I see my positions?

On the Cork dApp at [app.cork.tech](https://app.cork.tech), connect your wallet and go to the Dashboard section.
