# How to Deploy Liquidity

## Deploying Liquidity to a Cork Vault

Deploying liquidity on Cork V1 is permissionless. Cork Vaults are a good destination for deploying liquidity due to its:

* **Choice of Pair or Market** via parameters such as the [Pegged Asset](../../../core-concepts/reference-asset.md) and [Redemption Asset](../../../core-concepts/collateral-asset.md)
* **Allocation of 0% - 50% to PSM pool** servicing the current term/epoch
  * **Collect Premiums** from sale of Depeg Swaps
  * **Collect Revenues** from Redemption fees and Repurchase fees
* **Allocation of 50% - 100% to AMM pool** facilitating the trading of [Cover Tokens](../../../core-concepts/principal-token.md) and [Depeg Swap](../../../core-concepts/swap-token.md) Tokens of the current term/epoch
  * **Collect Revenues** from Trading fees
  * **Collect Premiums & Revenues** from the PSM by providing AMM Liquidity in the Optimal Asset Ratio. No impermanent loss if held to expiry.
* **Automated Rollovers** that seamlessly deploy liquidity over an extended period, across every successive term/epoch
* **Transferrable LV Tokens** that are standard ERC20 shares representing fractional vault ownership
* **Gas-efficient** architecture

Cork Vaults simplify liquidity deployment by eliminating the need for manual redeployment after the expiry of each term/epoch. Each Cork Vault only serves a specific market, limiting risk exposure.

***

## Vault / Market Parameters

The protocol enables the deployment of liquidity to isolated markets by specifying:

* one pegged asset,
* one redemption asset,
* a Coverage Term Expiry Interval (CTEI) or term duration,
* an initial Annual Risk Premium (ARP),
* and a Redemption Exchange Rate Provider (optional).

Each market is defined by a unique combination of these parameters, which also determines the specific PSM or AMM pools where each Cork Vault allocates liquidity.

Cork Vaults are created when a market is initialized or opened. To create a Cork Vault, see this guide: [How to Open a Market](how-to-open-a-market.md)

{% hint style="info" %}
For detailed market parameters, refer to our [Market Parameters Document](https://corkfi.notion.site/Launch-market-parameters-183328fa34fd80bea8c9fdb36d808474).
{% endhint %}

***

## Liquidity Deployment Options

**This guide covers two approaches to deploying liquidity:**

1. [Deploy liquidity to a Cork Vault](how-to-deploy-liquidity.md#guide-deploy-liquidity-to-a-cork-vault)
2. [Deploy short-term liquidity to a Cork AMM (coming soon)](how-to-deploy-liquidity.md#guide-deploy-short-term-liquidity-to-a-cork-amm)



***

## Guide: Deploy liquidity to a Cork Vault

### 1. Jump on the Cork `ModuleCore` contract[​](https://docs.morpho.org/morpho/tutorials/market-creation#1-jump-on-the-morpho-contract) <a href="#id-1-jump-on-the-morpho-contract" id="id-1-jump-on-the-morpho-contract"></a>

See the [deployment address section](../../../smart-contracts/phoenix/live-deployments.md).&#x20;

Copy the `CorkConfig` contract address into a network block explorer (e.g. [etherscan.io](https://etherscan.io)).

E.g: Ethereum mainnet address is [`0xF0DA8927Df8D759d5BA6d3d714B1452135D99cFC`](https://eth.blockscout.com/address/0xF0DA8927Df8D759d5BA6d3d714B1452135D99cFC?tab=contract)



Navigate to the right page:

* Go to `Contract` -> `Read Contract` page of `CorkConfig`.
* Scroll down to `moduleCore`, then expand its section and open its address (in the network block explorer). Copy this address somewhere as you will need it in the next step.
* Go to `Contract` -> `Read as Proxy` page of `ModuleCore`.



### 2. Approve Spending of the RA Token <a href="#id-2-fill-all-attributes" id="id-2-fill-all-attributes"></a>

Jump on the `markets` function of `ModuleCore`.

* `id`: paste the id of the pair or market.

{% hint style="info" %}
The id of the pair or market is available from the dApp interface at [https://app.cork.tech](https://app.cork.tech), or from the market creator (for new and unvetted markets).
{% endhint %}

This function will return all market parameters of the pair `id`. Before continuing, ensure that all five market parameters meet your expectations.

{% hint style="info" %}
For a better understanding of each market parameter, refer to the [Market Parameters](how-to-deploy-liquidity.md#vault-market-parameters) section above.
{% endhint %}

Open the `ra`  [redemption asset](../../../core-concepts/collateral-asset.md) address (in the network block explorer).



#### Make the Approval

Go to `Contract` -> `Write Contract` page.

Jump on the `approve` function

* `spender`: paste the address of the Cork `ModuleCore` contract,
* `value`: paste the amount that you would like to deposit / deploy to the vault.

{% hint style="info" %}
Ensure amounts account for the [redemption token’s](../../../core-concepts/collateral-asset.md) ERC20 decimals (e.g., for 6 decimals, 1 token is represented as `1000000`).
{% endhint %}

Connect your wallet and proceed to sign the transaction. Wait for at least one confirmation (of the `approve` transaction) before moving to the next step.



### 3. Fill all attributes[​](https://docs.morpho.org/morpho/tutorials/market-creation/#2-fill-all-attributes) <a href="#id-2-fill-all-attributes" id="id-2-fill-all-attributes"></a>

Return to the Cork `ModuleCore` contract page in the network block explorer.

Go to `Contract` -> `Write as Proxy` page of `ModuleCore`.

Jump on the `depositLv` function

* `id`: paste the id of the pair or market,
* `amount`: paste the amount that you would like to deposit / deploy to the vault,
* `raTolerance`: input `0` in this field.
* `ctTolerance`: input `0` in this field.

{% hint style="info" %}
Ensure amounts account for the [redemption token’s](../../../core-concepts/collateral-asset.md) ERC20 decimals (e.g., for 6 decimals, 1 token is represented as `1000000`).
{% endhint %}

{% hint style="info" %}
The `raTolerance` and `ctTolerance` set the minimum expected amounts of the redemption asset and cover token when adding liquidity to the AMM pool, triggering a revert if slippage exceeds acceptable levels.
{% endhint %}

{% hint style="info" %}
For detailed market parameters, refer to our [Market Parameters Document](https://corkfi.notion.site/Launch-market-parameters-183328fa34fd80bea8c9fdb36d808474).
{% endhint %}

### 4. Sign the transaction[​](https://docs.morpho.org/morpho/tutorials/market-creation/#3-sign-the-transaction) <a href="#id-3-sign-the-transaction" id="id-3-sign-the-transaction"></a>

Connect your wallet and proceed to sign the transaction. You are done.



***

## Guide: Deploy short-term liquidity to a Cork AMM

Coming soon!
