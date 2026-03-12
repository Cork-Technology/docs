---
description: How whitelist access control works in Cork Pools
---

# Whitelist Functionality

Cork Pools can optionally restrict access through a whitelist. When enabled, only whitelisted addresses can interact with that market.

---

## How It Works

Each Cork Pool can be configured with whitelist enforcement at creation. When enabled:

- **All operations are restricted** — only whitelisted addresses can interact with the market (Deposit, Exercise, Withdraw, Redeem, Unwind)
- An address is considered whitelisted if it is on the **global whitelist** OR the **market-specific whitelist**

{% hint style="info" %}
If a market's whitelist is disabled (or was never enabled), all addresses can interact with it. Public markets cannot later be whitelisted — the whitelist can only be disabled, not enabled after creation.
{% endhint %}

---

## Why Whitelisting Exists

Cork introduced whitelisting for two reasons:

1. **Security** — Restricting deposits to known addresses reduces the attack surface on whitelisted markets
2. **Institutional requirements** — Certain institutional use cases require permissioned access to comply with regulatory or operational requirements

---

## Checking Whitelist Status

### On-Chain

Query the WhitelistManager (`0xcCccCcCccCC6e38a2772Eb42D2f408eeB89cb0eE`):

```solidity
// Combined check — returns true if whitelist is disabled OR address is whitelisted
function isWhitelisted(MarketId poolId, address account) external view returns (bool)

// Individual checks
function isMarketWhitelistEnabled(MarketId poolId) external view returns (bool)
function isGlobalWhitelisted(address account) external view returns (bool)
function isMarketWhitelisted(MarketId poolId, address account) external view returns (bool)
```

Use `isWhitelisted` for pre-flight checks before deposits. It handles all cases: if the whitelist is disabled, it returns `true` for any address.

### Via API

```
GET /v1/pools/whitelisted-addresses?chainId=1&walletAddress=0x...
```

Returns whitelist status per pool. See [API Reference](../developers/api-reference.md) for details.

---

## Getting Whitelisted

If a market has whitelisting enabled and you need access, contact the Cork team to be onboarded. A governance multisig manages the whitelist through the DefaultCorkController.
