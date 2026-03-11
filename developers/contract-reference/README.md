---
description: Cork Phoenix contract addresses, architecture overview, and detailed interface documentation
---

# Contract Reference

Cork Phoenix uses a singleton architecture. All pools are managed by a single CorkPoolManager contract. For write operations with slippage protection, use CorkAdapter. For read operations, query CorkPoolManager directly.

## Contract Addresses

All addresses are identical across Ethereum mainnet and supported chains, deployed via deterministic CREATE2.

| Contract | Address | Purpose |
|---|---|---|
| CorkPoolManager | `0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC` | All pool operations and state queries |
| CorkAdapter | `0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407` | Safe wrappers with slippage and deadline protection |
| DefaultCorkController | `0xcCcCcCccCccbC06627F8aad7aAF13fe3a457f779` | Governance: pool creation, fees, pause control |
| WhitelistManager | `0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE` | Global and per-market access control |
| ConstraintRateAdapter | `0xCCcCcCcccCccEF378949D1a61ED2283C831AF03A` | Oracle rate protection with daily change limits |
| SharesFactory | `0xcCCCccCCCcCc1782617fe14A386AC910a20D4324` | cPT/cST token pair deployment |

## Architecture

```
┌─────────────────────────────────┐
│         Your Application        │
└──────────┬──────────────────────┘
           │ write operations          read operations
           ▼                                │
┌─────────────────────┐                     │
│     CorkAdapter     │                     │
│  (slippage/deadline │                     │
│   protection)       │                     │
└──────────┬──────────┘                     │
           │                                │
           ▼                                ▼
┌──────────────────────────────────────────────┐
│              CorkPoolManager                  │
│  (singleton — all pool state and operations)  │
└──────────────────────────────────────────────┘
```

**For integrators:**
- Call **CorkAdapter** for all write operations (deposits, swaps, withdrawals, redeems). It adds slippage protection and deadline checks.
- Call **CorkPoolManager** directly for read operations (swap rate, pool balances, previews, max amounts).

## Detailed References

- [CorkAdapter](cork-adapter.md) — every safe operation with parameter details, code examples, and error handling
- [CorkPoolManager](cork-pool-manager.md) — query functions, preview functions, raw operations, and key protocol concepts
- [Admin & Governance](admin-and-governance.md) — DefaultCorkController, WhitelistManager, ConstraintRateAdapter, SharesFactory, and PoolShare tokens
