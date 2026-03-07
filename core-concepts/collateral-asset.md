---
hidden: true
---

# Collateral Asset

{% hint style="info" %}
**TLDR:** Redemption Assets provisioned into the Cork Protocol, not only help stabilize markets but also unlock new strategies for DeFi users, enabling safer leverage and offering profitable opportunities in adverse market conditions.
{% endhint %}

### Introduction

**Redemption Assets** play a crucial role in maintaining stability and liquidity within the ecosystem. They are high-liquidity tokens that [**Pegged Assets**](reference-asset.md) can be swapped for at some guaranteed ratio (or exchange rate) through the **Peg Stability Module**, provided the redeemer holds both a [**Pegged Asset**](reference-asset.md) and a corresponding [**Depeg Swap**](swap-token.md).



This system provides two key benefits:

1\. **Liquidity Assurance:** Through [**Depeg Swaps**](swap-token.md), the [**Pegged Assets**](swap-token.md) inherit the deep liquidity of Redemption Assets, significantly reducing the risk of liquidation in lending markets.

2\. **Depeg Protection:** In the event of a depeg, holders of [**Depeg Swaps**](swap-token.md) can secure guaranteed value by redeeming [**Pegged Assets**](reference-asset.md) for Redemption Assets, making [**Depeg Swaps**](swap-token.md) an effective hedge against volatility.

***

### Redemption Process

A Redeemer can deposit 1 **Pegged Asset** + 1 **Depeg Swap**, to receive a specific amount of the **Redemption Asset** from the **Peg Stability Module**, minus a small redemption fee. This effectively lets anyone holding a [**Depeg Swap**](swap-token.md) to redeem the [**Pegged Asset**](reference-asset.md) for the **Redemption Asset** at the guaranteed exchange rate (or at a `1:1` ratio).

$$
Depeg\ Swap + Pegged\ Asset = Redemption\ Asset
$$

***

### Impact on Lending Markets

When a **Depeg Swap** exists, the **Pegged Asset** inherits the **Redemption Asset**’s liquidity because of the guaranteed swap at the pre-determined exchange rate via the **Peg Stability Module (PSM)**. This has a significant impact on how the **Pegged Asset** is viewed in lending markets:

* **Redemption Asset Liquidity:** The Redemption Asset typically has much deeper liquidity (orders of magnitude more) than the **Pegged Asset**.
* **Reduced Liquidation Risk:** Since the **Pegged Asset** can always be swapped for the more liquid Redemption Asset at a `1:1` ratio (or at the redemption exchange rate), lending markets are protected from shortfalls during minor depegs.
* **Higher Loan-To-Value (LTV):** With liquidation risks mitigated, lending markets can offer higher LTV ratios. This allows users to loop (repeatedly borrow and deposit) with less risk of forced liquidation.

***

#### Example: LRT-Collateralized Lending Market

1. A user deposits _ezETH_ as collateral to borrow _ETH_.
2. The user trades that borrowed _ETH_ for more _ezETH_ and repeats the process.
3. In a typical scenario, a slight depeg of _ezETH_ would trigger liquidation.
4. However, with a **Depeg Swap**, the user can always swap _ezETH_ back to _ETH_ at a 1:1 ratio, avoiding liquidation risk and enabling safer leverage.

***

### Impact on Return Potential of Depeg Swaps

Large holders of the **Pegged Asset** often face challenges liquidating big positions due to limited liquidity. **Depeg Swaps** may provide a guaranteed `1:1` exit route to the Redemption Asset, extending their utility during adverse market conditions beyond simple depeg hedging.&#x20;

* **Redemption:** **Depeg Swap** holders can redeem the Redemption Asset through the **Peg Stability Module**.
* **Value Determination:** The value of a **Depeg Swap** is based on the price difference between the **Redemption Asset** and the **Pegged Asset**, as well as the perceived risk of further depeg.
* **Profit Potential:** Buying **Depeg Swaps** early at a lower cost (before a depeg event), can yield significant returns if the depeg occurs, highlighting their leveraged upside.
* **Risk Pricing:** Trading of **Depeg Swaps** effectively function as a market for pricing the risk of further depegs.

***

#### Example: Gains from Depeg Swaps (LRT to ETH pair)

* Suppose the **Pegged Asset** (LRT) drops to 0.8 ETH.
* A single **Depeg Swap** would then be worth at least 0.2 ETH (reflecting the fair value of the `1:1` redemption).
* If an investor initially purchased the **Depeg Swap** for 0.01 ETH, they would see a `20x` gain if the depeg event occurs.
* This reflects the leveraged upside of a **Depeg Swap**, similar to how _Credit Default Swaps (CDS)_ paid out on loan defaults during the 2008 financial crisis.

***

### Conclusion

To sum up, **Redemption Assets** provisioned into the Cork Protocol, not only help stabilize markets but also unlock new strategies for DeFi users, enabling safer leverage and offering profitable opportunities in adverse market conditions.
