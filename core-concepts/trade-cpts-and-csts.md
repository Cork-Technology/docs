---
hidden: true
---

# Trade cPTs & cSTs

In our system, the AMM is where users can buy and sell their Depeg Swaps and Cover Tokens. Because of the particular design of our system, we have implemented a customized AMM which operates a bit differently from how users are familiar with AMMs.&#x20;

## AMM Model

The token pair in the AMM in our system is Cover Token and Redemption Asset. To trade Depeg Swaps, there is a flash swaps mechanism (explained further below). What this design enables is that the market for both Cover Tokens as well as Depeg Swaps are unified in the same market, which reduces liquidity fragmentation and improves yields for liquidity providers.&#x20;



## **Liquidity curve**

In most normal market conditions, the Cover Token and the Redemption Asset are naturally tightly correlated, with the price of the Cover Token gradually trading closer and closer to the Redemption Asset as you approach expiry (since it can be redeemed for Redemption Asset at expiry). However, the protocol is also designed for black-swan-events and extreme volatility. The liquidity concentration can be concentrated because of the correlation, but has to be flexible enough to support a wider trading range in case of a depeg event. Therefore, we have selected the Yield Space curve as our liquidity curve, which can be described in the following formula:

$$
x^{1−t} + y^{1−t} = k
$$

This liquidity provision formula works in “yield space” rather than “price” space. Specifically, we want the interest rate - not the price - to be a pure function of reserves. The invariant&#x20;

$$
x_{start}^{1−t} +y_{start}^{1−t}
$$

is recomputed for each trade based on the current value of t, and the current reserves for x and y. This means the curve “pivots” around the point representing the current reserves. The price curve gradually flattens as maturity approaches providing optimal invariant for unbounded range implied APY discovery and incorporating natural time dependent price convergence/decay to maturity.&#x20;

#### AMM Implementation

To support our specific use case and allow for further customization and improvements we have developed our AMM in Uniswap V4 through a custom hook. Using UniV4 enables gas optimization, routing advantages and full flexibility to further enhance our design to improve capital efficiency.

#### **Fee model**

In our AMM the fee model follows a Linear Decay Function as described below:

$$
\text{Fee}(T) = \text{Base Fee} \times {T}
$$



If the Base Fee = 0.5% at 1 year-to-maturity then it lowers to Fee(0.25) = 0.25% at half-year to maturity etc. where T is time to maturity normalized to years. This is important because of the value decay of our Depeg Swaps and subsequent value accrual of the Cover Token makes it such  that if fees are constant, there is an escalating fee burden on both sides. For the Cover Token, for example a 3% annual risk premium will give Cover Token holders 3% yield per year. In this example, at 1 year to expiry, the Cover Token is worth 0.97, if the Cover Token is paying a 0.3% fee to sell their position it would equate to 0.003, roughly 10% of their annualized yield (0.03). If the fee remains constant but we shorten the expiry to 6 months, the same 0.003 is paid, however proportionally higher to their annualized yield of 0.015. If the fee therefore would be scaled with the linear decay function as described, the fee would be 0.0015, still representing 10% of the yield.&#x20;



For the Depeg Swap side, the scaling has a different implication because of the flash swaps mechanism, the lower the price of the Depeg Swap is, the higher the implied leverage is in each trade. For example, if a user buys 10 Depeg Swaps at a price of 0.1 ETH (total value 1 ETH), this has the net effect of creating 10 Cover Token of sell pressure in the AMM, which applies the fee. If the same Depeg Swap is closer to expiry, such that 1 ETH now buys 20 Depeg Swaps, the same 1 ETH buy would create 20 Cover Tokens of sell pressure. As a result, because of the linear decay function these two trades would have the same fee outcome instead of the latter paying double the fees to transact the same amount of ETH.&#x20;



## **Flash Swaps Router**

When you buy or sell Depeg Swaps from the AMM, these are facilitated through the Flash Swaps Router. Since Depeg Swaps are not trading directly in the AMM, the router automates a "flash swap" transaction which is described below. The router also is the mechanism through which the Liquidity Vault makes it's Depeg Swaps available to the market. The way it does this is by the Liquidity Vault depositing its Depeg Swaps into the router, where a % (initially 50% but configurable) of all buying volume through the router is filled by Depeg Swaps from the Vault. This way, the Liquidity Vault can unload its Depeg Swaps into the market, without dumping price and users receive coverage according to the market price with less fees and price impact. The router becomes a unified contract through which integrators can easily trigger buying and selling orders against Depeg Swaps without navigating the complexity introduced in the background by flash swaps.&#x20;



Through the Flash Swaps Router, the following functions can be triggered against the AMM:

**Buying Depeg Swaps:**

1. Buyer sends Redemption Asset into the swap contract
2. A contract withdraws more Redemption Asset from the AMM
3. The Redemption Asset is used to mint Cover Token and Depeg Swap
4. Depeg Swap is sent to the buyer
5. The Cover Token is sold for Redemption Asset to return the amount from step 2

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

**Selling Depeg Swaps:**

1. Seller sends Depeg Swap into a swap contract
2. The contract borrows an equivalent amount of Cover Token from the pool
3. The Depeg Swap and Cover Token are used to redeem the Redemption Asset
4. Redemption Asset is sent to the seller (or routed to any major tokens)
5. A portion of the Redemption Asset is sold to the pool for Cover Token to return the amount from step 2

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

**Buying & selling Cover Token**

1. Sell Cover Token: Cover Token is swapped for Redemption Asset and returned to the seller
2. Buy Cover Token: Redemption Asset is deposited into the AMM for Cover Token that is returned to the seller



The market for the Depeg Swap and Cover Token is inversely related because 1 Cover Token + 1 Depeg Swap = 1 Redemption Asset. For example if the price of a Depeg Swap goes up, the price of a Cover Token goes down, this means the yield for Cover Token holders increases and the cost to receive protection with Depeg Swaps goes up.





