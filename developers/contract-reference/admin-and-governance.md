---
description: Governance, access control, rate protection, and token factory contracts
---

# Admin & Governance

These contracts handle protocol governance, access control, and infrastructure. They are primarily relevant to protocol operators rather than integrators.

---

## DefaultCorkController

**Address:** `0xcCcCcCccCccbC06627F8aad7aAF13fe3a457f779`

Governance contract that manages pool creation, fee configuration, pause control, and whitelist management. Only accounts with the appropriate roles can call these functions.

### Pool Creation

```solidity
struct PoolCreationParams {
    Market pool;
    uint256 unwindSwapFeePercentage; // 18 decimals, 1e18 = 1%
    uint256 swapFeePercentage;       // 18 decimals, 1e18 = 1%
    bool isWhitelistEnabled;
}

function createNewPool(PoolCreationParams calldata params) external
```

### Fee Management

```solidity
function updateSwapFeePercentage(MarketId id, uint256 newSwapFeePercentage) external
function updateUnwindSwapFeePercentage(MarketId id, uint256 newUnwindSwapFeePercentage) external
```

Fees use 18-decimal precision (`1e18 = 1%`). Maximum allowed fee is `5e18` (5%).

### Pause Control

Per-market pause functions:

```solidity
function pauseDeposits(MarketId id) external
function unpauseDeposits(MarketId id) external
function pauseSwaps(MarketId id) external
function unpauseSwaps(MarketId id) external
function pauseWithdrawals(MarketId id) external
function unpauseWithdrawals(MarketId id) external
function pauseUnwindDepositAndMints(MarketId id) external
function unpauseUnwindDepositAndMints(MarketId id) external
function pauseUnwindSwaps(MarketId id) external
function unpauseUnwindSwaps(MarketId id) external
function pauseMarket(MarketId id) external  // pauses all operations
```

Single-market emergency pause/unpause:

```solidity
function pause(MarketId id) external
function unpause(MarketId id) external
```

All-markets pause:

```solidity
function pauseAll() external
function unpauseAll() external
```

### Pause Queries

```solidity
function isDepositPaused(MarketId id) external view returns (bool)
function isSwapPaused(MarketId id) external view returns (bool)
function isWithdrawalPaused(MarketId id) external view returns (bool)
function isUnwindDepositAndMintPaused(MarketId id) external view returns (bool)
function isUnwindSwapPaused(MarketId id) external view returns (bool)
```

### Whitelist Management

```solidity
function disableMarketWhitelist(MarketId marketId) external
function addToGlobalWhitelist(address[] calldata accounts) external
function removeFromGlobalWhitelist(address[] calldata accounts) external
function addToMarketWhitelist(MarketId marketId, address[] calldata accounts) external
function removeFromMarketWhitelist(MarketId marketId, address[] calldata accounts) external
function isWhitelisted(MarketId marketId, address account) external view returns (bool)
```

---

## WhitelistManager

**Address:** `0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE`

Manages global and per-market access control. An address is considered whitelisted if it is on the global whitelist OR the specific market whitelist.

```solidity
function isMarketWhitelistEnabled(MarketId poolId) external view returns (bool)
function isGlobalWhitelisted(address account) external view returns (bool)
function isMarketWhitelisted(MarketId poolId, address account) external view returns (bool)
function isWhitelisted(MarketId poolId, address account) external view returns (bool)
```

{% hint style="info" %}
`isWhitelisted` is the combined check — returns `true` if the market's whitelist is disabled (all addresses pass) OR if the account is on the global or market-specific whitelist. Use this for pre-flight checks before deposits.
{% endhint %}

---

## ConstraintRateAdapter

**Address:** `0xCCcCcCcccCccEF378949D1a61ED2283C831AF03A`

Protects against oracle manipulation by constraining how much the swap rate can change per day and in total. Wraps the rate oracle and applies daily change limits and capacity caps defined in the Market struct.

```solidity
function adjustedRate(MarketId poolId) external returns (uint256 rate)
function previewAdjustedRate(MarketId poolId) external view returns (uint256 rate)
function constraints(MarketId poolId) external view returns (
    uint256, // lastAdjustedRate
    uint256, // lastAdjustmentTimestamp
    uint256  // remainingCredits
)
```

- `adjustedRate` — fetches the oracle rate, applies constraints, and saves the new state. **Non-view**: this writes constraint state on-chain. Use `previewAdjustedRate` for read-only access
- `previewAdjustedRate` — same computation without writing state (view function)
- `constraints` — returns the current constraint state for a market

---

## SharesFactory

**Address:** `0xcCCCccCCCcCc1782617fe14A386AC910a20D4324`

Deploys paired Cork Principal Token (cPT) and Cork Swap Token (cST) contracts when a new pool is created.

```solidity
struct DeployParams {
    Market poolParams;
    MarketId poolId;
}

function deployPoolShares(DeployParams calldata params)
    external returns (address principalToken, address swapToken)
```

---

## PoolShare Tokens (cPT / cST)

Each Cork Pool issues two ERC20 tokens. Both implement standard ERC20 with EIP-2612 Permit support.

### Standard ERC20

`transfer`, `approve`, `balanceOf`, `totalSupply`, `allowance`, `permit` — standard interfaces.

### Pool-Specific Queries

```solidity
function poolId() external view returns (MarketId)
function poolManager() external view returns (IPoolManager)
function factory() external view returns (address)
function isExpired() external view returns (bool)
function expiry() external view returns (uint256)
function issuedAt() external view returns (uint256)
function pairName() external view returns (string memory)
function getReserves() external view returns (uint256 collateralAssets, uint256 referenceAssets)
```

### Convenience Functions

PoolShare tokens also expose all `preview*` and `max*` functions from CorkPoolManager as convenience wrappers, allowing you to query pool state directly from the token contract without needing to know the CorkPoolManager address.
