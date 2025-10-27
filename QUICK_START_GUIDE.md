# Sim by Dune - Quick Start Guide

## 🚀 Get Started in 5 Minutes

This guide will help you get up and running with Sim by Dune APIs quickly. You'll learn how to authenticate, make your first API call, and start building blockchain applications.

---

## Prerequisites

- **API Key**: Get your API key from [Sim Dashboard](https://sim.dune.com/)
- **Basic Knowledge**: Familiarity with HTTP APIs and JSON
- **Programming Language**: Choose your preferred language (examples provided for JavaScript, Python, and cURL)

---

## Step 1: Get Your API Key

1. Visit [https://sim.dune.com/](https://sim.dune.com/)
2. Sign up or log in to your account
3. Navigate to API Keys section
4. Create a new API key with purpose `Sim API`
5. Copy your API key (you'll need it for all requests)

**⚠️ Important**: Keep your API key secure and never share it publicly.

---

## Step 2: Make Your First API Call

### Get Wallet Balances

Let's start by getting the token balances for a wallet address.

#### Using cURL
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY_HERE" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045"
```

#### Using JavaScript/Node.js
```javascript
const API_KEY = 'YOUR_API_KEY_HERE';
const ADDRESS = '0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045';

async function getBalances() {
  try {
    const response = await fetch(
      `https://api.sim.dune.com/v1/evm/balances/${ADDRESS}`,
      {
        headers: {
          'X-Sim-Api-Key': API_KEY
        }
      }
    );
    
    if (response.ok) {
      const data = await response.json();
      console.log('Balances:', data);
      return data;
    } else {
      console.error('Error:', response.status, response.statusText);
    }
  } catch (error) {
    console.error('Request failed:', error);
  }
}

getBalances();
```

#### Using Python
```python
import requests

API_KEY = 'YOUR_API_KEY_HERE'
ADDRESS = '0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045'

def get_balances():
    headers = {
        'X-Sim-Api-Key': API_KEY
    }
    
    response = requests.get(
        f'https://api.sim.dune.com/v1/evm/balances/{ADDRESS}',
        headers=headers
    )
    
    if response.status_code == 200:
        data = response.json()
        print('Balances:', data)
        return data
    else:
        print(f'Error: {response.status_code} - {response.text}')

get_balances()
```

---

## Step 3: Explore Different APIs

### 1. Get Transactions
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/transactions/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?limit=10"
```

### 2. Get NFT Collectibles
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/collectibles/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045"
```

### 3. Get Solana Balances
```bash
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/beta/svm/balances/86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY"
```

---

## Step 4: Add Filters and Parameters

### Filter by Chain
```bash
# Get balances only from Ethereum mainnet
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?chain_ids=1"
```

### Filter by Token Type
```bash
# Get only ERC20 tokens (exclude native ETH)
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?filters=erc20"
```

### Get Historical Prices
```bash
# Get 24-hour price history
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?historical_prices=1,6,24"
```

### Include Token Metadata
```bash
# Get logo and website URLs
curl -H "X-Sim-Api-Key: YOUR_API_KEY" \
     "https://api.sim.dune.com/v1/evm/balances/0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045?metadata=logo,url"
```

---

## Step 5: Build a Simple Application

### JavaScript Example: Wallet Dashboard

```javascript
class WalletDashboard {
  constructor(apiKey) {
    this.apiKey = apiKey;
    this.baseUrl = 'https://api.sim.dune.com';
  }

  async getWalletOverview(address) {
    try {
      // Get balances
      const balances = await this.getBalances(address);
      
      // Get recent transactions
      const transactions = await this.getTransactions(address);
      
      // Get NFTs
      const nfts = await this.getCollectibles(address);
      
      return {
        address,
        balances: balances.balances || [],
        transactions: transactions.transactions || [],
        nfts: nfts.collectibles || [],
        totalValue: this.calculateTotalValue(balances.balances || [])
      };
    } catch (error) {
      console.error('Error getting wallet overview:', error);
      throw error;
    }
  }

  async getBalances(address, chains = '1,137') {
    const response = await fetch(
      `${this.baseUrl}/v1/evm/balances/${address}?chain_ids=${chains}&metadata=logo,url`,
      {
        headers: { 'X-Sim-Api-Key': this.apiKey }
      }
    );
    return response.json();
  }

  async getTransactions(address, limit = 10) {
    const response = await fetch(
      `${this.baseUrl}/v1/evm/transactions/${address}?limit=${limit}`,
      {
        headers: { 'X-Sim-Api-Key': this.apiKey }
      }
    );
    return response.json();
  }

  async getCollectibles(address) {
    const response = await fetch(
      `${this.baseUrl}/v1/evm/collectibles/${address}`,
      {
        headers: { 'X-Sim-Api-Key': this.apiKey }
      }
    );
    return response.json();
  }

  calculateTotalValue(balances) {
    return balances.reduce((total, balance) => {
      return total + (balance.value_usd || 0);
    }, 0);
  }
}

// Usage
const dashboard = new WalletDashboard('YOUR_API_KEY');
dashboard.getWalletOverview('0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045')
  .then(overview => {
    console.log('Wallet Overview:', overview);
    console.log(`Total Value: $${overview.totalValue.toFixed(2)}`);
  });
```

### Python Example: Token Tracker

```python
import requests
import json
from datetime import datetime

class TokenTracker:
    def __init__(self, api_key):
        self.api_key = api_key
        self.base_url = 'https://api.sim.dune.com'
        self.headers = {'X-Sim-Api-Key': api_key}
    
    def get_token_info(self, token_address, chain_ids='1'):
        """Get detailed information about a token"""
        url = f"{self.base_url}/v1/evm/token-info/{token_address}"
        params = {'chain_ids': chain_ids}
        
        response = requests.get(url, headers=self.headers, params=params)
        if response.status_code == 200:
            return response.json()
        else:
            raise Exception(f"Error: {response.status_code}")
    
    def get_token_holders(self, token_address, limit=100):
        """Get top token holders"""
        url = f"{self.base_url}/v1/evm/token-holders/{token_address}"
        params = {'limit': limit}
        
        response = requests.get(url, headers=self.headers, params=params)
        if response.status_code == 200:
            return response.json()
        else:
            raise Exception(f"Error: {response.status_code}")
    
    def track_token_price(self, token_address, hours=[1, 6, 24]):
        """Track token price over time"""
        url = f"{self.base_url}/v1/evm/balances/0x0000000000000000000000000000000000000000"
        params = {
            'chain_ids': '1',
            'historical_prices': ','.join(map(str, hours))
        }
        
        response = requests.get(url, headers=self.headers, params=params)
        if response.status_code == 200:
            data = response.json()
            # Find the specific token
            for balance in data.get('balances', []):
                if balance['address'].lower() == token_address.lower():
                    return {
                        'token': balance['symbol'],
                        'current_price': balance['price_usd'],
                        'historical_prices': balance.get('historical_prices', [])
                    }
        else:
            raise Exception(f"Error: {response.status_code}")

# Usage
tracker = TokenTracker('YOUR_API_KEY')

# Track USDC price
try:
    usdc_info = tracker.track_token_price('0xA0b86a33E6441b8c4C8C3C8C3C8C3C8C3C8C3C8C3')
    print(f"USDC Current Price: ${usdc_info['current_price']}")
    
    for price_point in usdc_info['historical_prices']:
        hours_ago = price_point['offset_hours']
        price = price_point['price_usd']
        print(f"{hours_ago} hours ago: ${price}")
        
except Exception as e:
    print(f"Error tracking token: {e}")
```

---

## Step 6: Handle Errors and Rate Limits

### Error Handling Example

```javascript
class SimApiClient {
  constructor(apiKey) {
    this.apiKey = apiKey;
    this.baseUrl = 'https://api.sim.dune.com';
  }

  async makeRequest(endpoint, params = {}) {
    try {
      const url = new URL(`${this.baseUrl}${endpoint}`);
      Object.keys(params).forEach(key => 
        url.searchParams.append(key, params[key])
      );

      const response = await fetch(url, {
        headers: { 'X-Sim-Api-Key': this.apiKey }
      });

      if (response.status === 429) {
        // Rate limit exceeded
        const retryAfter = response.headers.get('Retry-After');
        console.log(`Rate limited. Retry after ${retryAfter} seconds`);
        throw new Error('Rate limit exceeded');
      }

      if (!response.ok) {
        const errorData = await response.json().catch(() => ({}));
        throw new Error(`HTTP ${response.status}: ${errorData.message || response.statusText}`);
      }

      return await response.json();
    } catch (error) {
      if (error.message.includes('Rate limit')) {
        // Implement exponential backoff
        await this.delay(1000);
        return this.makeRequest(endpoint, params);
      }
      throw error;
    }
  }

  delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}
```

---

## Step 7: Next Steps

### What You Can Build

1. **Portfolio Trackers**: Monitor wallet balances across multiple chains
2. **Transaction Monitors**: Track incoming/outgoing transactions
3. **NFT Galleries**: Display NFT collections
4. **Price Alerts**: Monitor token price changes
5. **Analytics Dashboards**: Analyze wallet activity patterns

### Advanced Features to Explore

- **WebSocket APIs**: Real-time updates
- **Batch Requests**: Multiple addresses in one call
- **Historical Data**: Price and transaction history
- **Multi-chain Support**: Ethereum, Polygon, Solana, and more

### Resources

- **Full Documentation**: [https://docs.sim.dune.com](https://docs.sim.dune.com)
- **API Reference**: See `TECHNICAL_API_REFERENCE.md`
- **Community**: [Telegram](https://t.me/+nvyoX5xyxNwyNjU0)
- **Support**: [https://sim.dune.com/support](https://sim.dune.com/support)

---

## 🎯 Common Use Cases

### 1. Check if a wallet has a specific token
```javascript
async function hasToken(walletAddress, tokenAddress, chainId = '1') {
  const response = await fetch(
    `https://api.sim.dune.com/v1/evm/balances/${walletAddress}?chain_ids=${chainId}`,
    { headers: { 'X-Sim-Api-Key': API_KEY } }
  );
  
  const data = await response.json();
  return data.balances.some(balance => 
    balance.address.toLowerCase() === tokenAddress.toLowerCase()
  );
}
```

### 2. Get wallet's total portfolio value
```javascript
async function getPortfolioValue(walletAddress) {
  const response = await fetch(
    `https://api.sim.dune.com/v1/evm/balances/${walletAddress}`,
    { headers: { 'X-Sim-Api-Key': API_KEY } }
  );
  
  const data = await response.json();
  return data.balances.reduce((total, balance) => 
    total + (balance.value_usd || 0), 0
  );
}
```

### 3. Monitor recent transactions
```javascript
async function getRecentTransactions(walletAddress, limit = 5) {
  const response = await fetch(
    `https://api.sim.dune.com/v1/evm/transactions/${walletAddress}?limit=${limit}`,
    { headers: { 'X-Sim-Api-Key': API_KEY } }
  );
  
  const data = await response.json();
  return data.transactions.map(tx => ({
    hash: tx.hash,
    value: tx.value,
    timestamp: tx.block_time,
    status: tx.status === 1 ? 'Success' : 'Failed'
  }));
}
```

---

## 🚨 Troubleshooting

### Common Issues

1. **401 Unauthorized**: Check your API key is correct
2. **400 Bad Request**: Verify parameter formats
3. **429 Too Many Requests**: Implement rate limiting
4. **404 Not Found**: Check the endpoint URL and address format

### Getting Help

- Check the [error handling section](#error-handling) in the main documentation
- Review the [rate limits](#rate-limits) section
- Join the [Telegram community](https://t.me/+nvyoX5xyxNwyNjU0)
- Contact support through the [Sim dashboard](https://sim.dune.com/)

---

**🎉 Congratulations!** You've successfully made your first API calls to Sim by Dune. You're now ready to build powerful blockchain applications!

*Need more examples or have questions? Check out the comprehensive documentation or reach out to the community.*