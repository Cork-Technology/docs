---
description: How fees work in Cork Pools — types, calculation, and configuration
---

# Fees

Cork Pools charge fees on two operations: **Exercise** and **Repurchase**. By default, fees flow to the Cork fee treasury. Governance can configure specific markets to redirect fees (partially or fully) to Cork Principal Token holders to adjust underwriting economics.

---

## Fee Types

| Fee | Applied To | When |
|---|---|---|
| Exercise Fee | Exercise (swap / exercise) | When exchanging Reference Asset + Cork Swap Token for Collateral Asset |
| Repurchase Fee | Repurchase (unwindSwap / unwindExercise) | When exchanging Collateral Asset for Reference Asset + Cork Swap Token |

Fees are **not** charged on Deposit, Unwind Deposit, Withdraw, or Redeem operations.

---

## How Fees Are Calculated

Fees use a **custom encoding** where `1e18 = 1%` (one percentage point). This differs from standard 18-decimal fixed-point where `1e18` would represent 100%. The fee is applied to the Collateral Asset amount involved in the operation:

```
feeAmount = amount × feePercentage / 100e18
```

**Example:** If the Exercise fee is `1e18` (1%) and you exercise for 100 Collateral Asset:

```
feeAmount = 100 × 1e18 / 100e18 = 1 Collateral Asset
```

You receive 99 Collateral Asset after the fee.

{% hint style="info" %}
Fees are rounded up (ceiling) to protect the pool. The actual fee may be fractionally higher than the formula suggests for non-round amounts.
{% endhint %}

---

## Fee Limits

The maximum allowed fee for either type is `5e18` (**5%**). Fees are configured individually for each Cork Pool at creation and can be updated by governance (DefaultCorkController) within this maximum.

---

## Querying Fees

Read the current fee for any pool on CorkPoolManager. The `MarketId` type is a `bytes32` hash that uniquely identifies a pool — see [MarketId Computation](../developers/contract-reference/cork-pool-manager.md#marketid-computation) for details.

```solidity
function swapFee(MarketId poolId) external view returns (uint256 fees)
function unwindSwapFee(MarketId poolId) external view returns (uint256 fees)
```

The returned value uses Cork's fee encoding (`1e18 = 1%`).

{% hint style="info" %}
The API returns fees as decimal fractions (e.g., `0.01` = 1%). On-chain, the same 1% fee is represented as `1e18` (1000000000000000000). See [API Reference](../developers/api-reference.md) for details.
{% endhint %}

---

## Fee Configuration

Fees are set per pool at creation via the `PoolCreationParams` struct and can be updated by governance:

```solidity
function updateSwapFeePercentage(MarketId id, uint256 newSwapFeePercentage) external
function updateUnwindSwapFeePercentage(MarketId id, uint256 newUnwindSwapFeePercentage) external
```

See [Admin & Governance](../developers/contract-reference/admin-and-governance.md) for the full governance interface.
