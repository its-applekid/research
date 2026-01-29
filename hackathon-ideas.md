# ETH Global Hack Money — Actions SDK Project Ideas
*Brainstormed by Applekid — January 29, 2026*

## Available SDK Features for Hackathon

| Feature | Status | Provider |
|---------|--------|----------|
| **Lending** | ✅ Live | Morpho, Aave |
| **Borrowing** | ✅ Ready | Aave |
| **Swap** | ✅ Ready | Uniswap |
| **Bridge** | 🔜 In Progress | TBD |

---

# 🏦 Serious / Fintech Ideas

### 1. **Yield-Optimized Savings Account**
A no-frills savings account that automatically allocates deposits across the best-yielding lending markets.

**Core Flow:**
- User deposits USDC
- Backend evaluates Morpho vs Aave APYs across chains
- Auto-deposits to best market
- User sees simple "balance + earned" view

**Differentiation:** Bank-like UX. No market selection, no DeFi jargon. Just "deposit" and "withdraw."

**Tech:** Actions SDK lending (multi-provider), React frontend

---

### 2. **Credit Line Dashboard**
Borrow against deposited collateral with a clean credit-card-style interface.

**Core Flow:**
- Deposit collateral (ETH, WETH)
- See available credit limit
- Borrow stablecoins up to limit
- Track health factor with simple "safe/warning/danger" indicators

**Differentiation:** Makes borrowing feel like a credit card, not a liquidation minefield.

**Tech:** Actions SDK lend + borrow (Aave), health factor calculations

---

### 3. **Smart Treasury for DAOs/Teams**
Multi-sig compatible treasury that earns yield on idle funds.

**Core Flow:**
- Team deposits treasury funds
- Funds auto-deposited to lending markets
- Yield compounds or streams to designated wallet
- Supports withdrawal policies (approval thresholds)

**Differentiation:** "Your treasury is losing money sitting idle" → solved

**Tech:** Actions SDK lending + smart wallet support

---

### 4. **Dollar-Cost-Average + Earn**
Recurring buy + auto-lend in one flow.

**Core Flow:**
- User sets: "Buy $100 ETH every week"
- After purchase, ETH goes into lending market
- Earnings compound
- User sees total position value over time

**Differentiation:** Combines DCA and yield — two things people do separately

**Tech:** Actions SDK swap (Uniswap) + lend, cron/scheduler

---

### 5. **Invoice Financing**
Freelancers/businesses deposit unpaid invoices as collateral stand-in, borrow against future receivables.

**Core Flow:**
- Submit invoice (mock or via integration)
- Receive loan at % of invoice value
- Repay when invoice is paid
- Collateral returned

**Differentiation:** Real-world use case, underserved market

**Tech:** Actions SDK borrow, basic invoice tracking

---

### 6. **Payroll Yield**
Companies deposit payroll funds early, earn yield until payday, then distribute.

**Core Flow:**
- Company deposits $50k for payroll on the 1st
- Funds earn yield until the 15th
- Auto-distribute to employee wallets on payday
- Company keeps the yield (or shares it)

**Differentiation:** B2B angle, solves real timing problem

**Tech:** Actions SDK lending + scheduled withdrawals + batch transfers

---

# 🎮 Fun / Gamified Ideas

### 7. **Yield Battles**
Competitive yield farming game — who earns the most over 7 days?

**Core Flow:**
- Create/join a "battle" with friends
- Everyone deposits same amount
- Real-time leaderboard shows earnings
- Winner gets bragging rights (or prize pool)

**Gamification:**
- Achievements: "First to $10 earned", "Diversification King"
- Strategy visibility: see what markets others pick
- Trash talk chat

**Tech:** Actions SDK lending + real-time position tracking + social layer

---

### 8. **DeFi Piggy Bank**
Goal-oriented savings with visual progress and celebrations.

**Core Flow:**
- Set goal: "Vacation - $2,000 by June"
- Deposit into lending markets
- Watch progress bar fill (principal + yield)
- Confetti explosion when goal is hit

**Gamification:**
- Streak rewards for consistent deposits
- Milestone badges
- Share achievements socially

**Tech:** Actions SDK lending + goal tracking + fun UI

---

### 9. **Lending Market "Tinder"**
Swipe-based discovery for lending opportunities.

**Core Flow:**
- Cards show market: APY, asset, chain, risk level
- Swipe right to add to portfolio
- Swipe left to skip
- "It's a Match!" when you deposit

**Gamification:**
- Daily "fresh markets" to review
- "Hot markets" trending section
- Compatibility scores

**Tech:** Actions SDK market data + swipe UI

---

### 10. **Crypto Tamagotchi**
Virtual pet that grows based on your yield earnings.

**Core Flow:**
- Deposit funds → pet hatches
- Yield = food for pet
- Pet evolves as earnings milestone hit
- Pet dies if you withdraw too early (sad!)

**Gamification:**
- Pet customization
- Leaderboard of biggest/oldest pets
- Rare evolutions for high earners

**Tech:** Actions SDK lending + pet state machine + pixel art

---

### 11. **Prediction Market: APY Edition**
Bet on which lending market will have highest APY next week.

**Core Flow:**
- See current APYs across markets
- Predict which will be highest in 7 days
- Stake prediction
- Winners split the pool

**Tech:** Actions SDK market data + simple betting contract

---

### 12. **Leverage Roulette** (Degen Mode)
Spin the wheel to determine your leverage ratio, then YOLO.

**Core Flow:**
- Deposit collateral
- Spin wheel: 1.5x, 2x, 3x, 5x, 10x
- Auto-executes borrow at that leverage
- Live PnL tracker with dramatic UI

**Gamification:**
- "Survived the spin" badges
- Liquidation hall of shame
- High score leaderboard

**Tech:** Actions SDK lend + borrow, leverage calculation

---

### 13. **Yield Farm Simulator**
Idle game where you manage a virtual farm powered by real yield.

**Core Flow:**
- Deposit = plant crops
- Yield = crop growth
- Harvest = compound or withdraw
- Buy upgrades (tractors, silos) with earnings

**Gamification:**
- Seasonal events
- Rare crops for high APY markets
- Neighbor visits

**Tech:** Actions SDK lending + game loop

---

# 🌉 Cross-Chain Ideas (If Bridge Ready)

### 14. **Chain Arbitrage Finder**
Visual tool showing yield differences across chains, with one-click bridge + deposit.

**Core Flow:**
- Dashboard shows: "USDC yields 4% on Base, 6% on Optimism"
- Click "Migrate" → bridges and redeposits
- Shows net gain after fees

**Tech:** Actions SDK lending + bridge + swap

---

### 15. **Yield Passport**
Collect "stamps" by earning yield on different chains.

**Core Flow:**
- Deposit on Base → get Base stamp
- Bridge to Optimism, deposit → get OP stamp
- Complete the collection for NFT badge

**Gamification:**
- Rare stamps for new chains
- Time-limited events
- Collector leaderboard

**Tech:** Actions SDK + bridge + NFT minting

---

# 🏆 Top Picks for 2-Day Hackathon

## Serious
1. **Yield-Optimized Savings Account (#1)** — Clean, achievable, demos well
2. **Credit Line Dashboard (#2)** — Shows off borrowing, real utility

## Fun
1. **DeFi Piggy Bank (#8)** — Accessible, visual, feel-good
2. **Yield Battles (#7)** — Social, competitive, shareable

## Dark Horse
- **Crypto Tamagotchi (#10)** — Memorable, viral potential, judges will remember it

---

Let me know which direction resonates and I'll start on a detailed spec! 🍎
