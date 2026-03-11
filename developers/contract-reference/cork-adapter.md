---
description: Slippage-protected write operations for Cork Protocol integration
---

# CorkAdapter

**Address:** `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407` (same on all chains)

CorkAdapter wraps CorkPoolManager operations with slippage protection and deadline checks via Morpho Bundler3. Use this contract for all write operations in production integrations.

{% hint style="warning" %}
While you can interact with CorkPoolManager directly, it does not provide slippage or deadline protection. Use CorkAdapter for production integrations.
{% endhint %}

## Common Parameters

Every CorkAdapter function takes a params struct. These fields appear across all operations:

| Field | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | The MarketId identifying which Cork Pool to interact with |
| `receiver` | `address` | The address that receives the output tokens |
| `deadline` | `uint256` | Unix timestamp after which the transaction reverts. A reasonable default is `block.timestamp + 300` (5 minutes). |

**Slippage fields** vary per operation — each function has either a `min*Out` field (minimum acceptable output) or `max*In` field (maximum acceptable input). Set these using the corresponding `preview*` function on CorkPoolManager, adjusted for your slippage tolerance. For example, for 0.5% slippage on a deposit:

```typescript
const expectedShares = await previewDeposit(marketId, amount)
const minSharesOut = expectedShares * 995n / 1000n // 0.5% slippage
```

---

## Deposit Operations

Provide Collateral Asset to receive Cork Principal Token (cPT) and Cork Swap Token (cST).

### safeDeposit

Deposit a specific amount of Collateral Asset and receive cPT + cST shares.

```solidity
function safeDeposit(SafeDepositParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `collateralAssetsIn` | `uint256` | Amount of Collateral Asset to deposit (in native token decimals) |
| `receiver` | `address` | Receives the minted cPT and cST |
| `minCptAndCstSharesOut` | `uint256` | Minimum cPT + cST shares to receive (18 decimals) |
| `deadline` | `uint256` | Transaction deadline |

```typescript
import { encodeFunctionData } from 'viem'

const params = {
  poolId: MARKET_ID,
  collateralAssetsIn: parseUnits('100', 18), // 100 sUSDe
  receiver: userAddress,
  minCptAndCstSharesOut: expectedShares * 995n / 1000n,
  deadline: BigInt(Math.floor(Date.now() / 1000) + 300),
}

const data = encodeFunctionData({
  abi: corkAdapterAbi,
  functionName: 'safeDeposit',
  args: [params],
})
```

### safeMint

Mint a specific amount of cPT + cST shares, spending up to a maximum of Collateral Asset.

```solidity
function safeMint(SafeMintParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `cptAndCstSharesOut` | `uint256` | Exact amount of cPT + cST shares to mint (18 decimals) |
| `receiver` | `address` | Receives the minted cPT and cST |
| `maxCollateralAssetsIn` | `uint256` | Maximum Collateral Asset to spend (native decimals) |
| `deadline` | `uint256` | Transaction deadline |

---

## Unwind Deposit

Return cPT + cST to get Collateral Asset back. Available before expiry only.

### safeUnwindDeposit

Specify how much Collateral Asset you want back; burns the required cPT + cST.

```solidity
function safeUnwindDeposit(SafeUnwindDepositParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `collateralAssetsOut` | `uint256` | Target Collateral Asset amount to receive |
| `owner` | `address` | Address that owns the cPT and cST being burned |
| `receiver` | `address` | Receives the Collateral Asset |
| `maxCptAndCstSharesIn` | `uint256` | Maximum cPT + cST shares to burn |
| `deadline` | `uint256` | Transaction deadline |

### safeUnwindMint

Specify how many cPT + cST shares to burn; receive the corresponding Collateral Asset.

```solidity
function safeUnwindMint(SafeUnwindMintParams memory params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `cptAndCstSharesIn` | `uint256` | Amount of cPT + cST shares to burn. Pass `type(uint256).max` to redeem all. |
| `owner` | `address` | Address that owns the shares |
| `receiver` | `address` | Receives the Collateral Asset |
| `minCollateralAssetsOut` | `uint256` | Minimum Collateral Asset to receive |
| `deadline` | `uint256` | Transaction deadline |

---

## Withdraw

Burn Cork Principal Token (cPT) to exit a position pre-expiry, receiving proportional Collateral Asset and Reference Asset based on current pool composition. This is the pre-expiry counterpart to [Redeem](#redeem-post-expiry).

### safeWithdraw

Withdraw a specific amount of Collateral Asset by burning cPT.

```solidity
function safeWithdraw(SafeWithdrawParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `collateralAssetsOut` | `uint256` | Amount of Collateral Asset to withdraw |
| `owner` | `address` | Address that owns the cPT being burned |
| `receiver` | `address` | Receives the withdrawn assets |
| `maxCptSharesIn` | `uint256` | Maximum cPT shares to burn |
| `deadline` | `uint256` | Transaction deadline |

### safeWithdrawOther

Withdraw a specific amount of Reference Asset by burning cPT.

```solidity
function safeWithdrawOther(SafeWithdrawOtherParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `referenceAssetsOut` | `uint256` | Amount of Reference Asset to withdraw |
| `owner` | `address` | Address that owns the cPT being burned |
| `receiver` | `address` | Receives the withdrawn assets |
| `maxCptSharesIn` | `uint256` | Maximum cPT shares to burn |
| `deadline` | `uint256` | Transaction deadline |

---

## Redeem (Post-Expiry)

Burn Cork Principal Token (cPT) after the pool has expired to receive proportional Collateral Asset and Reference Asset.

### safeRedeem

```solidity
function safeRedeem(SafeRedeemParams memory params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `cptSharesIn` | `uint256` | Amount of cPT to redeem. Pass `type(uint256).max` to redeem all. |
| `owner` | `address` | Address that owns the cPT |
| `receiver` | `address` | Receives the redeemed assets |
| `minReferenceAssetsOut` | `uint256` | Minimum Reference Asset to receive |
| `minCollateralAssetsOut` | `uint256` | Minimum Collateral Asset to receive |
| `deadline` | `uint256` | Transaction deadline |

---

## Exercise

Exercise Cork Swap Token (cST) coverage — provide Reference Asset + cST to receive Collateral Asset (minus an exercise fee). Available before expiry only.

### safeExercise

Specify the amount of cST to exercise.

```solidity
function safeExercise(SafeExerciseParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `cstSharesIn` | `uint256` | Amount of cST shares to exercise |
| `receiver` | `address` | Receives the Collateral Asset |
| `minCollateralAssetsOut` | `uint256` | Minimum Collateral Asset to receive |
| `maxReferenceAssetsIn` | `uint256` | Maximum Reference Asset to spend |
| `deadline` | `uint256` | Transaction deadline |

### safeExerciseOther

Specify the amount of Reference Asset to provide; the required cST is computed automatically.

```solidity
function safeExerciseOther(SafeExerciseOtherParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `referenceAssetsIn` | `uint256` | Amount of Reference Asset to provide |
| `receiver` | `address` | Receives the Collateral Asset |
| `minCollateralAssetsOut` | `uint256` | Minimum Collateral Asset to receive |
| `maxCstSharesIn` | `uint256` | Maximum cST shares to spend |
| `deadline` | `uint256` | Transaction deadline |

### safeSwap

Specify the amount of Collateral Asset to receive; the required cST and Reference Asset are computed automatically.

```solidity
function safeSwap(SafeSwapParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `collateralAssetsOut` | `uint256` | Exact amount of Collateral Asset to receive |
| `receiver` | `address` | Receives the Collateral Asset |
| `maxCstSharesIn` | `uint256` | Maximum cST shares to spend |
| `maxReferenceAssetsIn` | `uint256` | Maximum Reference Asset to spend |
| `deadline` | `uint256` | Transaction deadline |

---

## Repurchase (Unwind Exercise)

Reverse an exercise — provide Collateral Asset to get back Reference Asset + Cork Swap Token (cST). Available before expiry only.

### safeUnwindSwap

Specify the Collateral Asset to provide; receive Reference Asset + cST.

```solidity
function safeUnwindSwap(SafeUnwindSwapParams memory params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `collateralAssetsIn` | `uint256` | Amount of Collateral Asset to provide. Pass `type(uint256).max` to use full balance. |
| `receiver` | `address` | Receives the Reference Asset and cST |
| `minReferenceAssetsOut` | `uint256` | Minimum Reference Asset to receive |
| `minCstSharesOut` | `uint256` | Minimum cST shares to receive |
| `deadline` | `uint256` | Transaction deadline |

### safeUnwindExercise

Specify the cST amount to unlock; provide Collateral Asset in exchange.

```solidity
function safeUnwindExercise(SafeUnwindExerciseParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `cstSharesOut` | `uint256` | Amount of cST shares to unlock |
| `receiver` | `address` | Receives cST and Reference Asset |
| `minReferenceAssetsOut` | `uint256` | Minimum Reference Asset to receive |
| `maxCollateralAssetsIn` | `uint256` | Maximum Collateral Asset to spend |
| `deadline` | `uint256` | Transaction deadline |

### safeUnwindExerciseOther

Specify the Reference Asset amount to unlock; provide Collateral Asset in exchange.

```solidity
function safeUnwindExerciseOther(SafeUnwindExerciseOtherParams calldata params) external
```

| Parameter | Type | Description |
|---|---|---|
| `poolId` | `bytes32` | MarketId |
| `referenceAssetsOut` | `uint256` | Amount of Reference Asset to unlock |
| `receiver` | `address` | Receives cST and Reference Asset |
| `minCstSharesOut` | `uint256` | Minimum cST shares to receive |
| `maxCollateralAssetsIn` | `uint256` | Maximum Collateral Asset to spend |
| `deadline` | `uint256` | Transaction deadline |

---

## Error Reference

These errors may be thrown by CorkAdapter operations:

| Error | Cause |
|---|---|
| `DeadlineExceeded()` | Transaction was mined after the specified deadline |
| `InsufficientLiquidity(uint256 available, uint256 requested)` | The pool does not have enough Reference Asset + cST liquidity to fill the requested unwind swap amount |
| `NotWhitelisted(address account, bytes32 marketId)` | The caller is not on the whitelist for this market |
| `Expired()` | Attempted a pre-expiry operation on an expired pool |
| `NotExpired()` | Attempted a post-expiry operation (redeem) on a pool that has not expired |
| `ZeroAmount()` | Input amount is zero |
| `InsufficientSharesAmount(uint256 minimumRequired, uint256 provided)` | Slippage check failed — output is less than the specified minimum |
| `InsufficientAmount()` | Input amount does not meet the minimum requirements |
