# Sim by Dune - Technical API Reference

## Table of Contents

1. [API Specifications](#api-specifications)
2. [Data Models](#data-models)
3. [Response Schemas](#response-schemas)
4. [Parameter Specifications](#parameter-specifications)
5. [Error Codes](#error-codes)
6. [WebSocket APIs](#websocket-apis)

---

## API Specifications

### Base Configuration

- **OpenAPI Version**: 3.0.3
- **Base URL**: `https://api.sim.dune.com`
- **Authentication**: API Key in `X-Sim-Api-Key` header
- **Content Type**: `application/json`

---

## Data Models

### EVM Balance Data Model

```typescript
interface BalanceData {
  // Required fields
  chain: string;                    // Chain name (e.g., "ethereum")
  address: string;                  // Token contract address
  amount: string;                   // Raw token amount (wei/smallest unit)
  
  // Optional fields
  chain_id?: number;                // Chain ID (e.g., 1 for Ethereum mainnet)
  decimals?: number;                // Token decimal places
  name?: string;                    // Token name
  symbol?: string;                  // Token symbol
  price_usd?: number;              // Current USD price
  value_usd?: number;              // Total USD value
  pool_size?: number;              // Liquidity pool size
  low_liquidity?: boolean;         // Low liquidity flag
  
  // Historical price data
  historical_prices?: HistoricalPricePoint[];
  
  // Token metadata
  token_metadata?: {
    logo?: string;                  // Logo URL
    url?: string;                   // Token website URL
  };
}

interface HistoricalPricePoint {
  offset_hours: number;            // Hours from current time (1-24)
  price_usd: number;               // USD price at that time
}
```

### EVM Transaction Data Model

```typescript
interface TransactionData {
  // Basic transaction info
  address: string;                  // Sender address
  block_hash: string;               // Block hash
  block_number: number;             // Block number
  block_time: string;               // Block timestamp (ISO 8601)
  chain: string;                    // Chain name
  chain_id: number;                 // Chain ID
  hash: string;                     // Transaction hash
  
  // Gas information
  gas_price: string;                // Gas price in wei
  gas_used: number;                 // Gas used
  
  // Transaction details
  input: string;                    // Input data (hex)
  nonce: number;                    // Transaction nonce
  status: number;                   // Transaction status (1 = success, 0 = failed)
  to: string;                       // Recipient address
  transaction_index: number;        // Transaction index in block
  value: string;                    // Transaction value in wei
  
  // Decoded information
  decoded_logs?: DecodedLog[];      // Decoded transaction logs
}

interface DecodedLog {
  address: string;                  // Contract address
  topics: string[];                 // Event topics
  data: string;                     // Event data
  decoded?: {                       // Decoded event data
    name: string;                   // Event name
    inputs: any[];                  // Event parameters
  };
}
```

### SVM Balance Data Model

```typescript
interface SvmBalanceData {
  chain: string;                    // Chain name (e.g., "solana")
  address: string;                  // Token address ("native" for SOL)
  amount: string;                   // Raw token amount
  balance: string;                  // Human-readable balance
  value_usd?: number;              // USD value
  program_id?: string;              // SPL program ID
  decimals: number;                 // Token decimal places
  total_supply: string;             // Total token supply
  name: string;                     // Token name
  symbol: string;                   // Token symbol
}
```

---

## Response Schemas

### EVM Balances Response

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["wallet_address", "balances"],
  "properties": {
    "wallet_address": {
      "type": "string",
      "description": "The wallet address that was queried",
      "example": "0xd8da6bf26964af9d7eed9e03e53415d37aa96045"
    },
    "balances": {
      "type": "array",
      "items": {
        "$ref": "#/definitions/BalanceData"
      }
    },
    "next_offset": {
      "type": "string",
      "description": "Pagination cursor for next request"
    },
    "request_time": {
      "type": "string",
      "format": "date-time",
      "description": "Timestamp when request was received"
    },
    "response_time": {
      "type": "string",
      "format": "date-time",
      "description": "Timestamp when response was generated"
    },
    "errors": {
      "$ref": "#/definitions/BalanceErrors"
    }
  }
}
```

### EVM Transactions Response

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["transactions"],
  "properties": {
    "transactions": {
      "type": "array",
      "items": {
        "$ref": "#/definitions/TransactionData"
      }
    },
    "next_offset": {
      "type": "string",
      "description": "Pagination cursor for next request"
    }
  }
}
```

### SVM Balances Response

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "required": ["wallet_address", "balances"],
  "properties": {
    "processing_time_ms": {
      "type": "number",
      "description": "Request processing time in milliseconds"
    },
    "wallet_address": {
      "type": "string",
      "description": "The SVM address that was queried"
    },
    "next_offset": {
      "type": "string",
      "description": "Base64 encoded pagination token"
    },
    "balances_count": {
      "type": "number",
      "description": "Total number of balances returned"
    },
    "balances": {
      "type": "array",
      "items": {
        "$ref": "#/definitions/SvmBalanceData"
      }
    }
  }
}
```

---

## Parameter Specifications

### EVM API Parameters

#### Chain IDs Parameter
```typescript
interface ChainIdsParameter {
  name: "chain_ids";
  in: "query";
  required: false;
  description: string;
  schema: {
    type: "string";
    examples: {
      single_chain_id: {
        summary: "Single chain ID";
        value: "1";
      };
      single_chain_name: {
        summary: "Single chain name";
        value: "ethereum";
      };
      multiple_chains: {
        summary: "Multiple chains";
        value: "1,137";
      };
      multiple_chain_names: {
        summary: "Multiple chain names";
        value: "ethereum,polygon";
      };
    };
  };
}
```

#### Filters Parameter
```typescript
interface FiltersParameter {
  name: "filters";
  in: "query";
  required: false;
  description: string;
  schema: {
    type: "string";
    enum: ["erc20", "native"];
  };
}
```

#### Metadata Parameter
```typescript
interface MetadataParameter {
  name: "metadata";
  in: "query";
  required: false;
  description: string;
  schema: {
    type: "string";
    description: "Comma-separated list of metadata fields";
    examples: {
      logo_only: {
        summary: "Logo only";
        value: "logo";
      };
      logo_and_url: {
        summary: "Logo and URL";
        value: "logo,url";
      };
    };
  };
}
```

#### Historical Prices Parameter
```typescript
interface HistoricalPricesParameter {
  name: "historical_prices";
  in: "query";
  required: false;
  style: "form";
  explode: false;
  schema: {
    oneOf: [
      {
        type: "integer";
        format: "int32";
        minimum: 1;
        maximum: 24;
      };
      {
        type: "array";
        items: {
          type: "integer";
          format: "int32";
          minimum: 1;
          maximum: 24;
        };
        maxItems: 3;
      };
    ];
    examples: {
      single_offset: {
        summary: "Single offset";
        value: "24";
      };
      three_offsets: {
        summary: "Three offsets";
        value: "1,6,24";
      };
    };
  };
}
```

#### Pagination Parameters
```typescript
interface PaginationParameters {
  offset: {
    name: "offset";
    in: "query";
    required: false;
    description: "Pagination cursor from previous response";
    schema: {
      type: "string";
    };
  };
  limit: {
    name: "limit";
    in: "query";
    required: false;
    description: "Maximum number of results";
    schema: {
      type: "integer";
      minimum: 1;
      maximum: 1000; // or 100 for some endpoints
      default: 1000; // or 100 for some endpoints
    };
  };
}
```

### SVM API Parameters

#### Chains Parameter
```typescript
interface ChainsParameter {
  name: "chains";
  in: "query";
  required: false;
  description: "Comma-separated list of chains to include";
  schema: {
    type: "string";
    examples: {
      solana_only: {
        summary: "Solana only";
        value: "solana";
      };
      multiple_chains: {
        summary: "Multiple chains";
        value: "solana,eclipse";
      };
    };
  };
}
```

---

## Error Codes

### HTTP Status Codes

| Status Code | Description | Common Causes |
|-------------|-------------|---------------|
| 200 | Success | Request completed successfully |
| 400 | Bad Request | Invalid parameters, malformed request |
| 401 | Unauthorized | Missing or invalid API key |
| 403 | Forbidden | API key lacks required permissions |
| 404 | Not Found | Resource not found |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server-side error |

### Error Response Format

```json
{
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": "Additional error context"
  },
  "request_id": "unique-request-identifier",
  "timestamp": "2025-01-13T10:30:00Z"
}
```

### Common Error Codes

#### EVM API Errors
```typescript
interface EvmApiErrors {
  INVALID_ADDRESS: "Invalid wallet address format";
  UNSUPPORTED_CHAIN: "Chain ID or name not supported";
  RATE_LIMIT_EXCEEDED: "Too many requests, please slow down";
  INVALID_FILTER: "Invalid filter parameter value";
  INVALID_HISTORICAL_PRICES: "Invalid historical prices format";
  INVALID_METADATA: "Invalid metadata field specified";
}
```

#### SVM API Errors
```typescript
interface SvmApiErrors {
  INVALID_SVM_ADDRESS: "Invalid SVM address format";
  UNSUPPORTED_CHAIN: "Chain not supported";
  ADDRESS_NOT_FOUND: "Address not found on specified chain";
  INVALID_PAGINATION: "Invalid pagination token";
}
```

---

## WebSocket APIs

### Real-time Updates

Sim by Dune provides WebSocket endpoints for real-time blockchain data updates.

#### EVM WebSocket Endpoints

```typescript
// Connect to WebSocket
const ws = new WebSocket('wss://api.sim.dune.com/ws/v1/evm');

// Subscribe to balance updates
ws.send(JSON.stringify({
  action: 'subscribe',
  type: 'balances',
  address: '0xd8da6bf26964af9d7eed9e03e53415d37aa96045',
  chains: ['1', '137']
}));

// Listen for updates
ws.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log('Balance update:', data);
};
```

#### SVM WebSocket Endpoints

```typescript
// Connect to SVM WebSocket
const ws = new WebSocket('wss://api.sim.dune.com/ws/beta/svm');

// Subscribe to transaction updates
ws.send(JSON.stringify({
  action: 'subscribe',
  type: 'transactions',
  address: '86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY'
}));
```

### WebSocket Message Format

```typescript
interface WebSocketMessage {
  type: 'subscription' | 'update' | 'error';
  timestamp: string;
  data: any;
  subscription_id?: string;
}

interface SubscriptionMessage extends WebSocketMessage {
  type: 'subscription';
  subscription_id: string;
  status: 'subscribed' | 'unsubscribed';
}

interface UpdateMessage extends WebSocketMessage {
  type: 'update';
  subscription_id: string;
  data: BalanceData | TransactionData;
}

interface ErrorMessage extends WebSocketMessage {
  type: 'error';
  error: {
    code: string;
    message: string;
  };
}
```

---

## Advanced Usage Patterns

### Batch Requests

For multiple addresses or chains, consider using batch endpoints:

```typescript
// Batch balance request
const batchRequest = {
  addresses: [
    '0xd8da6bf26964af9d7eed9e03e53415d37aa96045',
    '0x742d35Cc6634C0532925a3b8D4C9db96C4b4d8b6'
  ],
  chains: ['1', '137'],
  filters: 'erc20'
};

const response = await fetch('/v1/evm/balances/batch', {
  method: 'POST',
  headers: {
    'X-Sim-Api-Key': API_KEY,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify(batchRequest)
});
```

### Caching Strategies

```typescript
// Implement caching for frequently accessed data
class SimApiCache {
  private cache = new Map<string, { data: any; timestamp: number }>();
  private ttl = 5 * 60 * 1000; // 5 minutes

  async getCachedBalances(address: string, chains: string[]): Promise<any> {
    const key = `balances:${address}:${chains.sort().join(',')}`;
    const cached = this.cache.get(key);
    
    if (cached && Date.now() - cached.timestamp < this.ttl) {
      return cached.data;
    }
    
    // Fetch fresh data
    const data = await this.fetchBalances(address, chains);
    this.cache.set(key, { data, timestamp: Date.now() });
    return data;
  }
}
```

### Error Handling and Retries

```typescript
class SimApiClient {
  private maxRetries = 3;
  private retryDelay = 1000;

  async requestWithRetry(endpoint: string, options: RequestInit): Promise<Response> {
    let lastError: Error;
    
    for (let attempt = 1; attempt <= this.maxRetries; attempt++) {
      try {
        const response = await fetch(endpoint, options);
        
        if (response.ok) {
          return response;
        }
        
        // Don't retry on client errors (4xx)
        if (response.status >= 400 && response.status < 500) {
          throw new Error(`Client error: ${response.status}`);
        }
        
        lastError = new Error(`HTTP ${response.status}: ${response.statusText}`);
        
      } catch (error) {
        lastError = error as Error;
      }
      
      // Wait before retrying (exponential backoff)
      if (attempt < this.maxRetries) {
        await new Promise(resolve => 
          setTimeout(resolve, this.retryDelay * Math.pow(2, attempt - 1))
        );
      }
    }
    
    throw lastError!;
  }
}
```

---

## Performance Considerations

### Request Optimization

1. **Use appropriate limits**: Don't request more data than needed
2. **Implement pagination**: Use `offset` parameter for large datasets
3. **Filter by chains**: Only request data from relevant chains
4. **Cache responses**: Implement client-side caching for static data

### Rate Limiting

- **EVM APIs**: 1000 requests/minute
- **SVM APIs**: 500 requests/minute
- **Burst handling**: Implement exponential backoff for rate limit errors

### Response Time Optimization

- **Use filters**: Reduce data transfer with `filters` parameter
- **Limit metadata**: Only request needed metadata fields
- **Historical prices**: Limit historical price points to essential timeframes

---

*This technical reference provides comprehensive details about all Sim by Dune APIs, data models, and advanced usage patterns. For implementation examples and best practices, refer to the main documentation.*