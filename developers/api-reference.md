---
description: Cork Protocol REST API for market discovery, transaction history, and limit orders
---

# API Reference

**Base URL:** `https://api-phoenix.cork.tech`\
**Interactive docs:** [https://api-phoenix.cork.tech/docs](https://api-phoenix.cork.tech/docs)

Use on-chain reads (CorkPoolManager) for real-time state like swap rates and pool balances. Use this API for historical data, market discovery, and limit order management.

{% hint style="info" %}
Always include `chainId=1` in queries to filter to Ethereum mainnet. Other chain IDs may return testnet data.
{% endhint %}

## Pagination

All list endpoints return paginated responses with this structure:

```json
{
  "items": [...],
  "nextCursor": "abc123",
  "hasMore": true
}
```

Pass `nextCursor` as a query parameter to fetch the next page. When `hasMore` is `false`, you have reached the end of the results.

---

## Pools

### List Pools

```
GET /v1/pools/
```

Returns pool metadata, TVL, fee configuration, and pause status.

**Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID (use `1` for mainnet) |
| `poolId` | `string` | Filter by specific MarketId |
| `collateralAddress` | `string` | Filter by Collateral Asset address |
| `referenceAddress` | `string` | Filter by Reference Asset address |
| `principalAddress` | `string` | Filter by cPT token address |
| `swapAddress` | `string` | Filter by cST token address |
| `poolWhitelistStatus` | `string` | Filter by whitelist enabled/disabled |
| `expiryBefore` | `string` | ISO 8601 timestamp — pools expiring before this date |
| `expiryAfter` | `string` | ISO 8601 timestamp — pools expiring after this date |
| `limit` | `number` | Results per page (default: 2000, max: 2000) |
| `nextCursor` | `string` | Pagination cursor |

**Example:**

```bash
curl "https://api-phoenix.cork.tech/v1/pools/?chainId=1"
```

**Response shape:**

```json
{
  "items": [
    {
      "chainId": 1,
      "poolId": "0xab4988fb673606b689a98dc06bdb3799c88a1300b6811421cd710aa8f86b702a",
      "poolName": "sUSDe-vbUSDC-20APR2026",
      "expiry": "2026-04-20T00:00:00.000Z",
      "collateralToken": {
        "address": "0x9D39A5DE30e57443BfF2A8307A4256c8797A3497",
        "name": "Staked USDe",
        "symbol": "sUSDe",
        "decimals": 18
      },
      "referenceToken": {
        "address": "0x53E82ABbb12638F09d9e624578ccB666217a765e",
        "name": "Vault Batching USDC",
        "symbol": "vbUSDC",
        "decimals": 6
      },
      "principalToken": {
        "address": "0x...",
        "name": "Cork Principal Token ...",
        "symbol": "cPT-...",
        "decimals": 18
      },
      "swapToken": {
        "address": "0x...",
        "name": "Cork Swap Token ...",
        "symbol": "cST-...",
        "decimals": 18
      },
      "isWhitelistEnabled": true,
      "isDepositPaused": false,
      "isWithdrawPaused": false,
      "isSwapPaused": false,
      "isUnwindSwapPaused": false,
      "isUnwindDepositPaused": false,
      "exerciseFeePercentage": 0.01,
      "repurchaseFeePercentage": 0.01,
      "tvl": {
        "inUsdAtNow": 1234567.89,
        "inUsdAtEvents": 1200000.00
      },
      "collateralValue": {
        "inWei": "1000500000000000000000",
        "inToken": 1000.5,
        "inUsdAtNow": 1000.5,
        "inUsdAtEvents": 980.0
      },
      "referenceValue": {
        "inWei": "500000000",
        "inToken": 500.0,
        "inUsdAtNow": 500.0,
        "inUsdAtEvents": 490.0
      }
    }
  ],
  "nextCursor": null,
  "hasMore": false
}
```

**Key response fields:**
- `tvl.inUsdAtNow` — current USD value of all pool assets
- `collateralValue` / `referenceValue` — asset balances in wei, token units, and USD
- `inUsdAtNow` — current USD valuation
- `inUsdAtEvents` — USD valuation at the time of the last on-chain event
- `exerciseFeePercentage` / `repurchaseFeePercentage` — decimal fractions (0.01 = 1%). On-chain, fees use 18-decimal precision (`1e18 = 1%`) — see [CorkPoolManager > Fee Functions](contract-reference/cork-pool-manager.md#fee-functions)

### Whitelisted Addresses

```
GET /v1/pools/whitelisted-addresses
```

Returns pools with their whitelisted addresses.

**Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID |
| `poolId` | `string` | Filter by specific MarketId |
| `walletAddress` | `string` | Check if a specific address is whitelisted |
| `poolWhitelistStatus` | `string` | Filter by whitelist enabled/disabled |
| `limit` | `number` | Results per page (default: 2000, max: 2000) |
| `nextCursor` | `string` | Pagination cursor |

**Response shape:**

```json
{
  "items": [
    {
      "chainId": 1,
      "poolId": "0x...",
      "poolName": "sUSDe-vbUSDC-20APR2026",
      "isWhitelistEnabled": true,
      "whitelistedAddresses": [
        {
          "account": "0x49903609b8a22e1d9f7406d1306191e28c8ececd",
          "isGlobalWhitelisted": false,
          "isMarketWhitelisted": true
        }
      ]
    }
  ],
  "nextCursor": null,
  "hasMore": false
}
```

---

## Flows (Transaction History)

### List Flows

```
GET /v1/flows/
```

Returns user transaction history across Cork Pool operations.

**Parameters:**

| Parameter | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID |
| `walletAddress` | `string` | Filter by user address |
| `poolId` | `string` | Filter by specific MarketId |
| `actionType` | `string` | Filter by operation type: `exercise`, `repurchase`, `redeem`, `mint`, `unwind` |
| `fromTimestamp` | `string` | ISO 8601 — transactions after this time |
| `toTimestamp` | `string` | ISO 8601 — transactions before this time |
| `fromBlock` | `number` | Filter by start block |
| `toBlock` | `number` | Filter by end block |
| `limit` | `number` | Results per page (default: 2000, max: 2000) |
| `nextCursor` | `string` | Pagination cursor |

{% hint style="info" %}
Provide at least `walletAddress` or `poolId` to narrow results.
{% endhint %}

---

## Limit Orders

### List Markets

```
GET /v1/limit-orders/markets
```

Returns available limit order markets.

| Parameter | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID |
| `poolId` | `string` | Filter by specific MarketId |
| `onlyActive` | `boolean` | Only return active markets |
| `limit` | `number` | Results per page (default: 100, max: 2000) |
| `nextCursor` | `string` | Pagination cursor |

### Get Orderbook

```
GET /v1/limit-orders/orderbook
```

Returns active limit orders.

| Parameter | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID |
| `poolId` | `string` | Filter by MarketId |
| `maker` | `string` | Filter by maker address |
| `side` | `string` | `BUY` or `SELL` |
| `status` | `string[]` | Order status filter |
| `limit` | `number` | Results per page (default: 100, max: 2000) |
| `nextCursor` | `string` | Pagination cursor |

### Get Fills

```
GET /v1/limit-orders/fills
```

Returns filled order history.

| Parameter | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID |
| `orderHash` | `string` | Filter by specific order |
| `maker` | `string` | Filter by maker address |
| `taker` | `string` | Filter by taker address |
| `fromTimestamp` | `string` | ISO 8601 — fills after this time |
| `toTimestamp` | `string` | ISO 8601 — fills before this time |
| `limit` | `number` | Results per page (default: 100, max: 2000) |
| `nextCursor` | `string` | Pagination cursor |

### Submit Order

```
POST /v1/limit-orders/
```

Submit a new limit order. Orders use EIP-712 typed signing. The `makerTraits` field encodes order constraints (expiry, partial fill settings).

{% hint style="info" %}
Limit order integration requires constructing and signing EIP-712 typed data. Contact the Cork team for the signing SDK and integration guide.
{% endhint %}

**Request body:**

| Field | Type | Description |
|---|---|---|
| `chainId` | `number` | Chain ID |
| `salt` | `string` | Unique order salt |
| `maker` | `string` | Maker address |
| `receiver` | `string` | Receiver address |
| `makerAsset` | `string` | Token address the maker is selling |
| `takerAsset` | `string` | Token address the maker wants to receive |
| `makingAmount` | `string` | Amount of makerAsset |
| `takingAmount` | `string` | Amount of takerAsset |
| `makerTraits` | `string` | Encoded maker traits |
| `orderHash` | `string` | Hash of the order |
| `signature` | `string` | EIP-712 signature |
| `side` | `string` | `BUY` or `SELL` |
| `premium` | `string` | Order premium |
| `nonce` | `string` | Order nonce |
