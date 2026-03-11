---
description: Singleton pool manager — all pool state, queries, previews, and raw operations
---

# CorkPoolManager

**Address:** `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC` (same on all chains)

CorkPoolManager is the singleton contract that holds all Cork Pool state and executes all pool operations. Use it directly for read operations. For write operations, use [CorkAdapter](cork-adapter.md) which adds slippage and deadline protection.

## Query Functions

These are the primary read functions for integrators.

### swapRate

```solidity
function swapRate(MarketId poolId) external view returns (uint256 rate)
```

Returns the exchange rate between Reference Asset and Collateral Asset, scaled to 18 decimals.

**Interpretation:** 1 Reference Asset = `rate / 1e18` Collateral Assets.

Example: a rate of `0.82e18` (820000000000000000) means 1 Reference Asset is worth 0.82 Collateral Assets.

```typescript
const rate = await client.readContract({
  address: CORK_POOL_MANAGER,
  abi: poolManagerAbi,
  functionName: 'swapRate',
  args: [MARKET_ID],
})
// rate = 820000000000000000n → 1 REF = 0.82 CA
```

### assets

```solidity
function assets(MarketId poolId) external view returns (uint256 collateralAssets, uint256 referenceAssets)
```

Returns the pool's Collateral Asset and Reference Asset balances in their native token decimals.

```typescript
const [collateral, reference] = await client.readContract({
  address: CORK_POOL_MANAGER,
  abi: poolManagerAbi,
  functionName: 'assets',
  args: [MARKET_ID],
})
```

### shares

```solidity
function shares(MarketId poolId) external view returns (address principalToken, address swapToken)
```

Returns the Cork Principal Token (cPT) and Cork Swap Token (cST) contract addresses for a given market.

### market

```solidity
function market(MarketId poolId) external view returns (Market memory)
```

Returns the full Market configuration struct (all 8 fields). See [Key Concepts](#key-concepts) below.

### getId

```solidity
function getId(Market calldata marketParameters) external view returns (MarketId marketId)
```

Computes a MarketId from a Market struct. Useful for on-chain MarketId derivation.

---

## Preview Functions

Preview functions return the expected output of an operation without executing it. Use them for UI quotes, pre-flight checks, and computing slippage parameters for CorkAdapter.

{% hint style="info" %}
Preview functions return `0` (or all zeros for multi-value returns) if the operation is currently paused or if the pool has expired (for pre-expiry operations).
{% endhint %}

### Deposit Previews

```solidity
function previewDeposit(MarketId poolId, uint256 collateralAssetsIn)
    external view returns (uint256 cptAndCstSharesOut)

function previewMint(MarketId poolId, uint256 cptAndCstSharesOut)
    external view returns (uint256 collateralAssetsIn)
```

### Unwind Deposit Previews

```solidity
function previewUnwindDeposit(MarketId poolId, uint256 collateralAssetsOut)
    external view returns (uint256 cptAndCstSharesIn)

function previewUnwindMint(MarketId poolId, uint256 cptAndCstSharesIn)
    external view returns (uint256 collateralAssetsOut)
```

### Withdraw Previews

```solidity
function previewWithdraw(MarketId poolId, uint256 collateralAssetsOut)
    external view returns (uint256 cptSharesIn, uint256 actualCollateralAssetsOut, uint256 actualReferenceAssetsOut)

function previewWithdrawOther(MarketId poolId, uint256 referenceAssetsOut)
    external view returns (uint256 cptSharesIn, uint256 actualCollateralAssetsOut, uint256 actualReferenceAssetsOut)
```

### Redeem Preview

```solidity
function previewRedeem(MarketId poolId, uint256 cptSharesIn)
    external view returns (uint256 referenceAssetsOut, uint256 collateralAssetsOut)
```

### Exercise Previews

```solidity
function previewExercise(MarketId poolId, uint256 cstSharesIn)
    external view returns (uint256 collateralAssetsOut, uint256 referenceAssetsIn, uint256 fee)

function previewExerciseOther(MarketId poolId, uint256 referenceAssetsIn)
    external view returns (uint256 collateralAssetsOut, uint256 cstSharesIn, uint256 fee)

function previewSwap(MarketId poolId, uint256 collateralAssetsOut)
    external view returns (uint256 cstSharesIn, uint256 referenceAssetsIn, uint256 fee)
```

### Unwind Exercise / Swap Previews

```solidity
function previewUnwindSwap(MarketId poolId, uint256 collateralAssetsIn)
    external view returns (uint256 cstSharesOut, uint256 referenceAssetsOut, uint256 fee)

function previewUnwindExercise(MarketId poolId, uint256 cstSharesOut)
    external view returns (uint256 collateralAssetsIn, uint256 referenceAssetsOut, uint256 fee)

function previewUnwindExerciseOther(MarketId poolId, uint256 referenceAssetsOut)
    external view returns (uint256 collateralAssetsIn, uint256 cstSharesOut, uint256 fee)
```

---

## Max Functions

Returns the maximum amount the given address can use for each operation. Accounts for token balances, allowances, and pool liquidity.

```solidity
// Deposit
function maxDeposit(MarketId poolId, address owner) external view returns (uint256)
function maxMint(MarketId poolId, address owner) external view returns (uint256)

// Unwind Deposit
function maxUnwindDeposit(MarketId poolId, address owner) external view returns (uint256)
function maxUnwindMint(MarketId poolId, address owner) external view returns (uint256)

// Withdraw
function maxWithdraw(MarketId poolId, address owner) external view returns (uint256)
function maxWithdrawOther(MarketId poolId, address owner) external view returns (uint256)

// Redeem
function maxRedeem(MarketId poolId, address owner) external view returns (uint256)

// Exercise / Swap
function maxExercise(MarketId poolId, address owner) external view returns (uint256)
function maxExerciseOther(MarketId poolId, address owner) external view returns (uint256)
function maxSwap(MarketId poolId, address owner) external view returns (uint256)

// Unwind Exercise / Swap
function maxUnwindSwap(MarketId poolId, address) external view returns (uint256)
function maxUnwindExercise(MarketId poolId, address) external view returns (uint256)
function maxUnwindExerciseOther(MarketId poolId, address) external view returns (uint256)
```

---

## Fee Functions

```solidity
function swapFee(MarketId poolId) external view returns (uint256 fees)
function unwindSwapFee(MarketId poolId) external view returns (uint256 fees)
```

Fees use 18-decimal precision: `1e18 = 1%`. The maximum allowed fee is `5e18` (5%). Fees apply to exercise/swap and unwind exercise/swap operations.

---

## Raw Operation Functions

CorkPoolManager exposes all pool operations directly. These functions execute the operation without slippage or deadline checks.

{% hint style="warning" %}
These functions lack slippage and deadline protection. For production integrations, use [CorkAdapter](cork-adapter.md) safe wrappers instead.
{% endhint %}

```solidity
// Deposit
function deposit(MarketId poolId, uint256 collateralAssetsIn, address receiver)
    external returns (uint256 cptAndCstSharesOut)
function mint(MarketId poolId, uint256 cptAndCstSharesOut, address receiver)
    external returns (uint256 collateralAssetsIn)

// Unwind Deposit
function unwindDeposit(MarketId poolId, uint256 collateralAssetsOut, address owner, address receiver)
    external returns (uint256 cptAndCstSharesIn)
function unwindMint(MarketId poolId, uint256 cptAndCstSharesIn, address owner, address receiver)
    external returns (uint256 collateralAssetsOut)

// Withdraw
function withdraw(MarketId poolId, uint256 collateralAssetsOut, address owner, address receiver)
    external returns (uint256 cptSharesIn, uint256 actualCollateralAssetsOut, uint256 actualReferenceAssetsOut)
function withdrawOther(MarketId poolId, uint256 referenceAssetsOut, address owner, address receiver)
    external returns (uint256 cptSharesIn, uint256 actualCollateralAssetsOut, uint256 actualReferenceAssetsOut)

// Redeem (post-expiry)
function redeem(MarketId poolId, uint256 cptSharesIn, address owner, address receiver)
    external returns (uint256 referenceAssetsOut, uint256 collateralAssetsOut)

// Exercise / Swap
function exercise(MarketId poolId, uint256 cstSharesIn, address receiver)
    external returns (uint256 collateralAssetsOut, uint256 referenceAssetsIn, uint256 fee)
function exerciseOther(MarketId poolId, uint256 referenceAssetsIn, address receiver)
    external returns (uint256 collateralAssetsOut, uint256 cstSharesIn, uint256 fee)
function swap(MarketId poolId, uint256 collateralAssetsOut, address receiver)
    external returns (uint256 cstSharesIn, uint256 referenceAssetsIn, uint256 fee)

// Unwind Exercise / Swap
function unwindSwap(MarketId poolId, uint256 collateralAssetsIn, address receiver)
    external returns (uint256 cstSharesOut, uint256 referenceAssetsOut, uint256 fee)
function unwindExercise(MarketId poolId, uint256 cstSharesOut, address receiver)
    external returns (uint256 collateralAssetsIn, uint256 referenceAssetsOut, uint256 fee)
function unwindExerciseOther(MarketId poolId, uint256 referenceAssetsOut, address receiver)
    external returns (uint256 collateralAssetsIn, uint256 cstSharesOut, uint256 fee)
```

---

## Key Concepts

### Market Struct

Every Cork Pool is defined by a Market struct containing 8 fields:

| Field | Type | Description |
|---|---|---|
| `collateralAsset` | `address` | The yield-bearing asset deposited into the pool (e.g., sUSDe) |
| `referenceAsset` | `address` | The asset whose risk is being hedged (e.g., vbUSDC) |
| `expiryTimestamp` | `uint256` | Unix timestamp when the pool expires |
| `rateMin` | `uint256` | Minimum swap rate the oracle can return (18 decimals). Rates below this are clamped. |
| `rateMax` | `uint256` | Maximum swap rate the oracle can return (18 decimals). Rates above this are clamped. |
| `rateChangePerDayMax` | `uint256` | Maximum allowed rate change per day in absolute value (18 decimals) |
| `rateChangeCapacityMax` | `uint256` | Maximum accumulated rate change capacity (18 decimals). Caps how much rate can change even if `rateChangePerDayMax` allows more. |
| `rateOracle` | `address` | Oracle contract providing the rate between Reference Asset and Collateral Asset |

### MarketId Computation

A MarketId is the `keccak256` hash of the ABI-encoded Market struct:

```solidity
MarketId marketId = MarketId.wrap(keccak256(abi.encode(market)));
```

In TypeScript with viem:

```typescript
import { encodeAbiParameters, keccak256 } from 'viem'

const marketId = keccak256(
  encodeAbiParameters(
    [
      { type: 'address' }, // collateralAsset
      { type: 'address' }, // referenceAsset
      { type: 'uint256' }, // expiryTimestamp
      { type: 'uint256' }, // rateMin
      { type: 'uint256' }, // rateMax
      { type: 'uint256' }, // rateChangePerDayMax
      { type: 'uint256' }, // rateChangeCapacityMax
      { type: 'address' }, // rateOracle
    ],
    [
      collateralAsset,
      referenceAsset,
      expiryTimestamp,
      rateMin,
      rateMax,
      rateChangePerDayMax,
      rateChangeCapacityMax,
      rateOracle,
    ]
  )
)
```

You can also compute it on-chain by calling `getId(Market)` on CorkPoolManager.

{% hint style="info" %}
A MarketId is deterministic — the same Market parameters always produce the same MarketId, regardless of which chain you compute it on.
{% endhint %}

### Operations Table

#### Before Expiry

| Operation | You Provide | You Receive | Pool Effect |
|---|---|---|---|
| Deposit / Mint | Collateral Asset | cPT + cST | CA ↑ |
| Unwind Deposit | cPT + cST | Collateral Asset | CA ↓ |
| Withdraw | cPT | CA + REF (proportional) | CA ↓, REF ↓ |
| Exercise (`swap` / `exercise`) | REF + cST | Collateral Asset - Fee | CA ↓, REF ↑, cST ↑ |
| Repurchase (`unwindSwap` / `unwindExercise`) | Collateral Asset | REF + cST | CA ↑, REF ↓, cST ↓ |

#### After Expiry

| Operation | You Provide | You Receive |
|---|---|---|
| Redeem | cPT | CA + REF (proportional to pool composition) |

### Swap Rate Formula

The swap rate determines how much Collateral Asset you receive per Reference Asset when exercising:

```
collateralOut = referenceIn × swapRate / 1e18
```

Example: with `swapRate = 0.82e18`, exercising with 100 REF returns 82 CA (before fees). The exercise also consumes cST — see [previewExercise](#exercise-previews) for exact amounts.

### Decimal Normalization

All Cork Principal Token (cPT) and Cork Swap Token (cST) amounts use **18 decimals**, regardless of the underlying token's decimals. When the Collateral Asset or Reference Asset has different decimals (e.g., USDC with 6 decimals), the protocol normalizes internally.

- **Input amounts** to contract functions should be in the token's **native decimals** (e.g., 6 decimals for USDC-based assets)
- **Share amounts** (cPT, cST) are always **18 decimals**
- **Swap rates and fees** are always **18 decimals**

### Pause Bitmap

Each pool has a 5-bit pause bitmap controlling which operations are active:

| Bit | Operation |
|---|---|
| 0 | Deposit |
| 1 | Swap / Exercise |
| 2 | Withdrawal |
| 3 | Unwind Deposit / Unwind Mint |
| 4 | Unwind Swap / Exercise |

Query the pause state via `getPausedBitMap(MarketId)` or use the DefaultCorkController convenience functions (`isDepositPaused`, `isSwapPaused`, etc.) documented in [Admin & Governance](admin-and-governance.md).
