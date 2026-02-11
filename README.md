# Polymarket Plugin for ElizaOS

### ElizaOKClaw - Autonomous Prediction Market Intelligence

This plugin provides integration with [Polymarket](https://polymarket.com) prediction markets through the CLOB (Central Limit Order Book) API, enabling AI agents to interact with prediction markets. Part of the **ElizaOKClaw** ecosystem — the first open-source [ElizaOS](https://elizaos.ai) agent combining Polymarket intelligence with [Moltbook](https://www.moltbook.com) social engagement.

> **See also:** [@elizaos/plugin-moltbook](https://github.com/elizaclaw/plugin-moltbook) — ElizaOKClaw's Moltbook social plugin for autonomous posting and community engagement.

---

## The ElizaOKClaw Ecosystem

ElizaOKClaw uses **two plugins together** to create an autonomous prediction market agent that shares intelligence socially:

| Plugin | Purpose | Repository |
|--------|---------|------------|
| **plugin-polymarket** (this repo) | Read Polymarket data, analyze markets, track prices | [elizaclaw/plugin-polymarket](https://github.com/elizaclaw/plugin-polymarket) |
| **plugin-moltbook** | Post insights on Moltbook, engage with AI agents | [elizaclaw/plugin-moltbook](https://github.com/elizaclaw/plugin-moltbook) |

**How they work together:**
1. `plugin-polymarket` fetches real-time market data, price history, and order books from Polymarket
2. The ElizaOKClaw agent analyzes this data for mispriced probabilities and trading opportunities
3. `plugin-moltbook` autonomously posts these insights to Moltbook every 15 minutes
4. The agent engages with other AI agents on Moltbook, building community around prediction market intelligence

> Follow the agent live: [moltbook.com/u/eos_ElizaOKClaw](https://www.moltbook.com/u/eos_ElizaOKClaw)

---

## Features

- **Market Discovery** — Retrieve all available prediction markets (politics, crypto, sports, geopolitics)
- **Price History** — Historical price data with trend analysis and technical indicators
- **Order Book Depth** — Real-time bid/ask data for liquidity and spread analysis
- **Simplified Views** — Reduced data schemas for efficient LLM context usage
- **Bulk Queries** — Multi-token order book depth in a single call
- **API Key Management** — Secure credential handling with revocation support

## Installation

```bash
# In an elizaOS project
bun add @elizaos/plugin-polymarket
```

## Configuration

### Character Setup (Recommended)

```typescript
export const character: Character = {
  name: 'ElizaOKClaw',
  plugins: [
    '@elizaos/plugin-sql',
    '@elizaos/plugin-polymarket',  // Polymarket data
    '@elizaos/plugin-moltbook',    // Social posting
    '@elizaos/plugin-bootstrap',
  ],
  settings: {
    secrets: {
      // Polymarket
      CLOB_API_URL: 'https://clob.polymarket.com',
      // Moltbook
      MOLTBOOK_API_KEY: process.env.MOLTBOOK_API_KEY || 'your_key_here',
      MOLTBOOK_AUTO_ENGAGE: 'true',
    },
  },
};
```

### Environment Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `CLOB_API_URL` | Polymarket CLOB API endpoint | `https://clob.polymarket.com` | Yes (activates plugin) |
| `CLOB_API_KEY` | API key for authenticated requests | — | No (read-only works without) |
| `POLYMARKET_PRIVATE_KEY` | Private key for trading operations | — | No (only for order placement) |

### Plugin Activation

The plugin activates automatically when `CLOB_API_URL` is set:

```typescript
// In character config
...(process.env.CLOB_API_URL ? ['@elizaos/plugin-polymarket'] : []),
```

---

## Available Actions

### GET_ALL_MARKETS

Retrieve all available prediction markets from Polymarket.

**Triggers**: `LIST_MARKETS`, `SHOW_MARKETS`, `GET_MARKETS`, `FETCH_MARKETS`, `ALL_MARKETS`

**Examples**:
- "Show me all available prediction markets"
- "What markets can I trade on Polymarket?"
- "List all active prediction markets"

**Returns**: Market questions, categories, active status, end dates, token info, pagination.

### GET_PRICE_HISTORY

Historical price data for technical analysis and trend identification.

**Triggers**: `PRICE_HISTORY`, `GET_PRICE_HISTORY`, `HISTORICAL_PRICES`, `PRICE_CHART`

**Examples**:
- "Get price history for token 123456 with 1d interval"
- "Show me 1h price chart for token 456789"

**Intervals**: `1m`, `5m`, `1h`, `6h`, `1d`, `1w`, `max`

**Returns**: Time-series data, price trends, highs/lows, recent price points.

### GET_ORDER_BOOK

Order book summary (bids and asks) for a specific token.

**Triggers**: `ORDER_BOOK`, `BOOK_SUMMARY`, `BID_ASK`, `MARKET_DEPTH`

**Examples**:
- "Show order book for token 123456"
- "What's the bid/ask spread for this token?"

**Returns**: Market depth, best bid/ask, spread, top 5 levels.

### GET_ORDER_BOOK_DEPTH

Bulk order book data for multiple tokens.

**Triggers**: `ORDER_BOOK_DEPTH`, `BOOK_DEPTH`, `BULK_BOOKS`

**Examples**:
- "Get depth for tokens 123456, 789012"

**Returns**: Array of order books with summary statistics.

### DELETE_API_KEY

Revoke an existing API key for security management.

**Triggers**: `DELETE_API_KEY`, `REVOKE_API_KEY`

---

## TypeScript API

```typescript
import {
  retrieveAllMarketsAction,
  getPriceHistory,
  getOrderBookSummaryAction,
  getOrderBookDepthAction
} from "@elizaos/plugin-polymarket";

// Get all markets
const markets = await retrieveAllMarketsAction.handler(runtime, message, state);

// Price history
const prices = await getPriceHistory.handler(runtime, message, state);

// Order book
const book = await getOrderBookSummaryAction.handler(runtime, message, state);

// Bulk depth
const depth = await getOrderBookDepthAction.handler(runtime, message, state);
```

---

## Architecture

```
src/
├── actions/                    # Action implementations
│   ├── retrieveAllMarkets.ts   # List all markets
│   ├── getMarketDetails.ts     # Market details
│   ├── getOrderBookSummary.ts  # Single order book
│   ├── getOrderBookDepth.ts    # Bulk order books
│   ├── getBestPrice.ts         # Best bid/ask
│   ├── getMidpointPrice.ts     # Midpoint price
│   └── getSpread.ts            # Spread calculation
├── utils/
│   ├── clobClient.ts           # CLOB API client
│   └── llmHelpers.ts           # LLM parameter extraction
├── templates.ts                # LLM prompt templates
├── plugin.ts                   # Plugin definition
└── index.ts                    # Entry point
```

---

## Supported Markets

Works with all Polymarket prediction markets:

- **Politics** — Elections, legislation, government policy
- **Crypto** — BTC/ETH price targets, DeFi events, protocol upgrades
- **Sports** — Game outcomes, tournament predictions
- **Economics** — GDP, inflation, interest rates, employment
- **Geopolitics** — International events, conflicts, trade
- **Current Events** — News-driven binary markets

---

## Development

```bash
# Build
npm run build

# Test
npm run test:component

# Coverage
npm run test:coverage

# E2E tests
npm run test:e2e
```

---

## ElizaOKClaw Agent Profile

| | |
|---|---|
| **Name** | ElizaOKClaw |
| **Moltbook** | [@eos_ElizaOKClaw](https://www.moltbook.com/u/eos_ElizaOKClaw) |
| **Framework** | [ElizaOS](https://elizaos.ai) |
| **Cloud** | [elizacloud.ai](https://elizacloud.ai) |
| **Plugins** | [plugin-polymarket](https://github.com/elizaclaw/plugin-polymarket) + [plugin-moltbook](https://github.com/elizaclaw/plugin-moltbook) |
| **Status** | First elizaOS agent on Moltbook with Polymarket intelligence |

---

## Links

- **ElizaOKClaw on Moltbook**: [moltbook.com/u/eos_ElizaOKClaw](https://www.moltbook.com/u/eos_ElizaOKClaw)
- **Moltbook Plugin**: [github.com/elizaclaw/plugin-moltbook](https://github.com/elizaclaw/plugin-moltbook)
- **ElizaOS Framework**: [elizaos.ai](https://elizaos.ai)
- **ElizaOS Cloud**: [elizacloud.ai](https://elizacloud.ai)
- **Polymarket**: [polymarket.com](https://polymarket.com)
- **Polymarket API Docs**: [docs.polymarket.com](https://docs.polymarket.com/developers/CLOB/introduction)

---

## License

MIT

---

*ElizaOKClaw - Prediction market intelligence meets AI agent social networking. Powered by ElizaOS.*
