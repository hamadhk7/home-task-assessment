# ChainQuest — Submission

**Name:** Hammad Hassan Khan  
**Email:** hammadhk1717@gmail.com  
**Contract address (localhost):** `0x5FbDB2315678afecb367f032d93F642f64180aa3`  
**GitHub repo:** https://github.com/hamadhk7/home-task-assessment

---

## How to run

### Prerequisites

- Node.js (v20+)
- MetaMask browser extension

### 1. Install dependencies

```bash
npm install
npm install --prefix contracts
```

### 2. Start local blockchain

```bash
npm run contracts:node
```

### 3. Deploy contracts (new terminal)

```bash
npm run contracts:deploy
```

Copy the output addresses into `.env.local` at the project root:

```
NEXT_PUBLIC_QUEST_ESCROW_ADDRESS=0x5FbDB2315678afecb367f032d93F642f64180aa3
NEXT_PUBLIC_MOCK_USDC_ADDRESS=0xe7f1725E7734CE288F8367e1Bb143E90bb3F0512
NEXT_PUBLIC_CHAIN_ID=31337
NEXT_PUBLIC_RPC_URL=http://127.0.0.1:8545
```

### 4. Start the UI (new terminal)

```bash
npm run dev
```

Open **http://localhost:3000**

### 5. MetaMask setup

- Network: chain ID **31337**, RPC `http://127.0.0.1:8545`
- Import Hardhat accounts:
  - Account A (poster): `0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80`
  - Account B (worker): `0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d`

### 6. Run tests

```bash
npm run test
```

All 9 scenarios (A–I) pass.

---

## What was implemented

### Part A — Smart Contract (`contracts/contracts/QuestEscrow.sol`)

Full implementation of the `QuestEscrow` contract with:

- **Quest lifecycle**: Open → Accepted → Submitted → Completed/Cancelled/Refunded
- **Dual payment support**: Native ETH (`token == address(0)`) and ERC20 tokens via `SafeERC20`
- **3% platform fee** (`FEE_BPS = 300`) deducted on payout, tracked per token in `availableFees`
- **Access controls**: poster-only for approve/cancel/refund; worker-only for accept/submit/claimTimeout
- **Timeout mechanics**: worker can claim payout after review deadline; poster can refund after review deadline
- **Reentrancy protection** via OpenZeppelin's `ReentrancyGuard` on all functions that transfer funds
- **Owner fee withdrawal** restricted to contract owner via `Ownable`

### Part B — Wallet Hooks (`lib/hooks/useQuestEscrow.ts`)

Implemented `useCreateQuest` and `useQuestActions` hooks using `useWriteContract` + `useWaitForTransactionReceipt`:

- `createEthQuest`: sends `createQuest` tx with ETH value
- `accept`, `submit`, `approve`, `claimTimeout`, `cancel`, `refund`: all wired to their respective contract functions
- Pending state tracked across both write and confirmation phases

### Part C — UI Checklist

Screenshots in `assets/`:

| Step | Account | Action | Status |
|------|---------|--------|--------|
| 1 | A | Wallet connected in header | See screenshots |
| 2 | A | Create quest on `/quests/create` | See screenshots |
| 3 | A | Quest visible on `/quests` (Open) | See screenshots |
| 4 | B | Accept quest on `/quests/[id]` | See screenshots |
| 5 | B | Submit deliverable | See screenshots |
| 6 | A | Approve & pay → Completed | See screenshots |
| 7 | B | Balance ≈ 97% of reward | See screenshots |
