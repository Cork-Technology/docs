# Frequently Asked Questions

#### What is duration risk and how does Cork solve for it?

Certain assets, such as vault shares or Real World Assets (RWAs) often have limited secondary liquidity and primarily are liquid through a redemption mechanism. However, when such assets have a long time-delay between when you request a redemption and a payout is complete, you as a user are faced with what is called duration risk. Duration has a big impact on the ability for these assets to be used in DeFi, as generally DeFi is not designed to handle duration risk (yet). Cork provides the ability to create Swap Tokens which enable the direct exchange of a vault share or RWA for a liquid collateral asset, thereby allowing users to pay for coverage against duration risk. This can enable long duration assets to be used across DeFi for example in lending markets.

#### What is a Depeg Event?

A depeg event occurs when a pegged asset, like a stablecoin or liquid staking token, deviates from its intended value due to market volatility, liquidity issues, or external shocks. Some depegs are temporary (e.g., USDC during the SVB collapse), while others are permanent (e.g., UST).

### Why are you building Cork?

For DeFi to become the backbone of finance, trusted by large scale institutions, it needs risk management infrastructure. We are building Cork to enable the creation of risk markets to help accelerate the adoption of DeFi.

### Who is the team behind Cork?

Cork Protocol has been built by Cork Protocol Inc., a Delaware company, and is operated by Cork Labs, a Cayman Islands company. Cork is founded by three serial entrepreneurs, who all have built out DeFi projects in the past. Cork is backed by a16z CSX, OrangeDAO, Road Capital, BitGo, G-20, and Steakhouse Financial

To learn more about our team, visit our website:\
[https://www.cork.tech/about/](https://www.cork.tech/about/)

### Who would use Cork?&#x20;

Cork is primarily designed for institutional and protocol users.&#x20;

### What determines the price of a Swap Token or Principal Token?

The current Cork markets are all trading through OTC limit orders, where the pricing is set between two counterparties separately and executed onchain. The price of a Swap Token and Principal Token are always inversely related, with both adding up to 1 Collateral Asset. So if the premium increases, the price of the Swap Token increases and Principal Token decreases.

### What happens if the Collateral Asset is impaired?

Underwriters and to some extent Swap Token holders are exposed to the performance of the collateral asset. If it is impaired, the underwriter may face a loss when they receive back their now impaired Collateral Assets. The Swap Token holder will now only be able to swap out to an impaired asset, which might not be as vaulable which may reduce the value of the Swap Token.

### What are the risks of holding a Principal Tokens?

If there is an impairment of either the Reference Asset or the Collateral Asset, the underwriter holding Principal Tokens bears the loss.&#x20;

### What happens if when a Swap Token expires?

At expiry, the Swap Token can no longer be exercised and has no further value. The Principal Token can at expiry be used to redeem the collateral in the Cork Pool

### What is the Premium?

The premium is the annualized cost which Swap Token holders pay to Principal Token holders for their coverage. For example a 4% premium for a 3 month expiry would cost 0.04/4 = 0.01 (Collateral Asset).

### Where can I see my positions?

On the Cork dapp, connect your wallet and go to the Dashboard section. There you can see all your positions.

### Where is Cork Protocol deployed?

Cork is live on Ethereum Mainnet and the Sepolia Testnet.

<br>
