---
hidden: true
---

# Overview

### Overview of the Smart Contracts and Architecture

Here is a brief overview of the Cork smart contracts for EVM:&#x20;

**Main contracts:**

* The Cork Configuration contract (**`CorkConfig`**) - from where all other smart contracts read configuration data from. This also serves as the entrypoint for integrations.
* The Cork Peg Stability Module (PSM) and the Cork Liquidity Vault are bundled into a singleton contract (**`ModuleCore`**). This contract contains the bulk of the protocol’s logic.
* The Cover Tokens (**`CT`**), Liquidity Provider (**`LP`**) tokens, and Liquidity Vault (**`LV`**) tokens are standard **ERC20 contracts** that represent shares in the PSM pool, the AMM pool, and the Cork Vault respectively. These ERC20 contracts, along with the Depeg Swap (**`DS`**) token are deployed by a Factory/Manager contract (**`AssetFactory`**).
* The Protected Unit (**`PRO`**) tokens are **ERC4626 contracts** which have been extended to support two underlying tokens (pegged asset and depeg swap) and are deployed by a Factory/Manager contract (**`HedgeUnitFactory`**).
* The Cork Router contract (**`Router`**) draws inspiration from the Router contracts of Uniswap V2 and Curve Finance. While the bonding curve formulas (yield space) remain largely similar, with only minor modifications, the Router’s smart contract interfaces have undergone customization to meet our specific requirements for Flash swaps of Depeg Swap (**`DS`**) tokens.
* The AMMs are built on vanilla Uniswap V4 contracts which have not been modified, but are extended using **UniV4-standard hooks** (**`CorkHook`**).

**Helper contracts:**

* The Liquidator Helper contract (**`Liquidator`**) uses Cowswap to liquidate pegged assets during each rollover.
* The Withdrawal Helper contract (**`Withdrawal`**) uses a timelock contract pattern to hold tokens that are pending withdrawals from the liquidity vault.
* The Hedge Unit Router contract (**`HedgeUnitRouter`**) provides an optional entrypoint to wrap multiple assets into Hedge-Units in a single transaction, as an alternative to using widely-available `Multicall` contracts.

**Implementation Notes:**

* Computations/calculations are segregated into Solidity libraries to allow full testability following industry best practice.
* Any solidity code with side-effects (i.e. changes state) are written separately into separate contracts using minimal lines of code to improve readability.
* An entrypoint contract ensures that older contracts are immutable so that previous invariants are enforced in full, even after a major version upgrade.



### Source Code

The source-code is publicly available [here on Github](https://github.com/orgs/Cork-Technology/repositories).
