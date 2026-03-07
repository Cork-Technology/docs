---
hidden: true
---

# Formal Verification

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Has Cork undergone Formal Verification?

Yes, [**Runtime Verification**](https://runtimeverification.com/) conducted a formal verification audit on Cork Protocol in October 2024.

You can find the results here: [Runtime Verification Report](https://drive.google.com/file/d/1snmZ1d3JU8He0L42rD6yybCGQdrPxSjV/view)

The full announcement thread is available here: [https://x.com/rv\_inc/status/1891911866655338813](https://x.com/rv_inc/status/1891911866655338813)

***

## Verification Process and Tools

Our team uses [Kontrol](https://kontrol.runtimeverification.com/) to formally verify our Solidity smart contracts. [Kontrol](https://kontrol.runtimeverification.com/) enables developers to write property-based tests in Solidity and leverage symbolic execution to ensure these properties hold for all possible inputs and contract states.

The verification focused on the correctness of key components, including:

• [Peg Stability Module](../../core-concepts/cork-pool.md) ([GitHub](https://github.com/Cork-Technology/Depeg-swap/blob/main/contracts/core/Psm.sol))

• [Liquidity Vault](/broken/pages/I8JgHXnahZZTsSQtAobZ) ([GitHub](https://github.com/Cork-Technology/Depeg-swap/blob/main/contracts/core/Vault.sol))

• [Flash Swap Router](../../core-concepts/trade-cpts-and-csts.md) ([GitHub](https://github.com/Cork-Technology/Depeg-swap/blob/main/contracts/core/flash-swaps/FlashSwapRouter.sol))

• Associated library functions ([GitHub](https://github.com/Cork-Technology/Depeg-swap/tree/main/contracts/libraries))

These components were analyzed to ensure correctness and to formally prove that each function maintain desired properties.

***

## Technical Summary

We have published a technical summary for those interested in learning more about the work conducted to formally verify Cork's protocol:

* [Cork FV Summary Report (GitHub)](https://github.com/runtimeverification/publications/blob/main/reports/smart-contracts/Cork%20FV%20Summary%20Report.pdf)
* [Commits of Proofs by Runtime Verification's team (GitHub)](https://github.com/Cork-Technology/Depeg-swap/pull/272)
* [Formal Verification Report (Runtime Verification CI Platform)](https://kaas.runtimeverification.com/job/9df2b13d-bbbf-4b41-910e-448e0484d680/xml-report)

