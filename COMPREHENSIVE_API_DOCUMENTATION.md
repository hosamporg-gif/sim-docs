# Sim by Dune - Comprehensive API Documentation

## Table of Contents

1. [Overview](#overview)
2. [Authentication](#authentication)
3. [Base URL](#base-url)
4. [EVM APIs](#evm-apis)
5. [SVM APIs](#svm-apis)
6. [Sim IDX Framework](#sim-idx-framework)
7. [Error Handling](#error-handling)
8. [Rate Limits](#rate-limits)
9. [Examples](#examples)

## Overview

Sim by Dune is a comprehensive blockchain data platform that provides real-time access to blockchain data across multiple networks including Ethereum, Solana, and other EVM-compatible chains. The platform offers APIs for balances, transactions, collectibles, and more, along with a framework for building custom blockchain data indexers.

## Authentication

All API requests require authentication using an API key in the `X-Sim-Api-Key` header.

```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045
```

**Note**: You need an API key with a purpose of `Sim API` from the [Sim dashboard](https://sim.dune.com/).

## Base URL

```
https://api.sim.dune.com
```

---

## EVM APIs

The EVM APIs provide access to Ethereum and other EVM-compatible blockchain data.

### 1. Balances API

**Endpoint**: `GET /v1/evm/balances/{uri}`

**Description**: Get real-time token balances for an EVM address, including native assets and ERC20 tokens.

**Parameters**:
- `uri` (path, required): Wallet address to get balances for
- `chain_ids` (query, optional): Filter by chain(s). Accepts chain ID, chain name, or comma-separated list
- `filters` (query, optional): Filter by token type (`erc20` or `native`)
- `metadata` (query, optional): Additional metadata fields (`logo`, `url`)
- `historical_prices` (query, optional): 24-hour price history (1-24 hours, up to 3 offsets)
- `offset` (query, optional): Pagination cursor
- `limit` (query, optional): Maximum results (default: 1000, max: 1000)

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?chain_ids=1&filters=erc20&metadata=logo,url&historical_prices=1,6,24"
```

**Example Response**:
```json
{
  "wallet_address": "0xd8da6bf26964af9d7eed9e03e53415d37aa96045",
  "balances": [
    {
      "chain": "ethereum",
      "chain_id": 1,
      "address": "0x0e36c45d16585d1801e37cfaa577a9ad39f9343a",
      "amount": "45787557175393926414790300082",
      "symbol": "Kendu",
      "name": "Kendu of Bank",
      "decimals": 18,
      "price_usd": 1.233346836175539e+21,
      "value_usd": 5.647193877847869e+31,
      "pool_size": 1233.34683617554,
      "low_liquidity": true,
      "historical_prices": [
        {
          "offset_hours": 1,
          "price_usd": 1.233346836175539e+21
        },
        {
          "offset_hours": 6,
          "price_usd": 1.233346836175539e+21
        },
        {
          "offset_hours": 24,
          "price_usd": 1.233346836175539e+21
        }
      ]
    }
  ],
  "next_offset": "opaque-pagination-token",
  "request_time": "2025-08-13T10:31:08Z",
  "response_time": "2025-08-13T10:31:08Z"
}
```

### 2. Activity API

**Endpoint**: `GET /v1/evm/activity/{uri}`

**Description**: Get chronologically ordered transactions including native transfers, ERC20 movements, NFT transfers, and decoded contract interactions.

**Parameters**:
- `uri` (path, required): Wallet address
- `chain_ids` (query, optional): Filter by chain(s)
- `offset` (query, optional): Pagination cursor
- `limit` (query, optional): Maximum results (default: 100, max: 100)

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/activity/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?chain_ids=1&limit=50"
```

### 3. Collectibles API

**Endpoint**: `GET /v1/evm/collectibles/{uri}`

**Description**: Get all NFT (ERC721 and ERC1155) balances, including IDs and metadata.

**Parameters**:
- `uri` (path, required): Wallet address
- `chain_ids` (query, optional): Filter by chain(s)
- `offset` (query, optional): Pagination cursor
- `limit` (query, optional): Maximum results (default: 100, max: 100)

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/collectibles/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045"
```

### 4. Transactions API

**Endpoint**: `GET /v1/evm/transactions/{wallet_address}`

**Description**: Retrieve granular transaction details including block information, gas data, transaction types, and raw transaction values.

**Parameters**:
- `wallet_address` (path, required): EVM wallet address
- `chain_ids` (query, optional): Filter by chain(s)
- `limit` (query, optional): Maximum results (default: 100, max: 100)
- `offset` (query, optional): Pagination cursor
- `decode` (query, optional): Include decoded transaction logs (boolean)

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/transactions/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?decode=true&limit=50"
```

**Example Response**:
```json
{
  "transactions": [
    {
      "address": "0xd8da6bf26964af9d7eed9e03e53415d37aa96045",
      "block_hash": "0xbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb",
      "block_number": 12345678,
      "block_time": "2025-01-13T10:30:00Z",
      "chain": "ethereum",
      "chain_id": 1,
      "gas_price": "20000000000",
      "gas_used": 21000,
      "hash": "0xaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
      "input": "0x",
      "nonce": 123,
      "status": 1,
      "to": "0xcccccccccccccccccccccccccccccccccccccccc",
      "transaction_index": 0,
      "value": "1000000000000000000",
      "decoded_logs": []
    }
  ],
  "next_offset": "opaque-pagination-token"
}
```

### 5. Token Info API

**Endpoint**: `GET /v1/evm/token-info/{uri}`

**Description**: Get detailed metadata and real-time price information for any native asset or ERC20 token.

**Parameters**:
- `uri` (path, required): Token address or chain name for native assets
- `chain_ids` (query, optional): Filter by chain(s)

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/token-info/0xA0b86a33E6441b8c4C8C3C8C3C8C3C8C3C8C3C8C3"
```

### 6. Token Holders API

**Endpoint**: `GET /v1/evm/token-holders/{uri}`

**Description**: Discover token distribution across ERC20 or ERC721 holders, ranked by wallet value.

**Parameters**:
- `uri` (path, required): Token address
- `chain_ids` (query, optional): Filter by chain(s)
- `offset` (query, optional): Pagination cursor
- `limit` (query, optional): Maximum results (default: 100, max: 100)

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/token-holders/0xA0b86a33E6441b8c4C8C3C8C3C8C3C8C3C8C3C8C3"
```

### 7. Supported Chains API

**Endpoint**: `GET /v1/evm/supported-chains`

**Description**: Get information about supported EVM chains and their tags.

**Parameters**: None

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/supported-chains"
```

---

## SVM APIs

The SVM APIs provide access to Solana and Eclipse blockchain data.

### 1. SVM Balances API

**Endpoint**: `GET /beta/svm/balances/{uri}`

**Description**: Get accurate and fast real-time balances of native, SPL and SPL-2022 tokens on Solana and Eclipse blockchains.

**Parameters**:
- `uri` (path, required): SVM address
- `chains` (query, optional): Comma-separated list of chains to include
- `limit` (query, optional): Maximum results (default: 1000, max: 1000)
- `offset` (query, optional): Pagination offset from previous response

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/beta/svm/balances/86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY"
```

**Example Response**:
```json
{
  "processing_time_ms": 120,
  "wallet_address": "DYw8jCTfwHNRJhhmFcbXvVDTqWMEVFBX6ZKUmG5CNSKK",
  "next_offset": "eyJibG9ja190aW1lIjoxNjgwMDAwMDAwLCJpbmRleCI6MH0=",
  "balances_count": 2,
  "balances": [
    {
      "chain": "solana",
      "address": "native",
      "amount": "1000000000",
      "balance": "1.0",
      "value_usd": 20.5,
      "program_id": null,
      "decimals": 9,
      "total_supply": "1000000000000000",
      "name": "Solana",
      "symbol": "SOL"
    }
  ]
}
```

### 2. SVM Transactions API

**Endpoint**: `GET /beta/svm/transactions/{uri}`

**Description**: Quick and accurate lookup of transactions associated with a Solana address, ordered by descending block time.

**Parameters**:
- `uri` (path, required): SVM address
- `chains` (query, optional): Comma-separated list of chains to include
- `limit` (query, optional): Maximum results (default: 100, max: 100)
- `offset` (query, optional): Pagination offset from previous response

**Example Request**:
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/beta/svm/transactions/86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY"
```

---

## Sim IDX Framework

Sim IDX is a framework that radically simplifies the process of indexing blockchain data. It provides a complete serverless API development environment with built-in database connectivity, automatic scaling, and edge deployment.

### Installation

```bash
curl -L https://simcli.dune.com | bash
```

### CLI Commands

#### Initialize New App
```bash
sim init
```
Creates a pre-configured sample app that indexes Uniswap V3 trading pools across multiple chains.

#### Deploy App
```bash
sim deploy
```
Deploys your app to the Sim IDX platform.

#### Test Locally
```bash
cd apis
npm run dev
```
Runs your API locally at `http://localhost:8787`.

### Project Structure

```
my-idx-app/
├── apis/                    # API server code
│   ├── src/
│   │   └── index.ts        # Main API file
│   ├── package.json
│   └── .dev.vars           # Local environment variables
├── listener/                # Blockchain event listener
│   ├── src/
│   │   └── main.ts         # Listener logic
│   └── package.json
├── db/                      # Database schema and migrations
│   └── schema/
└── sim.toml                 # Configuration file
```

### API Development

The framework uses **Hono** for HTTP handling and **Drizzle** for database operations.

#### Basic API Setup
```typescript
import { App, db, types } from "sim-idx";
import { eq, sql } from "drizzle-orm";

const app = App.create();

// Add endpoints
app.get("/api/pools", async (c) => {
  const rows = await db.client(c)
    .select()
    .from(poolCreated)
    .limit(10);
  
  return c.json({ pools: rows });
});
```

#### Database Operations
```typescript
// Query with filters
const pools = await db.client(c)
  .select()
  .from(poolCreated)
  .where(eq(poolCreated.chain, "ethereum"))
  .limit(10);

// Raw SQL queries
const result = await db.client(c)
  .execute(sql`SELECT COUNT(*) FROM pool_created`);
```

### Listener Development

The listener monitors blockchain events and indexes them into your database.

#### Basic Listener
```typescript
import { Listener } from "sim-idx";

export default Listener.create({
  name: "uniswap-v3-pools",
  
  async onPoolCreated(event, context) {
    await context.db.insert(poolCreated).values({
      chain: event.chain,
      address: event.address,
      token0: event.token0,
      token1: event.token1,
      fee: event.fee,
      block_time: event.blockTime,
      block_number: event.blockNumber
    });
  }
});
```

### Environment Configuration

Create `.dev.vars` for local development:
```plaintext
DB_CONNECTION_STRING="your_database_connection_string_from_app_page"
```

### Deployment

1. **First Deployment**: Use `sim deploy` to create your app
2. **Subsequent Deployments**: Push to your Git repository for automatic deployment
3. **Environment Variables**: Configured automatically in production

---

## Error Handling

### HTTP Status Codes

- **200**: Success
- **400**: Bad Request - Malformed request data
- **404**: Not Found - Resource not found
- **500**: Internal Server Error - Server-side error

### Error Response Format

```json
{
  "error": "Error message",
  "details": "Additional error details"
}
```

### Common Error Scenarios

1. **Invalid API Key**: Ensure your API key is valid and has the correct purpose
2. **Invalid Address Format**: Check that wallet addresses are properly formatted
3. **Unsupported Chain**: Verify the chain ID or name is supported
4. **Rate Limit Exceeded**: Reduce request frequency

---

## Rate Limits

- **EVM APIs**: 1000 requests per minute per API key
- **SVM APIs**: 500 requests per minute per API key
- **Sim IDX**: No rate limits for your own indexed data

---

## Examples

### JavaScript/Node.js

```javascript
const axios = require('axios');

const API_KEY = 'your_api_key';
const BASE_URL = 'https://api.sim.dune.com';

async function getWalletBalances(address) {
  try {
    const response = await axios.get(
      `${BASE_URL}/v1/evm/balances/${address}`,
      {
        headers: {
          'X-Sim-Api-Key': API_KEY
        },
        params: {
          chain_ids: '1,137',
          filters: 'erc20',
          metadata: 'logo,url'
        }
      }
    );
    return response.data;
  } catch (error) {
    console.error('Error fetching balances:', error.response?.data || error.message);
    throw error;
  }
}

// Usage
getWalletBalances('0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045')
  .then(balances => console.log(balances))
  .catch(error => console.error(error));
```

### Python

```python
import requests

API_KEY = 'your_api_key'
BASE_URL = 'https://api.sim.dune.com'

def get_wallet_balances(address):
    headers = {
        'X-Sim-Api-Key': API_KEY
    }
    
    params = {
        'chain_ids': '1,137',
        'filters': 'erc20',
        'metadata': 'logo,url'
    }
    
    response = requests.get(
        f'{BASE_URL}/v1/evm/balances/{address}',
        headers=headers,
        params=params
    )
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f'Error: {response.status_code} - {response.text}')

# Usage
try:
    balances = get_wallet_balances('0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045')
    print(balances)
except Exception as e:
    print(f'Error: {e}')
```

### cURL

```bash
# Get EVM balances
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?chain_ids=1&filters=erc20"

# Get SVM balances
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/beta/svm/balances/86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY"

# Get transactions with decoding
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/transactions/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?decode=true&limit=50"
```

---

## Support and Resources

- **Documentation**: [https://docs.sim.dune.com](https://docs.sim.dune.com)
- **Dashboard**: [https://sim.dune.com](https://sim.dune.com)
- **Community**: [Telegram](https://t.me/+nvyoX5xyxNwyNjU0)
- **Twitter**: [@dunesim](https://x.com/dunesim)

---

## Changelog

For the latest updates and changes, see the [Sim IDX Changelog](/idx/changelog).

---

*This documentation covers all public APIs, functions, and components available in the Sim by Dune platform. For specific implementation details or advanced usage patterns, refer to the individual API documentation pages.*