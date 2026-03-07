---
description: Official Cork Phoenix deployment addresses on Ethereum Mainnet
---

# Phoenix Deployments

Cork Phoenix uses the Safe CREATE2 Deployer (`0x914d7Fec6aaC8cd542e72Bca78B30650d45643d7`) for deterministic contract addresses across all supported chains.

{% hint style="warning" %}
Always verify contract addresses against this page or the [phoenix repo](https://github.com/Cork-Technology/phoenix) before interacting. Cork will never ask you to send funds to an address not listed here.
{% endhint %}

### Core Contracts

| Contract                         | Address                                                                                                                 | Role                                                                                              |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| CorkPoolManager (Proxy)          | [`0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC`](https://etherscan.io/address/0xccCCcCcCCccCfAE2Ee43F0E727A8c2969d74B9eC) | Singleton managing all pool states, deposits, withdrawals, swaps                                  |
| CorkPoolManager (Implementation) | [`0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244`](https://etherscan.io/address/0x1cCccCccCcCf9A60Fe57cd7CEf504d1DaaA78244) | UUPS upgradeable implementation                                                                   |
| CorkAdapter                      | [`0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407`](https://etherscan.io/address/0xCCcCcCCCcccCBaD6F772a511B337d9CCc9570407) | Periphery adapter providing slippage protection, deadline checks, and Morpho Bundler3 integration |
| SharesFactory                    | [`0xcCCCccCCCcCc1782617fe14A386AC910a20D4324`](https://etherscan.io/address/0xcCCCccCCCcCc1782617fe14A386AC910a20D4324) | Deploys Cork Principal Token (cPT) and Cork Swap Token (cST) pairs for each pool                  |

### Governance & Access Control

| Contract                      | Address                                                                                                                 | Role                                                                             |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| DefaultCorkController         | [`0xcCcCcCccCccbC06627F8aad7aAF13fe3a457f779`](https://etherscan.io/address/0xcCcCcCccCccbC06627F8aad7aAF13fe3a457f779) | Pool authorization, fee configuration, pause, whitelist, and treasury management |
| WhitelistManager (Proxy)      | [`0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE`](https://etherscan.io/address/0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE) | Global and market-specific access control for pool operations                    |
| ConstraintRateAdapter (Proxy) | [`0xCCcCcCcccCccEF378949D1a61ED2283C831AF03A`](https://etherscan.io/address/0xCCcCcCcccCccEF378949D1a61ED2283C831AF03A) | Chainlink oracle integration for constraint rate pricing                         |

### Timelock Contracts

All governance actions are subject to timelocks with the following delays:

| Contract                | Address                                                                                                                 | Delay             |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------- |
| TimelockUpgrade         | [`0x7CcCCCccCcc0b4c00d01f321035b8e4523eF8448`](https://etherscan.io/address/0x7CcCCCccCcc0b4c00d01f321035b8e4523eF8448) | 5 days (432,000s) |
| TimelockControllerAdmin | [`0x7CccCCccccCCe566CdAFFA9EF2CB245Ad5575c3b`](https://etherscan.io/address/0x7CccCCccccCCe566CdAFFA9EF2CB245Ad5575c3b) | 1 day (86,400s)   |
| TimelockOperational     | [`0x7CcCcCCcCccCC1d856F2994A66fAa7011F1A89D9`](https://etherscan.io/address/0x7CcCcCCcCccCC1d856F2994A66fAa7011F1A89D9) | 6 hours (21,600s) |

### External Dependencies

| Contract              | Address                                                                                                                 | Purpose                                                                               |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| Morpho Bundler3       | [`0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245`](https://etherscan.io/address/0x6566194141eefa99Af43Bb5Aa71460Ca2Dc90245) | Atomic multi-operation execution; calls CorkAdapter for slippage-protected operations |
| Uniswap Permit2       | [`0x000000000022D473030F116dDEE9F6B43aC78BA3`](https://etherscan.io/address/0x000000000022D473030F116dDEE9F6B43aC78BA3) | Signature-based token approvals                                                       |
| Safe CREATE2 Deployer | [`0x914d7Fec6aaC8cd542e72Bca78B30650d45643d7`](https://etherscan.io/address/0x914d7Fec6aaC8cd542e72Bca78B30650d45643d7) | Deterministic deployment across chains                                                |

## Active Markets

Each Cork Pool is identified by a `MarketId` and defines a pair of a Collateral Asset (CA) and a Reference Asset (REF). When a pool is active, users can Deposit CA to receive Cork Principal Token (cPT) and Cork Swap Token (cST).

{% hint style="info" %}
Market-specific cPT and cST token addresses are deployed per-pool by the SharesFactory. Query the CorkPoolManager's `shares(MarketId)` function to retrieve the cPT and cST addresses for a specific market.
{% endhint %}

### vbUSDC / sUSDe

| Parameter             | Value                                                                                                                            |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| MarketId              | _Query `CorkPoolManager.getId(collateralAsset, referenceAsset)` on-chain_                                                        |
| Collateral Asset (CA) | vbUSDC ([`0x53E82ABbb12638F09d9e624578ccB666217a765e`](https://etherscan.io/address/0x53E82ABbb12638F09d9e624578ccB666217a765e)) |
| Reference Asset (REF) | sUSDe ([`0x9D39A5DE30e57443BfF2A8307A4256c8797A3497`](https://etherscan.io/address/0x9D39A5DE30e57443BfF2A8307A4256c8797A3497))  |
| Morpho Oracle         | [`0x5D3159ba95dCdE02451a31fE68B08fB650b00458`](https://etherscan.io/address/0x5D3159ba95dCdE02451a31fE68B08fB650b00458)          |

{% hint style="info" %}
The MarketId is a `bytes32` hash derived from the Collateral Asset and Reference Asset addresses. Query it on-chain or compute it using `keccak256(abi.encode(collateralAsset, referenceAsset))`.
{% endhint %}

## Architecture Overview

Cork Phoenix uses a **singleton architecture** — all pool states are managed by a single CorkPoolManager contract. This differs from factory-per-pool patterns and provides gas efficiency and unified state management.

```
User --> Morpho Bundler3 --> CorkAdapter --> CorkPoolManager
                                                  |
                                            SharesFactory
                                             /        \
                                           cPT        cST
```

* **CorkPoolManager** is the core singleton. All operations (Deposit, Mint, Withdraw, Redeem, Swap, Exercise, and their Unwind/Other variants) are executed here.
* **CorkAdapter** provides slippage protection and deadline checks on CorkPoolManager operations. It is called by Morpho Bundler3, which provides atomic multi-operation execution.
* **DefaultCorkController** manages governance: pool creation, fee updates, pausing, treasury, and whitelist management.
* **WhitelistManager** enforces per-market access control on pool operations.

{% hint style="warning" %}
While you can interact with CorkPoolManager directly, it does not provide slippage or deadline protection. Use CorkAdapter for all production interactions.
{% endhint %}

## Source Code

The Cork Phoenix smart contracts are open source and available at [github.com/Cork-Technology/phoenix](https://github.com/Cork-Technology/phoenix).

For audit reports, see [Audits](audits.md).
