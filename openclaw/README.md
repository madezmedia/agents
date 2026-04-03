# OpenClaw Agents - Veritas-Trade OS

A verifiable, multi-chain AI trading orchestration platform built for the LabLab "AI Trading Agents" Hackathon.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    CIO (Strategy)                        │
│                   gemini-3.1-pro                         │
└────────────────────────┬────────────────────────────────┘
                         │
         ┌───────────────┼───────────────┐
         ▼               ▼               ▼
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   Sentry    │  │ Executioner │  │   Auditor   │
│  (Monitor)  │──│  (Execute)  │──│  (Verify)   │
│ flash-lite  │  │ flash-lite  │  │    pro      │
└─────────────┘  └─────────────┘  └─────────────┘
      │                │                │
      ▼                ▼                ▼
┌─────────────────────────────────────────────────────────┐
│                  Shared State (Redis)                    │
└─────────────────────────────────────────────────────────┘
      │                │                │
      ▼                ▼                ▼
  Kraken API     Aerodrome DEX    ERC-8004 Registry
   (CEX)           (Base)             (Base)
```

## Agents

| Agent | Model | Purpose |
|-------|-------|---------|
| **CIO** | gemini-3.1-pro | Strategic orchestration, risk parameters, user interface |
| **Sentry** | gemini-3.1-flash-lite | Real-time market monitoring (Kraken + Aerodrome) |
| **Executioner** | gemini-3.1-flash-lite | High-speed trade execution via Risk Router |
| **Auditor** | gemini-3.1-pro | EIP-712 signing, ERC-8004 attestations |

## Quick Start

### Prerequisites
- Docker & Docker Compose
- Kraken API credentials
- Gemini API key
- Base network wallet with ETH for gas

### Local Development

```bash
# Clone and setup
cd openclaw
cp .env.example .env
# Edit .env with your credentials

# Start all services
docker-compose up -d

# View logs
docker-compose logs -f openclaw-gateway
```

### Deploy to Elest.io

1. **Install Elestio CLI**
```bash
npm install -g elestio
```

2. **Authenticate**
```bash
elestio login --email "you@company.com" --token "your-api-token"
# Get token from: https://dash.elest.io/account/security
```

3. **Deploy**
```bash
# Option 1: Using config file
elestio deploy --config elestio.yml

# Option 2: Interactive
elestio deploy
```

4. **Set Environment Variables**
- Go to Elestio dashboard
- Navigate to your service
- Add all required environment variables from `.env.example`

5. **Access Your Service**
- Elestio provides: `https://your-service.elestio.app`
- SSL is auto-provisioned

## Configuration

### Risk Parameters (via CIO)

```
User: "Set max leverage to 2x"
User: "Be more conservative today"
User: "What's our current exposure?"
```

### State Files

| File | Description |
|------|-------------|
| `state/market_data.json` | Current market conditions from Sentry |
| `state/health_score.json` | Market health score (0-100) |
| `state/positions.json` | Current portfolio positions |
| `state/executions.json` | Trade execution history |
| `state/attestations.json` | ERC-8004 on-chain attestations |
| `state/audit_reports.json` | Periodic audit summaries |

## ERC-8004 Integration

The Auditor signs all trade intents with EIP-712 and publishes attestations:

```solidity
// On-chain attestation
struct TradeAttestation {
    bytes32 intentId;
    bytes32 rationaleHash;
    uint8 healthScore;
    uint256 timestamp;
    bytes signature;
}
```

## Hackathon Targets

- **$10,000** - Best Trustless Trading Agent
- **$1,200** - Social Engagement (building in public)

## Success Metrics

- [ ] Autonomous trades with valid ERC-8004 attestations
- [ ] Survives market volatility via Risk Router
- [ ] Other teams query our Auditor Registry
- [ ] Real-time Risk Score dashboard

## License

MIT
