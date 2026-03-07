# Collateral Asset

{% hint style="info" %}
**TL;DR:** The Collateral Asset (CA) is the liquid, high-quality asset deposited into a Cork Pool to back all positions. It is the asset that Cork Swap Token holders receive when they exercise, and it forms the principal backing that Cork Principal Token holders have a claim on.
{% endhint %}

### Introduction

Every Cork Pool is defined by a pair of assets: a **Collateral Asset** and a [**Reference Asset**](reference-asset.md). The Collateral Asset serves as the foundation of the pool — it is the asset deposited by liquidity providers and locked in the CorkPoolManager to collateralize positions.

The Collateral Asset is typically a yield-bearing asset such as sUSDS, wstETH, or sDAI. By choosing a high-quality, liquid Collateral Asset, Cork Pools ensure that swaps can be exercised against deep, reliable liquidity.

***

### How It Works

Cork Pools require a high-quality, liquid asset to guarantee that the Swap operation can always be exercised. The Collateral Asset fills this role.

When a liquidity provider deposits the Collateral Asset into a Cork Pool, the CorkPoolManager locks it and mints two tokens in return:

$$
Collateral\ Asset \rightarrow Cork\ Principal\ Token + Cork\ Swap\ Token
$$

The locked Collateral Asset backs both instruments. The [Cork Swap Token](swap-token.md) (cST) gives its holder the right to exchange Reference Asset + cST for the Collateral Asset before expiry. The [Cork Principal Token](principal-token.md) (cPT) represents proportional ownership of whatever remains in the pool at expiry — which may include both Collateral Asset and Reference Asset if swaps were exercised.

***

### Key Properties

- **Locked on Deposit** — When the Collateral Asset is deposited, it is locked in the CorkPoolManager and cannot be withdrawn until the position is unwound (via UnwindDeposit) or the pool expires (via Withdraw).
- **Backs the Swap guarantee** — The Swap operation (REF + cST → CA) draws directly from the locked Collateral Asset, minus an exercise fee.
- **Yield accrual** — If the Collateral Asset is yield-bearing (e.g., sUSDS, wstETH), that yield accrues within the pool and benefits cPT holders. The exchange rate in the pool adjusts to prevent this yield from being arbitraged through swaps.
- **Reconstitution** — Before expiry, cPT + cST can always be combined to recover the original Collateral Asset deposit, regardless of market conditions.

***

### Relationship to Other Components

The Collateral Asset connects to every major component of a Cork Pool:

- **CorkPoolManager** — Holds and manages all locked Collateral Asset balances. The Deposit operation accepts CA, and the CorkPoolManager distributes it on Repurchase or Withdraw.
- **Cork Swap Token (cST)** — Exercising a cST via the Swap operation unlocks Collateral Asset from the pool. The amount is determined by the cST quantity, adjusted by the exchange rate and exercise fee.
- **Cork Principal Token (cPT)** — At expiry, cPT holders use the Withdraw operation to redeem their proportional share of remaining Collateral Asset (and any Reference Asset that entered the pool through exercised swaps).
- **Exchange Rate** — Governed by a rate oracle and constraint parameters (min, max, rate-of-change limits), the exchange rate ensures yield differentials between the Collateral Asset and Reference Asset are accounted for, keeping the pool fair to cPT holders.

***

### Example

Consider a Cork Pool with **sUSDS** as the Collateral Asset and a **USDC Vault Token** as the Reference Asset, with a 1:1 exchange rate:

1. A liquidity provider deposits **100 sUSDS** into the Cork Pool.
2. The CorkPoolManager locks the 100 sUSDS and mints **100 cPT + 100 cST** (returned to the depositor).
3. The LP sells the 100 cST for a premium — say **2 sUSDS** — to a hedger who wants coverage on the USDC Vault Token.
4. If the hedger exercises 50 cST via the Swap operation, they deposit **50 USDC Vault Tokens + 50 cST** and receive approximately **50 sUSDS** from the pool (minus the exercise fee). The exact Reference Asset amount depends on the current exchange rate.
5. At expiry, the cPT holder uses Withdraw to redeem their 100 cPT for a proportional share of what remains: **50 sUSDS + 50 USDC Vault Tokens**.

The Collateral Asset (sUSDS) is the anchor throughout — it backs the swap, generates yield for the LP, and is the asset hedgers receive on exercise.
