---
description: How to earn yield on Cork Protocol by underwriting risk as a liquidity provider
---

# Provide Liquidity

Liquidity providers deposit Collateral Asset into a Cork Pool to mint [Cork Principal Tokens](../../core-concepts/principal-token.md) and [Cork Swap Tokens](../../core-concepts/swap-token.md). By selling the Cork Swap Tokens at a premium, the LP earns yield while taking on the underwriting risk represented by the Cork Principal Token.

## What You'll Need

- **Collateral Asset** (e.g., sUSDe, sUSDS) in your wallet
- A wallet that supports the Cork dApp or direct contract interaction (SAFE Wallet recommended for multisig users)

## Operations

| Action | What It Does | When To Use |
|---|---|---|
| **Mint** (Deposit) | Deposit Collateral Asset → receive Cork Principal Token + Cork Swap Token | Enter a new position |
| **Unwind** (Unwind Deposit) | Return Cork Principal Token + Cork Swap Token → receive Collateral Asset | Exit a position before expiry |
| **Redeem** (Withdraw) | Return Cork Principal Token → receive Collateral Asset + Reference Asset | Claim assets after expiry |

## Guides

- [Mint Tokens](mint-tokens.md) — Overview of minting and the LP position lifecycle
- [Mint Tokens (without dApp)](mint-tokens-without-dapp.md) — Step-by-step guide using SAFE Wallet transaction builder
- [Mint Tokens (without dApp or Batch-Capable Wallet)](mint-tokens-without-dapp-or-batch-capable-wallet.md) — Guide for standard wallets via Blockscout
