---
description: Query Cork Protocol data and simulate operations in minutes
---

# Quick Start

This guide gets you from zero to reading Cork Protocol data and simulating a deposit on a local fork.

## Contract Addresses

Cork Phoenix uses deterministic CREATE2 deployment — all addresses are identical across Ethereum mainnet and supported chains.

| Contract | Address |
|---|---|
| CorkPoolManager | `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC` |
| CorkAdapter | `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407` |
| WhitelistManager | `0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE` |

## Find an Active Market

Every Cork Pool is identified by a MarketId. Fetch active markets from the API:

```bash
curl -s "https://api-phoenix.cork.tech/v1/pools/?chainId=1" | jq '.items[0] | {poolId, poolName, expiry}'
```

This returns a MarketId you can use in the examples below. You can also browse the full list at the [API interactive docs](https://api-phoenix.cork.tech/docs).

## Query Pool State

{% tabs %}
{% tab title="Foundry" %}

No setup required beyond [Foundry](https://book.getfoundry.sh/getting-started/installation). Query any pool directly with `cast`:

```bash
# Set your RPC endpoint and a MarketId from the API
export RPC_URL="https://eth-mainnet.g.alchemy.com/v2/YOUR_KEY"
export MARKET_ID="0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a"
export POOL_MANAGER="0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC"

# Get the swap rate (1 REF = rate/1e18 CA)
cast call $POOL_MANAGER \
  "swapRate(bytes32)(uint256)" $MARKET_ID \
  --rpc-url $RPC_URL

# Get pool balances (collateral, reference)
cast call $POOL_MANAGER \
  "assets(bytes32)(uint256,uint256)" $MARKET_ID \
  --rpc-url $RPC_URL

# Get cPT and cST token addresses
cast call $POOL_MANAGER \
  "shares(bytes32)(address,address)" $MARKET_ID \
  --rpc-url $RPC_URL
```

{% endtab %}
{% tab title="TypeScript" %}

Install dependencies:

```bash
npm install viem
```

Create `query-cork.ts`:

```typescript
import { createPublicClient, http, type Address } from 'viem'
import { mainnet } from 'viem/chains'

const client = createPublicClient({
  chain: mainnet,
  transport: http(), // uses default public RPC — replace with your own for production
})

const POOL_MANAGER: Address = '0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC'

// Fetch an active market from the API, or paste a known MarketId
const MARKET_ID = '0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a' as `0x${string}`

const poolManagerAbi = [
  {
    name: 'swapRate',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'poolId', type: 'bytes32' }],
    outputs: [{ name: 'rate', type: 'uint256' }],
  },
  {
    name: 'assets',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'poolId', type: 'bytes32' }],
    outputs: [
      { name: 'collateralAssets', type: 'uint256' },
      { name: 'referenceAssets', type: 'uint256' },
    ],
  },
  {
    name: 'shares',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'poolId', type: 'bytes32' }],
    outputs: [
      { name: 'principalToken', type: 'address' },
      { name: 'swapToken', type: 'address' },
    ],
  },
] as const

async function main() {
  const swapRate = await client.readContract({
    address: POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'swapRate',
    args: [MARKET_ID],
  })
  console.log('Swap rate:', Number(swapRate) / 1e18)

  // Asset balances use the token's native decimals (e.g., 18 for sUSDe, 6 for vbUSDC)
  const [collateral, reference] = await client.readContract({
    address: POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'assets',
    args: [MARKET_ID],
  })
  console.log('Collateral:', collateral)
  console.log('Reference:', reference)

  const [principalToken, swapToken] = await client.readContract({
    address: POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'shares',
    args: [MARKET_ID],
  })
  console.log('cPT:', principalToken)
  console.log('cST:', swapToken)
}

main()
```

Run it:

```bash
npx tsx query-cork.ts
```

{% endtab %}
{% endtabs %}

{% hint style="info" %}
When you deposit Collateral Asset into a Cork Pool, you receive two tokens: a **Cork Principal Token (cPT)** representing your principal claim, and a **Cork Swap Token (cST)** representing your swap rights. See [Cork Principal Token](../core-concepts/principal-token.md) and [Cork Swap Token](../core-concepts/swap-token.md) for details.
{% endhint %}

## Simulate a Deposit

Fork mainnet locally and simulate a deposit without using real tokens.

{% tabs %}
{% tab title="Foundry" %}

### 1. Start a Local Fork

```bash
anvil --fork-url $RPC_URL
```

### 2. Run a Forge Script

Create `script/DepositExample.s.sol`:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import {Script, console} from "forge-std/Script.sol";

interface IERC20 {
    function approve(address spender, uint256 amount) external returns (bool);
    function balanceOf(address account) external view returns (uint256);
}

interface IPoolManager {
    function previewDeposit(bytes32 poolId, uint256 collateralAssetsIn) external view returns (uint256);
    function deposit(bytes32 poolId, uint256 collateralAssetsIn, address receiver) external returns (uint256);
    function shares(bytes32 poolId) external view returns (address, address);
    function swapRate(bytes32 poolId) external view returns (uint256);
}

interface IWhitelistManager {
    function isWhitelisted(bytes32 poolId, address account) external view returns (bool);
}

contract DepositExample is Script {
    address constant POOL_MANAGER = 0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC;
    address constant WHITELIST_MANAGER = 0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE;

    // sUSDe (Collateral Asset for the example market)
    address constant SUSDE = 0x9D39A5DE30e57443BfF2A8307A4256c8797A3497;

    function run() external {
        // Replace with a MarketId from the API
        bytes32 marketId = 0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a;

        // Use a test address — Anvil's default account
        address depositor = 0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266;

        // Deal 100 sUSDe to the depositor
        uint256 depositAmount = 100e18;
        deal(SUSDE, depositor, depositAmount);

        // Check whitelist status
        bool whitelisted = IWhitelistManager(WHITELIST_MANAGER).isWhitelisted(marketId, depositor);
        console.log("Whitelisted:", whitelisted);

        // If not whitelisted, the deposit will revert.
        // For testing, you can disable the whitelist or add the address via DefaultCorkController.
        if (!whitelisted) {
            console.log("Address not whitelisted — skipping deposit");
            return;
        }

        // Check swap rate
        uint256 rate = IPoolManager(POOL_MANAGER).swapRate(marketId);
        console.log("Swap rate:", rate);

        // Preview deposit
        uint256 expectedShares = IPoolManager(POOL_MANAGER).previewDeposit(marketId, depositAmount);
        console.log("Expected cPT + cST:", expectedShares);

        // Execute deposit
        vm.startBroadcast(depositor);
        IERC20(SUSDE).approve(POOL_MANAGER, depositAmount);
        uint256 sharesOut = IPoolManager(POOL_MANAGER).deposit(marketId, depositAmount, depositor);
        vm.stopBroadcast();
        console.log("Shares received:", sharesOut);

        // Check cPT and cST balances (both use 18 decimals)
        (address cpt, address cst) = IPoolManager(POOL_MANAGER).shares(marketId);
        console.log("cPT balance:", IERC20(cpt).balanceOf(depositor));
        console.log("cST balance:", IERC20(cst).balanceOf(depositor));
    }
}
```

Run it against your local fork:

```bash
forge script script/DepositExample.s.sol --fork-url http://127.0.0.1:8545 --broadcast
```

{% hint style="info" %}
The `deal` cheatcode sets token balances directly — no need to source tokens from an external account. This avoids dependencies on specific whitelisted addresses or funded accounts.
{% endhint %}

{% endtab %}
{% tab title="TypeScript" %}

### 1. Start a Local Fork

```bash
anvil --fork-url $YOUR_RPC_URL
```

### 2. Run the Deposit Script

Install dependencies:

```bash
npm install viem tsx
```

Create `deposit-fork.ts`:

```typescript
import {
  createPublicClient,
  createWalletClient,
  http,
  parseUnits,
  encodeFunctionData,
  type Address,
  type Hex,
} from 'viem'
import { foundry } from 'viem/chains'

const transport = http('http://127.0.0.1:8545')
const publicClient = createPublicClient({ chain: foundry, transport })
const walletClient = createWalletClient({ chain: foundry, transport })

const POOL_MANAGER: Address = '0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC'
const WHITELIST_MANAGER: Address = '0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE'
const SUSDE: Address = '0x9D39A5DE30e57443BfF2A8307A4256c8797A3497'

// Fetch an active MarketId from the API, or paste one here
const MARKET_ID: Hex = '0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a'

// Use Anvil's default funded account
const DEPOSITOR: Address = '0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266'

const erc20Abi = [
  {
    name: 'approve',
    type: 'function',
    stateMutability: 'nonpayable',
    inputs: [{ name: 'spender', type: 'address' }, { name: 'amount', type: 'uint256' }],
    outputs: [{ type: 'bool' }],
  },
  {
    name: 'balanceOf',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'account', type: 'address' }],
    outputs: [{ type: 'uint256' }],
  },
] as const

const poolManagerAbi = [
  {
    name: 'shares',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'poolId', type: 'bytes32' }],
    outputs: [{ name: 'principalToken', type: 'address' }, { name: 'swapToken', type: 'address' }],
  },
  {
    name: 'previewDeposit',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'poolId', type: 'bytes32' }, { name: 'collateralAssetsIn', type: 'uint256' }],
    outputs: [{ name: 'cptAndCstSharesOut', type: 'uint256' }],
  },
  {
    name: 'deposit',
    type: 'function',
    stateMutability: 'nonpayable',
    inputs: [
      { name: 'poolId', type: 'bytes32' },
      { name: 'collateralAssetsIn', type: 'uint256' },
      { name: 'receiver', type: 'address' },
    ],
    outputs: [{ name: 'cptAndCstSharesOut', type: 'uint256' }],
  },
] as const

const whitelistAbi = [
  {
    name: 'isWhitelisted',
    type: 'function',
    stateMutability: 'view',
    inputs: [{ name: 'poolId', type: 'bytes32' }, { name: 'account', type: 'address' }],
    outputs: [{ type: 'bool' }],
  },
] as const

async function main() {
  const depositAmount = parseUnits('100', 18) // 100 sUSDe

  // Deal tokens to the depositor using Anvil's cheatcode
  await publicClient.request({
    method: 'anvil_impersonateAccount' as 'eth_chainId',
    params: [DEPOSITOR],
  })

  // Check whitelist
  const isWhitelisted = await publicClient.readContract({
    address: WHITELIST_MANAGER,
    abi: whitelistAbi,
    functionName: 'isWhitelisted',
    args: [MARKET_ID, DEPOSITOR],
  })
  console.log('Whitelisted:', isWhitelisted)

  if (!isWhitelisted) {
    console.log('Address not whitelisted — deposit would revert')
    return
  }

  // Preview
  const expectedShares = await publicClient.readContract({
    address: POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'previewDeposit',
    args: [MARKET_ID, depositAmount],
  })
  // cPT and cST always use 18 decimals, regardless of the underlying token's decimals
  console.log('Expected cPT + cST:', Number(expectedShares) / 1e18)

  // Approve
  await walletClient.sendTransaction({
    account: DEPOSITOR,
    to: SUSDE,
    data: encodeFunctionData({
      abi: erc20Abi,
      functionName: 'approve',
      args: [POOL_MANAGER, depositAmount],
    }),
  })

  // Deposit
  const txHash = await walletClient.sendTransaction({
    account: DEPOSITOR,
    to: POOL_MANAGER,
    data: encodeFunctionData({
      abi: poolManagerAbi,
      functionName: 'deposit',
      args: [MARKET_ID, depositAmount, DEPOSITOR],
    }),
  })
  console.log('Deposit tx:', txHash)

  // Check balances
  const [cptAddress, cstAddress] = await publicClient.readContract({
    address: POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'shares',
    args: [MARKET_ID],
  })

  const cptBalance = await publicClient.readContract({
    address: cptAddress,
    abi: erc20Abi,
    functionName: 'balanceOf',
    args: [DEPOSITOR],
  })
  const cstBalance = await publicClient.readContract({
    address: cstAddress,
    abi: erc20Abi,
    functionName: 'balanceOf',
    args: [DEPOSITOR],
  })

  console.log('cPT balance:', Number(cptBalance) / 1e18)
  console.log('cST balance:', Number(cstBalance) / 1e18)
}

main()
```

Run it:

```bash
npx tsx deposit-fork.ts
```

{% endtab %}
{% endtabs %}

{% hint style="warning" %}
**Whitelist:** Some Cork markets require addresses to be whitelisted before depositing. The examples above check whitelist status before attempting a deposit. For testing on a fork, you can disable the whitelist by impersonating the DefaultCorkController admin.
{% endhint %}

{% hint style="warning" %}
**Use CorkAdapter for production.** The examples above call CorkPoolManager directly for simplicity. In production, use [CorkAdapter](contract-reference/cork-adapter.md) — it wraps the same operations with slippage protection and deadline checks.
{% endhint %}

## Production Integration

**Solidity integrators:** Add Cork as a Foundry dependency:

```bash
forge install cork-technology/phoenix
```

Then import interfaces directly:

```solidity
import {IPoolManager, MarketId, Market} from "contracts/interfaces/IPoolManager.sol";
import {ICorkAdapter} from "contracts/interfaces/ICorkAdapter.sol";
```

**TypeScript integrators:** Use [viem](https://viem.sh) with the ABI snippets shown above, or install the published package:

```bash
npm install @cork-technology/phoenix
```

## Key Gotchas

{% hint style="warning" %}
**MarketId computation** — A MarketId is `keccak256(abi.encode(Market))` where Market contains 8 fields (collateralAsset, referenceAsset, expiryTimestamp, rateMin, rateMax, rateChangePerDayMax, rateChangeCapacityMax, rateOracle). It is not a simple hash of two addresses. See [CorkPoolManager > Key Concepts](contract-reference/cork-pool-manager.md#key-concepts) for the full computation.
{% endhint %}

{% hint style="info" %}
**18-decimal shares** — All Cork Principal Token (cPT) and Cork Swap Token (cST) amounts use 18 decimals, regardless of the underlying token's decimals. A market with 6-decimal USDC as Collateral Asset still produces 18-decimal cPT and cST.
{% endhint %}

## Next Steps

- [CorkAdapter](contract-reference/cork-adapter.md) — slippage-protected write operations for production use
- [CorkPoolManager](contract-reference/cork-pool-manager.md) — full query and operation reference
- [API Reference](api-reference.md) — historical data, market discovery, and limit orders
