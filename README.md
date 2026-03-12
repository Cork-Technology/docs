# What is Cork?

**TL;DR**\
Cork is a programmable risk layer for onchain assets such as vault tokens, yield-bearing stablecoins, and liquid (re)staking tokens. Cork's core primitive enables asset managers and issuers to spin up custom swap markets that enhance redemption liquidity, risk transparency, and market confidence for their onchain assets. Backed by a16z CSX, OrangeDAO, Road Capital, BitGo, G-20, and Steakhouse Financial, Cork is building the risk infrastructure needed to bring institutional capital into onchain credit markets.

***

## How Cork Works

Each **Cork Pool** pairs a [Collateral Asset](core-concepts/collateral-asset.md) (CA) — a liquid, yield-bearing asset like sUSDS or wstETH — with a [Reference Asset](core-concepts/reference-asset.md) (REF) — the asset whose risk is being managed, such as a vault token or RWA.

When a liquidity provider deposits the Collateral Asset, the pool mints two tokens:

- **[Cork Principal Token](core-concepts/principal-token.md) (cPT)**: Represents the depositor's principal claim. At expiry, cPT holders redeem their proportional share of whatever remains in the pool.
- **[Cork Swap Token](core-concepts/swap-token.md) (cST)**: Grants the right to exercise — exchange REF + cST for CA before expiry. This is the coverage instrument.

$$
Collateral\ Asset \rightarrow Cork\ Principal\ Token + Cork\ Swap\ Token
$$

The cPT holder earns yield (from the Collateral Asset and from selling cST at a premium). The cST holder gets guaranteed redemption liquidity — regardless of market conditions, they can always exchange their Reference Asset for the Collateral Asset through the Cork Pool.

Before expiry, cPT + cST can always be converted back to the original Collateral Asset:

$$
Cork\ Principal\ Token + Cork\ Swap\ Token = Collateral\ Asset
$$

***

## What Cork Enables

Cork Pools serve as a flexible primitive for managing onchain risk. Key applications include:

- **Liquidity buffers** — Instant liquidity for vaults and bridges with high utilization rates or withdrawal delays
- **Duration coverage** — Instant exit for assets with long redemption windows (RWAs with 40+ day queues)
- **Risk hedging** — Coverage against value loss for stablecoins, LSTs, LRTs, and vault tokens
- **Peg stability** — Guaranteed redemption liquidity strengthens issuer peg resilience
- **Protected loops** — Cork Pools enable looping vaults to self-liquidate atomically

See [Target Assets & Use Cases](core-concepts/target-assets-and-use-cases.md) for detailed examples.

***

## Why Tokenized Risk?

As onchain adoption accelerates and institutional capital enters the space, risk infrastructure is emerging as a foundational requirement. As of early 2026, the stablecoin market has more than doubled since early 2024 (from $130B to over $300B), while tokenized real-world assets have surged from $1B in 2021 to $35B (source: RWA.xyz). In 2025, tokenization entered mainstream finance with Nasdaq approving trading of tokenized stocks and ETFs.

Yet robust tools for identifying, pricing, and transferring risk onchain remain underdeveloped. Events like the Terra–Luna failure ($40B+ vaporized, triggering contagion across major institutions) and the October 2025 liquidation event (when multiple pegged assets traded below parity on centralized venues) demonstrated that risk management cannot be an afterthought.

For institutional players to meaningfully participate in DeFi, and for the tokenization of RWAs, credit, and stablecoins to scale, onchain finance needs programmable, transparent, and composable risk primitives. Cork is building that infrastructure — making onchain risk explicit and tradable.
