# Overview

### How does Cork work

Markets on Cork V1 are trustless and enables the protection of an asset against depeg risks.

#### Buy Protection

To buy protection, users acquire Cork Depeg Swap (DS) tokens from the protocol’s built-in AMM Pools and Routers. Holding these DS tokens is similar to having a term insurance policy, offering protection or benefits for a specified duration until the coverage term expires. Both on-chain contracts and user accounts holding DS tokens can claim or redeem benefits by exchanging (de)pegged assets for redemption assets at the current redemption exchange rate, anytime before the term expires. Ownership of the exchanged pegged assets is transferred to the market participants who initially contributed to the insurance benefit pool (explained below).

#### Provide Protection

To provide term coverage or protection, market participants deposit redemption assets into insurance benefit pools known as Peg Stability Module Pools. For each deposit, Cork Cover Tokens (CT) and Cork Depeg Swap (DS) tokens are minted and can be freely traded with other users. Upon term expiry, Cover Tokens can be used to withdraw the original deposits, minus any amounts already exchanged for (de)pegged assets due to redemptions.

Since each term has a limited duration, providing deposits can be a labor-intensive process requiring recurring transactions. To streamline this, the protocol offers Cork Vaults, where liquidity can be deployed and automatically rolled over into subsequent terms within the same market (i.e. with the same market-pair parameters such as pegged asset and redemption asset). Some of this liquidity is also used to bootstrap AMM pools to enable trading without a need for trusted counterparties.



***

### Why integrate with Cork

Cork V1 is a good destination for creating peg stability pools due to its:

* **Flexible PSM pool design** through
  * dynamic redemption rates or exchange rates,
  * configurable coverage term length, and
  * parameterized annual risk premiums
* **Built-in AMMs** with concentrated liquidity trading of protocol-issued tokens, unlocking capital efficiency
* **Built-in Vaults** that allow liquidity to be deployed seamlessly, across separate coverage terms/epochs, spread over an extended time
* **Built-on Proven Protocols** like Uniswap V4 and Cowswap
* **Built-in Composability** with other DeFi Protocols
* G**as-efficient** architecture



The protocol is permissionless and designed to facilitate adoption, by enabling users to instantly buy and sell protective tokens (Cork Depeg Swaps) upon permissionless market creation and liquidity deployment. The pools are optimized for efficiency and flexibility, supporting composability with other decentralized platforms while offering varied options for curators (with diverse risk preferences).



For more information, builders and integrators should see \[[Cork V1 Quickstart](quickstart/)].
