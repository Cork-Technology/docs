---
description: Common questions about Cork Protocol — how it works, key mechanics, and getting started
---

# Frequently Asked Questions

## Protocol Mechanics

#### What is a Cork Pool?

A Cork Pool is the core primitive of Cork Protocol. It pairs a Collateral Asset (CA) with a Reference Asset (REF) and issues two tokens on deposit: Cork Principal Token (cPT) and Cork Swap Token (cST). See [Cork Pool](../core-concepts/cork-pool.md) for the full explanation.

#### What is the difference between cPT and cST?

**Cork Principal Token (cPT)** represents your principal claim on the pool. At expiry, you redeem cPT for your proportional share of pool assets. **Cork Swap Token (cST)** gives you the right to exercise — exchange REF + cST for CA before expiry. They are complementary: cPT is for underwriters earning yield, cST is for those seeking coverage.

#### Why do cPT and cST use 18 decimals regardless of the underlying token?

All Cork pool share tokens are standardized to 18 decimals for consistency. Even if the Collateral Asset uses different decimals (e.g., USDC with 6), the protocol normalizes internally. This simplifies integration and prevents decimal-mismatch errors.

#### What determines the price of a Cork Swap Token or Cork Principal Token?

Current Cork markets trade through OTC limit orders, where pricing is set between two counterparties and executed onchain. The prices of cST and cPT are inversely related — both add up to 1 Collateral Asset. If the premium (cST price) increases, the cPT price decreases proportionally.

#### What is the Exchange Rate?

The Exchange Rate determines how many Reference Assets correspond to each cST when exercising. It accounts for yield differentials between the CA and REF. For yield-bearing pairs (e.g., wstETH/ETH), the rate adjusts continuously to prevent yield from being arbitraged out of the pool at the expense of cPT holders.

#### What is the Premium?

The premium is the annualized cost that cST holders pay to cPT holders for coverage. For example, a 4% annual premium on a 3-month expiry would cost approximately 0.01 CA per cST (4% / 4 quarters).

#### What happens when a Cork Swap Token expires?

At expiry, cST can no longer be exercised and has no further value. The Cork Principal Token (cPT) can be used to redeem the remaining assets in the Cork Pool via the Redeem operation.

#### What is a MarketId?

A MarketId is the `keccak256` hash of all 8 fields in the Market struct. It uniquely identifies a Cork Pool. The same Market parameters always produce the same MarketId on any chain. See [CorkPoolManager](../developers/contract-reference/cork-pool-manager.md#marketid-computation) for computation details.

---

## Risk & Coverage

#### What is duration risk and how does Cork address it?

Certain assets — vault shares, RWAs, bridge tokens — have limited secondary liquidity and primarily rely on redemption mechanisms with long delays (sometimes 40+ days). This exposes holders to duration risk: the gap between when you request a redemption and when you receive your assets. Cork Pools address this by enabling holders to acquire cST, which provides instant exchange of the Reference Asset for a liquid Collateral Asset, bypassing the redemption queue entirely.

#### What happens if the Reference Asset loses value relative to the Collateral Asset?

If REF loses value relative to CA, cST holders can exercise their tokens to exchange REF for CA at favorable terms. The cST becomes more valuable as the gap widens. cPT holders bear this risk — at expiry, they receive a proportional share of pool assets, which may now include the impaired Reference Asset.

#### What happens if the Collateral Asset is impaired?

Both cPT holders and (to some extent) cST holders are exposed to the performance of the Collateral Asset. If the CA is impaired, cPT holders may face a loss when they redeem. cST holders can still exercise, but they receive an impaired asset, which reduces the effective value of the coverage.

#### What are the risks of holding Cork Principal Tokens?

If there is an impairment of either the Reference Asset or the Collateral Asset, the cPT holder bears the loss. If swaps are exercised, the pool's composition shifts from CA toward REF — meaning at expiry, cPT holders receive a mix of CA and REF rather than pure CA.

---

## Operations

#### What does "Unwind" mean?

Unwind means reversing a prior operation **before expiry**. **Unwind Deposit** returns cPT + cST to recover the original Collateral Asset. **Repurchase** (Unwind Exercise) returns CA to recover REF + cST from the pool. These operations are only available before the pool expires — after expiry, the only exit is Redeem.

#### What operations can I do before vs after expiry?

**Before expiry:** Deposit, Unwind Deposit, Exercise, Repurchase, Withdraw (exit cPT for proportional CA + REF). **After expiry:** Redeem only (same proportional exit, but uses the `redeem` contract function). In user-facing terms, both let cPT holders claim their share — the contract distinguishes them based on whether the pool has expired. See the [Operations Table](../developers/contract-reference/cork-pool-manager.md#operations-table) for a complete reference.

#### How do fees work?

Fees are charged on Exercise and Repurchase operations only. They use 18-decimal precision (`1e18 = 1%`) with a maximum of 5%. Fees are configured per pool by governance. See [Fees](../core-concepts/fees.md) for calculation details and examples.

---

## Getting Started

#### Where is Cork Protocol deployed?

Cork is live on Ethereum mainnet and Sepolia testnet. All contracts use deterministic CREATE2 deployment, so addresses are identical across chains. See [Phoenix Deployments](../smart-contracts/phoenix-deployments.md) for the full address list.

#### Where can I see my positions?

On the Cork dApp at [app.cork.tech](https://app.cork.tech), connect your wallet and go to the Dashboard section.

#### Who is the team behind Cork?

Cork Protocol is built by Cork Protocol Inc. (Delaware) and operated by Cork Labs (Cayman Islands). The founding team are serial entrepreneurs with prior DeFi experience. Cork is backed by a16z CSX, OrangeDAO, Road Capital, BitGo, G-20, and Steakhouse Financial. Learn more at [cork.tech/about](https://www.cork.tech/about/).

#### Who would use Cork?

Cork is primarily designed for institutional and protocol users — asset managers, vault curators, lending protocols, and risk managers who need programmable risk infrastructure for their onchain assets.
