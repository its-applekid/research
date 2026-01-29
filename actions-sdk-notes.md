# Actions SDK Research Notes
*Prepared by Applekid — January 29, 2026*

## What is Actions SDK?

Actions SDK is an **open-source TypeScript toolkit** by Optimism that simplifies integrating DeFi into applications. It provides lightweight abstractions for common DeFi operations, letting developers focus on UX instead of blockchain complexity.

**Live demo:** https://actions.optimism.io/earn
**Docs:** https://docs.optimism.io/app-developers/quickstarts/actions
**Repo:** https://github.com/ethereum-optimism/actions

---

## Core Architecture

### Monorepo Structure
```
packages/
├── sdk/           # Core TypeScript SDK
├── demo/
│   ├── frontend/  # React + Vite demo app
│   ├── backend/   # Hono backend service
│   └── contracts/ # Demo contracts (Faucet, Morpho market deployment)
```

### Key Concepts

**1. Provider Pattern**
- SDK uses pluggable "Providers" for different services
- Currently supports: Morpho, Aave (lending)
- Coming soon: Uniswap (swap), Bridge providers

**2. Wallet Abstraction**
- `Wallet` - Base wallet class with balance/transaction methods
- `SmartWallet` - Smart contract wallet support (ERC-4337)
- Supports Privy, Dynamic, Turnkey as hosted wallet providers

**3. Actions Namespace**
- `actions.wallet` - Wallet operations
- `actions.lend` - Lending operations (live)
- `actions.swap` - Swap operations (coming soon)
- `actions.borrow` - Borrow operations (coming soon)

---

## Current Capabilities

### Lending (Live)
```typescript
// Open a lending position
const receipt = await wallet.lend.openPosition({
  amount: 100,
  asset: USDC,
  marketId: { address: '0x...', chainId: 8453 }
});

// Get position info
const position = await wallet.lend.getPosition(market);

// Close position (withdraw)
const receipt = await wallet.lend.closePosition({...});
```

### Supported Assets
- ETH, WETH, USDC, USDC_DEMO, MORPHO

### Supported Chains
- Mainnet, Sepolia
- Optimism, Optimism Sepolia
- Base, Base Sepolia
- Unichain, Unichain Sepolia
- Worldchain

### Lending Providers
- **Morpho** - Morpho Blue lending markets
- **Aave** - Aave V3 lending pools

---

## Coming Soon (Per Docs)

1. **Swap** (`wallet.swap.execute`)
   - Everdred adding Uniswap support
   
2. **Borrow** (`wallet.borrow.openPosition`)
   - Leverage existing lending infrastructure

3. **Send** (`wallet.send`)
   - Simple transfers with ENS resolution

4. **Bridge Providers**
   - Cross-chain asset movement

---

## Demo App Analysis

The demo frontend (`/earn`) showcases:
- Wallet provider selection (Privy, Dynamic, Turnkey)
- Market selector with APY display
- Deposit/withdraw UI
- Activity log of transactions
- Position balance display

**UI Style:** Clean, terminal-inspired aesthetic with Optimism branding

---

## Technical Notes

- Built on **viem** for Ethereum interactions
- Uses **Pimlico** for gas sponsorship/bundling (ERC-4337)
- React frontend with Tailwind CSS
- Hono backend (lightweight, modern Node.js framework)
- CircleCI for CI/CD, Docker deployment

---

## ETH Global Hackathon Ideas

See: [hackathon-ideas.md](./hackathon-ideas.md)
