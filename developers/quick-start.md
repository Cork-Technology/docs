---
description: Query Cork Protocol data and simulate operations in minutes
---

# Quick Start

This guide gets you from zero to reading Cork Protocol data and simulating operations on a local fork. No tokens or wallet required for the first section.

## Prerequisites

- Node.js 18+
- A package manager (npm, yarn, or pnpm)

```bash
npm install viem tsx
```

The examples below use `npx tsx` to run TypeScript directly.

## Contract Addresses

Cork Phoenix uses deterministic CREATE2 deployment — all addresses are identical across Ethereum mainnet and supported chains.

| Contract | Address |
|---|---|
| CorkPoolManager | `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC` |
| CorkAdapter | `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407` |
| WhitelistManager | `0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE` |

## Query Cork in 30 Seconds

Create a file called `query-cork.ts` and paste the following:

```typescript
import { createPublicClient, http } from 'viem'
import { mainnet } from 'viem/chains'

const client = createPublicClient({
  chain: mainnet,
  transport: http(), // uses default public RPC — replace with your own for production
})

const CORK_POOL_MANAGER = '0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC' as const

// A deployed sUSDe/vbUSDC market
// To find active markets, query: GET https://api-phoenix.cork.tech/v1/pools/?chainId=1
const MARKET_ID = '0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a' as const

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
  // 1. Read the swap rate — 1 REF = X CA (scaled to 18 decimals)
  const swapRate = await client.readContract({
    address: CORK_POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'swapRate',
    args: [MARKET_ID],
  })
  console.log('Swap rate:', Number(swapRate) / 1e18)

  // 2. Read pool balances (TVL)
  const [collateral, reference] = await client.readContract({
    address: CORK_POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'assets',
    args: [MARKET_ID],
  })
  console.log('Collateral (sUSDe):', Number(collateral) / 1e18) // sUSDe has 18 decimals
  console.log('Reference (vbUSDC):', Number(reference) / 1e6)   // vbUSDC has 6 decimals (native decimals)

  // 3. Get the cPT and cST token addresses for this market
  const [principalToken, swapToken] = await client.readContract({
    address: CORK_POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'shares',
    args: [MARKET_ID],
  })
  console.log('Cork Principal Token (cPT):', principalToken)
  console.log('Cork Swap Token (cST):', swapToken)
}

main()
```

Run it:

```bash
npx tsx query-cork.ts
```

You should see the current swap rate, pool balances, and token addresses for the sUSDe/vbUSDC market.

{% hint style="info" %}
When you deposit Collateral Asset into a Cork Pool, you receive two tokens: a **Cork Principal Token (cPT)** representing your principal claim, and a **Cork Swap Token (cST)** representing your swap rights. See [Cork Principal Token](../core-concepts/principal-token.md) and [Cork Swap Token](../core-concepts/swap-token.md) for details.
{% endhint %}

## Try It Locally

To simulate write operations (deposits, swaps) without using real tokens, fork mainnet locally using [Foundry's Anvil](https://book.getfoundry.sh/getting-started/installation).

### 1. Start a Local Fork

```bash
anvil --fork-url $YOUR_RPC_URL
```

Replace `$YOUR_RPC_URL` with your Alchemy, Infura, or other Ethereum mainnet RPC endpoint. Anvil starts a local chain at `http://127.0.0.1:8545` forked from the latest mainnet state.

### 2. Simulate a Deposit

Create a file called `deposit-fork.ts`:

```typescript
import {
  createPublicClient,
  createWalletClient,
  http,
  parseUnits,
  encodeFunctionData,
} from 'viem'
import { foundry } from 'viem/chains'

// Point at the local Anvil fork
const transport = http('http://127.0.0.1:8545')
const publicClient = createPublicClient({ chain: foundry, transport })
const walletClient = createWalletClient({ chain: foundry, transport })

// Contracts
const CORK_POOL_MANAGER = '0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC' as const
const CORK_ADAPTER = '0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407' as const
const SUSDE = '0x9D39A5DE30e57443BfF2A8307A4256c8797A3497' as const

const MARKET_ID = '0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a' as const

// A whitelisted address on this market
const WHITELISTED_ACCOUNT = '0x49903609b8a22e1d9f7406d1306191e28c8ececd' as const

const erc20Abi = [
  {
    name: 'approve',
    type: 'function',
    stateMutability: 'nonpayable',
    inputs: [
      { name: 'spender', type: 'address' },
      { name: 'amount', type: 'uint256' },
    ],
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
    outputs: [
      { name: 'principalToken', type: 'address' },
      { name: 'swapToken', type: 'address' },
    ],
  },
  {
    name: 'previewDeposit',
    type: 'function',
    stateMutability: 'view',
    inputs: [
      { name: 'poolId', type: 'bytes32' },
      { name: 'collateralAssetsIn', type: 'uint256' },
    ],
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

async function main() {
  const depositAmount = parseUnits('1', 18) // 1 sUSDe

  // Impersonate the whitelisted account
  await publicClient.request({
    method: 'anvil_impersonateAccount' as any,
    params: [WHITELISTED_ACCOUNT],
  })

  // Check sUSDe balance
  const balance = await publicClient.readContract({
    address: SUSDE,
    abi: erc20Abi,
    functionName: 'balanceOf',
    args: [WHITELISTED_ACCOUNT],
  })
  console.log('sUSDe balance:', Number(balance) / 1e18)

  // If balance is 0, use anvil_setBalance to give the account ETH
  // and deal tokens (or pick a different whitelisted address with funds)
  if (balance === 0n) {
    console.log('No sUSDe balance. Use `anvil_setStorageAt` to deal tokens, or pick a funded whitelisted address.')
    return
  }

  // 1. Preview the deposit to see expected shares
  const expectedShares = await publicClient.readContract({
    address: CORK_POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'previewDeposit',
    args: [MARKET_ID, depositAmount],
  })
  console.log('Expected cPT + cST shares:', Number(expectedShares) / 1e18)

  // 2. Approve CorkPoolManager to spend sUSDe
  await walletClient.sendTransaction({
    account: WHITELISTED_ACCOUNT,
    to: SUSDE,
    data: encodeFunctionData({
      abi: erc20Abi,
      functionName: 'approve',
      args: [CORK_POOL_MANAGER, depositAmount],
    }),
  })
  console.log('Approved CorkPoolManager to spend sUSDe')

  // 3. Deposit
  const txHash = await walletClient.sendTransaction({
    account: WHITELISTED_ACCOUNT,
    to: CORK_POOL_MANAGER,
    data: encodeFunctionData({
      abi: poolManagerAbi,
      functionName: 'deposit',
      args: [MARKET_ID, depositAmount, WHITELISTED_ACCOUNT],
    }),
  })
  console.log('Deposit tx:', txHash)

  // 4. Check cPT and cST balances
  const [cptAddress, cstAddress] = await publicClient.readContract({
    address: CORK_POOL_MANAGER,
    abi: poolManagerAbi,
    functionName: 'shares',
    args: [MARKET_ID],
  })

  const cptBalance = await publicClient.readContract({
    address: cptAddress,
    abi: erc20Abi,
    functionName: 'balanceOf',
    args: [WHITELISTED_ACCOUNT],
  })

  const cstBalance = await publicClient.readContract({
    address: cstAddress,
    abi: erc20Abi,
    functionName: 'balanceOf',
    args: [WHITELISTED_ACCOUNT],
  })

  // cPT and cST always use 18 decimals, regardless of the underlying token's decimals
  console.log('cPT balance:', Number(cptBalance) / 1e18)
  console.log('cST balance:', Number(cstBalance) / 1e18)
}

main()
```

Run it:

```bash
npx tsx deposit-fork.ts
```

{% hint style="warning" %}
**Whitelist:** Some Cork markets require addresses to be whitelisted before depositing. Check with `isWhitelisted(marketId, address)` on WhitelistManager (`0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE`) before attempting a deposit.
{% endhint %}

{% hint style="warning" %}
**Use CorkAdapter for production.** The example above calls CorkPoolManager directly for simplicity. In production, use [CorkAdapter](contract-reference/cork-adapter.md) — it wraps the same operations with slippage protection and deadline checks.
{% endhint %}

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
