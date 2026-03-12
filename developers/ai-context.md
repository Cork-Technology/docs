---
description: Structured protocol reference optimized for AI assistants and LLM consumption
---

# AI Context

This page provides a consolidated, structured reference for AI assistants integrating with or answering questions about Cork Protocol. It complements the detailed pages in this documentation by bringing together contract addresses, key function signatures, operations, and common gotchas in one place.

---

## What is Cork?

Cork is a programmable risk layer for onchain assets such as RWAs, vault tokens, and yield-bearing stablecoins. Cork's risk infrastructure primitive enables asset managers and issuers to spin up custom swap markets that enhance redemption liquidity, risk transparency, and market confidence for their onchain assets.

Each Cork Pool is built around a Collateral Asset (CA) and Reference Asset (REF) pair. Depositors provide CA and receive two tokens: Cork Principal Token (cPT) representing principal claims, and Cork Swap Token (cST) representing swap/exercise rights. cST holders can exercise coverage by exchanging REF + cST for CA — guaranteeing redemption liquidity regardless of market conditions.

All pools are managed by a single CorkPoolManager contract (singleton architecture). For write operations with slippage and deadline protection, use CorkAdapter. For read operations, query CorkPoolManager directly.

---

## Core Concepts

- **Cork Pool**: A singleton-managed pool holding Collateral Asset, issuing cPT + cST on deposit. Each pool is identified by a MarketId.
- **Cork Principal Token (cPT)**: ERC20 token representing a depositor's principal claim. Redeemable for proportional CA + REF after expiry.
- **Cork Swap Token (cST)**: ERC20 token granting the right to exercise — exchange REF + cST for CA (minus a fee) before expiry.
- **Collateral Asset (CA)**: The yield-bearing asset deposited into the pool (e.g., sUSDe, wstETH).
- **Reference Asset (REF)**: The asset whose risk is being covered (e.g., vbUSDC, ezETH).
- **Market struct**: 8 fields defining a pool — collateralAsset, referenceAsset, expiryTimestamp, rateMin, rateMax, rateChangePerDayMax, rateChangeCapacityMax, rateOracle.
- **MarketId**: `keccak256(abi.encode(Market))` — deterministic hash of all 8 Market fields. Same parameters always produce the same MarketId on any chain.
- **Swap Rate**: Exchange rate between REF and CA, scaled to 18 decimals. `collateralOut = referenceIn * swapRate / 1e18`.

---

## Contract Addresses

All addresses are identical across Ethereum mainnet and supported chains (deterministic CREATE2 deployment).

| Contract | Address | Purpose |
|---|---|---|
| CorkPoolManager | `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC` | Singleton — all pool state and operations |
| CorkAdapter | `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407` | Safe wrappers with slippage/deadline protection |
| DefaultCorkController | `0xcCcCcCccCccbC06627F8aad7aAF13fe3a457f779` | Governance: pool creation, fees, pause control |
| WhitelistManager | `0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE` | Global and per-market access control |
| ConstraintRateAdapter | `0xCCcCcCcccCccEF378949D1a61ED2283C831AF03A` | Oracle rate protection with daily change limits |
| SharesFactory | `0xcCCCccCCCcCc1782617fe14A386AC910a20D4324` | cPT/cST token pair deployment |

---

## Key Functions

### Read Operations (call CorkPoolManager)

```solidity
// Pool state
function swapRate(MarketId poolId) external view returns (uint256 rate)
function assets(MarketId poolId) external view returns (uint256 collateralAssets, uint256 referenceAssets)
function shares(MarketId poolId) external view returns (address principalToken, address swapToken)
function market(MarketId poolId) external view returns (Market memory)

// Preview operations (returns expected output without executing)
function previewDeposit(MarketId poolId, uint256 collateralAssetsIn) external view returns (uint256 cptAndCstSharesOut)
function previewExercise(MarketId poolId, uint256 cstSharesIn) external view returns (uint256 collateralAssetsOut, uint256 referenceAssetsIn, uint256 fee)
function previewRedeem(MarketId poolId, uint256 cptSharesIn) external view returns (uint256 referenceAssetsOut, uint256 collateralAssetsOut)
function previewUnwindDeposit(MarketId poolId, uint256 collateralAssetsOut) external view returns (uint256 cptAndCstSharesIn)
function previewUnwindSwap(MarketId poolId, uint256 collateralAssetsIn) external view returns (uint256 cstSharesOut, uint256 referenceAssetsOut, uint256 fee)
function previewWithdraw(MarketId poolId, uint256 collateralAssetsOut) external view returns (uint256 cptSharesIn, uint256 actualCollateralAssetsOut, uint256 actualReferenceAssetsOut)

// Max amounts (accounts for balances, allowances, pool liquidity)
function maxDeposit(MarketId poolId, address owner) external view returns (uint256)
function maxExercise(MarketId poolId, address owner) external view returns (uint256)
function maxRedeem(MarketId poolId, address owner) external view returns (uint256)

// Fees (18-decimal precision: 1e18 = 1%, max 5e18 = 5%)
function swapFee(MarketId poolId) external view returns (uint256 fees)
function unwindSwapFee(MarketId poolId) external view returns (uint256 fees)

// Whitelist check (call WhitelistManager)
function isWhitelisted(MarketId marketId, address account) external view returns (bool)
```

### Write Operations (call CorkAdapter for production use)

```solidity
// Deposit: CA → cPT + cST
function safeDeposit(SafeDepositParams calldata params) external
// params: poolId, collateralAssetsIn, receiver, minCptAndCstSharesOut, deadline

// Exercise: REF + cST → CA (minus fee) — before expiry
function safeExercise(SafeExerciseParams calldata params) external
// params: poolId, cstSharesIn, receiver, minCollateralAssetsOut, maxReferenceAssetsIn, deadline

function safeSwap(SafeSwapParams calldata params) external
// params: poolId, collateralAssetsOut, receiver, maxCstSharesIn, maxReferenceAssetsIn, deadline

// Withdraw: cPT → CA + REF (proportional) — before expiry
function safeWithdraw(SafeWithdrawParams calldata params) external
// params: poolId, collateralAssetsOut, owner, receiver, maxCptSharesIn, deadline

// Redeem: cPT → CA + REF (proportional) — after expiry
function safeRedeem(SafeRedeemParams memory params) external
// params: poolId, cptSharesIn, owner, receiver, minReferenceAssetsOut, minCollateralAssetsOut, deadline

// Unwind Deposit: cPT + cST → CA — before expiry
function safeUnwindDeposit(SafeUnwindDepositParams calldata params) external
// params: poolId, collateralAssetsOut, owner, receiver, maxCptAndCstSharesIn, deadline

// Repurchase: CA → REF + cST — before expiry
function safeUnwindSwap(SafeUnwindSwapParams memory params) external
// params: poolId, collateralAssetsIn, receiver, minReferenceAssetsOut, minCstSharesOut, deadline
```

### MarketId Computation

```solidity
// Solidity
MarketId marketId = MarketId.wrap(keccak256(abi.encode(market)));

// TypeScript (viem)
import { encodeAbiParameters, keccak256 } from 'viem'
const marketId = keccak256(encodeAbiParameters(
  [{ type: 'address' }, { type: 'address' }, { type: 'uint256' },
   { type: 'uint256' }, { type: 'uint256' }, { type: 'uint256' },
   { type: 'uint256' }, { type: 'address' }],
  [collateralAsset, referenceAsset, expiryTimestamp,
   rateMin, rateMax, rateChangePerDayMax, rateChangeCapacityMax, rateOracle]
))
```

---

## Operations Summary

### Before Expiry

| Operation | You Provide | You Receive |
|---|---|---|
| Deposit | Collateral Asset | cPT + cST |
| Unwind Deposit | cPT + cST | Collateral Asset |
| Withdraw | cPT | CA + REF (proportional) |
| Exercise | REF + cST | Collateral Asset (minus fee) |
| Repurchase | Collateral Asset | REF + cST |

### After Expiry

| Operation | You Provide | You Receive |
|---|---|---|
| Redeem | cPT | CA + REF (proportional to pool composition) |

---

## API

- **Base URL**: `https://api-phoenix.cork.tech`
- **Interactive docs**: [https://api-phoenix.cork.tech/docs](https://api-phoenix.cork.tech/docs)
- **Key endpoints**:
  - `GET /v1/pools/?chainId=1` — list pools with TVL, fees, pause status
  - `GET /v1/pools/whitelisted-addresses?chainId=1` — check whitelist status
  - `GET /v1/flows/?chainId=1` — transaction history
  - `GET /v1/limit-orders/markets?chainId=1` — limit order markets
- Always include `chainId=1` for Ethereum mainnet

---

## Key Gotchas

- **18-decimal shares**: cPT and cST always use 18 decimals, regardless of the underlying token's decimals (e.g., 6-decimal USDC produces 18-decimal cPT/cST)
- **Fees**: 18-decimal precision where `1e18 = 1%`. Maximum fee is `5e18` (5%). Fees apply to Exercise and Repurchase operations.
- **Whitelist**: Some markets require addresses to be whitelisted before depositing. Check `isWhitelisted(marketId, address)` on WhitelistManager before attempting deposits.
- **MarketId**: Computed from ALL 8 Market struct fields, not just collateralAsset and referenceAsset. The same Market parameters produce the same MarketId on any chain.
- **CorkAdapter vs CorkPoolManager**: Use CorkAdapter (`safe*` functions) for production writes — it adds slippage and deadline protection. Use CorkPoolManager directly for reads.

---

## Links

- [Developer Quick Start](quick-start.md)
- [Contract Reference](contract-reference/)
- [API Reference](api-reference.md)
- [Core Concepts](../core-concepts/cork-pool.md)
- [GitHub](https://github.com/Cork-Technology/phoenix)
- [Website](https://cork.tech)
