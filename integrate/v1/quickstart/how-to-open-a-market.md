# How to Open a Market

TLDR: **Cork** is a trustless and efficient peg-stability primitive with permissionless market creation. Markets are parameterized and isolated.&#x20;

## Opening a New Market

Initializing a market on Cork V1 is permissionless and enables the protection of a pegged asset against depeg risks. Cork V1 is a good destination for creating peg stability functionality due to its:

* **Flexible PSM pool design** through
  * dynamic redemption rates or exchange rates,
  * configurable coverage term length, and
  * parameterized annual risk premiums
* **Built-in AMMs** with concentrated liquidity trading of protocol-issued tokens, unlocking capital efficiency
* **Built-in Vaults** that allow liquidity to be deployed seamlessly, across separate coverage terms/epochs, spread over an extended time
* **Built-on Proven Protocols** like Uniswap V4 and Cowswap
* **Composability** with other DeFi Protocols
* G**as-efficient** architecture

The protocol is trustless and was designed with quality-of-life features that make the buying and selling of protective tokens (i.e. Cork Depeg Swaps) immediately accessible to all users, right after market creation and liquidity deployment.



***

## Market Parameters

The protocol enables the deployment of isolated markets by specifying:

* one pegged asset,
* one redemption asset,
* a Coverage Term Expiry Interval (CTEI) or term duration,
* an initial Annual Risk Premium (ARP),
* and a Redemption Exchange Rate Provider (optional).

These parameters allows the pools to be efficient and flexible, allowing composability with other decentralized platforms, and adoption by various curators with different risk appetites.

{% hint style="info" %}
For exisiting market parameters, refer to our [Market Parameters Document](https://corkfi.notion.site/Launch-market-parameters-183328fa34fd80bea8c9fdb36d808474).
{% endhint %}

***

## Market Deployment Options

**This guide covers two approaches to deploying a pair or market:**

1. [Create a pair only](how-to-open-a-market.md#guide-create-a-pair-only)
2. [Create a pair with a custom exchange rate provider](how-to-open-a-market.md#guide-create-a-pair-with-a-custom-exchange-rate-provider)



***

## Guide: Create a Pair Only

### 1. Jump on the `CorkConfig` contract[​](https://docs.morpho.org/morpho/tutorials/market-creation#1-jump-on-the-morpho-contract) <a href="#id-1-jump-on-the-morpho-contract" id="id-1-jump-on-the-morpho-contract"></a>

See the [deployment address section](../../../smart-contracts/phoenix/live-deployments.md).&#x20;

Copy the `CorkConfig` contract address  into the network block explorer (e.g. [etherscan.io](https://etherscan.io)).

E.g: Ethereum mainnet address is [`0xF0DA8927Df8D759d5BA6d3d714B1452135D99cFC`](https://eth.blockscout.com/address/0xF0DA8927Df8D759d5BA6d3d714B1452135D99cFC?tab=contract)



### 2. Fill all attributes[​](https://docs.morpho.org/morpho/tutorials/market-creation/#2-fill-all-attributes) <a href="#id-2-fill-all-attributes" id="id-2-fill-all-attributes"></a>

Jump on the `initializeModuleCore` function

* `paToken`: paste the address of the pegged token,
* `raToken`: paste the address of the redemption token,
* `initialArp`: the initialArp is defined with 18 decimals. 1e18 represents an annual risk premium of 100%.
* `expiryInterval`: paste the expiry interval in seconds,
* `exchangeRateProvider`: copy and paste the address from th&#x65;_`defaultExchangeRateProvider`_ property of the CorkConfig contract.

{% hint style="info" %}
The `expiryInterval` a market creator is expecting to use has to be enabled. At first, the following expiry intervals have been approved: \[coming soon]
{% endhint %}

To align with the target Annual Risk Premium (ARP) immediately, it's advised to add $1 of liquidity. This not only ensures that the AMM pool's utilization starts at \[coming soon] but also prevents the rate from decreasing due to \[coming soon], which can happen if \[coming soon].

{% hint style="info" %}
It's recommended to batch the market creation with supply liquidity transactions to \[coming soon]. ~~Close monitoring is necessary until the market achieves substantial usage. This prevents rates from converging to either extremely low or high levels, which would take some time to return to reasonable levels.~~

Adhering to this practice will greatly improve the market user experience.
{% endhint %}



### 3. Sign the transaction[​](https://docs.morpho.org/morpho/tutorials/market-creation/#3-sign-the-transaction)

Connect your wallet and proceed to sign the transaction. You are done.



### 4. Optional: Confirm the market parameters

Wait for at least one confirmation (of the `initializeModuleCore` transaction) before moving on.

* Go to `Contract` -> `Read Contract` page of `CorkConfig`.
* Scroll down to `moduleCore`, then expand its section and open its address (page in the network block explorer).



#### Retrieve Pair or Market Id

To retrieve the market `id`, one has to paste the parameters into the `getId` function of the `ModuleCore` contract.

* Go to `Contract` -> `Read as Proxy` page of `ModuleCore`.

Jump on the `getId` function, and fill the same attributes as in [Step 2](how-to-open-a-market.md#id-2-fill-all-attributes).

This function will return the pair or market `id`.&#x20;

{% hint style="info" %}
Creating a market will also cause the `ModuleCore` contract to emit an event: `InitializedModuleCore` with the pair or market `id`
{% endhint %}



#### Retrieve Market Params of Id

To retrieve the market parameters from this `id`, one has to paste the `id` into the `markets` function of the `ModuleCore` contract.

* Go to `Contract` -> `Read as Proxy` page of `ModuleCore`.

Jump on the `markets` function:

* `id`: paste the id of the pair or market.

This function will return all market parameters of the pair `id`. Confirm that all parameters are correct.



***

## Guide: Create a Pair with a custom Exchange Rate Provider

Coming soon!
