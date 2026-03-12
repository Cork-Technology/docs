---
description: How fees work in Cork Pools — types, calculation, and configuration
---

# Fees

Cork Pools charge fees on two operations: **Exercise** and **Repurchase**. All fees flow to the Cork fee treasury. In some market configurations, fees may be partially or fully redirected to Cork Principal Token (cPT) holders to adjust underwriting economics.

---

## Fee Types

| Fee | Applied To | When |
|---|---|---|
| Exercise Fee | Exercise (swap / exercise) | When exchanging REF + cST for CA |
| Repurchase Fee | Repurchase (unwindSwap / unwindExercise) | When exchanging CA for REF + cST |

Fees are **not** charged on Deposit, Unwind Deposit, Withdraw, or Redeem operations.

---

## How Fees Are Calculated

Fees use **18-decimal precision** where `1e18 = 1%`. The fee is applied to the Collateral Asset amount involved in the operation:

```
feeAmount = amount × feePercentage / 100e18
```

**Example:** If the Exercise fee is `1e18` (1%) and you exercise for 100 CA:

```
feeAmount = 100 × 1e18 / 100e18 = 1 CA
```

You receive 99 CA after the fee.

{% hint style="info" %}
Fees are rounded up (ceiling) to protect the pool. The actual fee may be fractionally higher than the formula suggests for non-round amounts.
{% endhint %}

---

## Fee Limits

The maximum allowed fee for either type is `5e18` (**5%**). Fees are configured individually for each Cork Pool at creation and can be updated by governance (DefaultCorkController) within this maximum.

---

## Querying Fees

Read the current fee for any pool on CorkPoolManager:

```solidity
function swapFee(MarketId poolId) external view returns (uint256 fees)
function unwindSwapFee(MarketId poolId) external view returns (uint256 fees)
```

The returned value uses 18-decimal precision (`1e18 = 1%`).

{% hint style="info" %}
The API returns fees as decimal fractions (e.g., `0.01` = 1%). On-chain, the same fee is represented as `1e18`. See [API Reference](../developers/api-reference.md) for details.
{% endhint %}

---

## Fee Configuration

Fees are set per pool at creation via the `PoolCreationParams` struct and can be updated by governance:

```solidity
function updateSwapFeePercentage(MarketId id, uint256 newSwapFeePercentage) external
function updateUnwindSwapFeePercentage(MarketId id, uint256 newUnwindSwapFeePercentage) external
```

See [Admin & Governance](../developers/contract-reference/admin-and-governance.md) for the full governance interface.
