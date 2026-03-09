# Fourmeme Sniper Bot – BNB Chain Four.meme Sniper

<div align="center">

**Fourmeme sniper bot for BNB Smart Chain – automated token sniping, MEV-protected bundling, PancakeSwap V3**

*Four.meme sniper • BNB Chain • bloXroute bundles • PancakeSwap V3*

[![Hardhat](https://img.shields.io/badge/Built%20with-Hardhat-yellow.svg)](https://hardhat.org/)
[![Solidity](https://img.shields.io/badge/Solidity-0.8.19-blue.svg)](https://soliditylang.org/)
[![BSC](https://img.shields.io/badge/BNB%20Chain-Compatible-green.svg)](https://www.bnbchain.org/)
[![Four.meme](https://img.shields.io/badge/Four.meme-Sniper%20Bot-red.svg)](https://four.meme)

</div>

---

## Fourmeme Sniper Bot – What It Does

This repo is a **fourmeme sniper bot** for **BNB Smart Chain (BSC)**. It helps you:

- **Snipe Four.meme launches** – Detect and buy new Four.meme tokens quickly.
- **Run atomic bundles** – Use **bloXroute** so deploy + pool + buy run in one bundle (MEV protection).
- **Use PancakeSwap V3** – Create pools, set price, add liquidity, and buy in a single flow.

If you’re searching for a **fourmeme sniper bot**, **Four.meme sniper**, or **Four.meme BSC sniper**, this project is built for that use case.

---

## Project Architecture

High-level layout and how the pieces connect.

### Repository Structure

```
PancakeSwap-Fourmeme-Sniper-Bot/
├── contracts/
│   └── MyToken.sol              # ERC20 token (OpenZeppelin), used in deploy + pool flow
├── scripts/
│   ├── bloxroute.js             # Main entry: bundle builder + bloXroute submit (sniper bot)
│   ├── deploy.js                # Hardhat deploy script (e.g. Lock / contracts)
│   └── abi.js                   # PancakeSwap V3 / Uniswap V3 ABIs (Factory, Pool, SwapRouter, etc.)
├── constants/
│   └── index.js                 # RPC, bloXroute endpoint, PancakeSwap V3 addresses, WBNB, gas config
├── test/
│   └── Lock.js                  # Integration tests: token deploy, pool create, liquidity, swap (BSC fork)
├── hardhat.config.js           # Networks (BSC mainnet, testnet, hardhat fork), Solidity, gas reporter
├── package.json
└── README.md
```

### Component Roles

| Component | Role |
|-----------|------|
| **scripts/bloxroute.js** | Main fourmeme sniper bot: builds atomic bundle (token deploy → approvals → pool create → init → liquidity → buy) and submits it to bloXroute. |
| **constants/index.js** | Single place for RPC URL, bloXroute endpoint/auth, PancakeSwap V3 factory/router/NFPM addresses, WBNB, and gas defaults. |
| **scripts/abi.js** | Uniswap V3–compatible ABIs for PancakeSwap V3 (Factory, Pool, SwapRouter, token approve, etc.). |
| **contracts/MyToken.sol** | ERC20 used when the bot deploys a new token as part of the bundle. |
| **hardhat.config.js** | BSC mainnet, BSC testnet, and Hardhat network with BSC fork for local/testing. |
| **test/Lock.js** | End-to-end test on a BSC fork: deploy token, create V3 pool, add liquidity, swap (validates the flow the bot uses). |

### Data and Execution Flow

1. **Config** – `.env` (e.g. `PRIVATE_KEY`, `AUTHORIZATION_HEADER`) and optionally `constants/index.js` / env for RPC and bloXroute.
2. **Bot startup** – `node scripts/bloxroute.js` (or `npm run dev`) loads config, connects to BSC via RPC, creates wallet.
3. **Bundle build** – Bot builds signed tx list: deploy token → approve token/WBNB for NFPM → create pool → initialize pool → add liquidity → (optional) initial buy.
4. **Submission** – Signed transactions are sent to bloXroute as a single bundle for a target block (MEV protection, atomic execution).
5. **Testing** – `npm run sn` starts a BSC fork; `npm run t` runs tests against that fork to verify the same flow.

---

## How to Run This Project

### Prerequisites

- **Node.js** v16+
- **BSC RPC** (e.g. QuickNode or public endpoint)
- **bloXroute** account and API key (for bundle submission)
- **Wallet** with BNB for gas and trading
- Basic familiarity with BSC and Four.meme

### 1. Clone and install

```bash
git clone https://github.com/m4rcu5o/PancakeSwap-Fourmeme-Sniper-Bot
cd PancakeSwap-Fourmeme-Sniper-Bot
npm install
```

### 2. Environment variables

Create a `.env` in the project root:

```env
PRIVATE_KEY=your_private_key_hex
AUTHORIZATION_HEADER=your_bloxroute_authorization_header
```

Optional (used by scripts/constants if set):

- `RPC_URL` or `BSC_RPC_URL` – BSC RPC endpoint
- `BSC_FORK_URL` – RPC used for Hardhat fork (e.g. `npm run sn`)
- `BLOXROUTE_ENDPOINT` – bloXroute API (default in code if not set)
- `TOKEN_NAME`, `TOKEN_SYMBOL`, `TOKEN_SUPPLY` – for token deploy step in the bot
- `GAS_PRICE_MULTIPLIER`, `FUTURE_BLOCK_OFFSET`, `FEE_TIER`, `INITIAL_PRICE` – for bundle/build behavior

### 3. Run the Fourmeme Sniper Bot

**Mainnet (live sniper):**

```bash
npm run dev
```

or:

```bash
npm start
```

Both run `scripts/bloxroute.js`: connect to BSC, build the bundle, and submit it to bloXroute.

### 4. Local BSC fork and tests

**Start a BSC fork (separate terminal):**

```bash
npm run sn
```

This runs `npx hardhat node --network hardhat` with BSC forking (see `hardhat.config.js`).

**Run tests (with fork running):**

```bash
npm run t
```

or:

```bash
npm test
```

Tests use the local fork to deploy token, create pool, add liquidity, and swap.

### 5. Other useful commands

```bash
npx hardhat compile
npx hardhat run scripts/deploy.js
REPORT_GAS=true npx hardhat test
```

---

## Features

- **Four.meme sniper flow** – Token deploy + pool creation + liquidity + optional buy in one bundle.
- **MEV protection** – Bundles submitted via bloXroute for atomic execution and reduced front-running risk.
- **PancakeSwap V3** – Uses V3 factory, pool, and router (Uniswap V3–compatible ABIs).
- **Configurable** – Token params, gas multiplier, target block offset, fee tier, and initial price via env.
- **Fork testing** – Hardhat BSC fork and integration tests to validate the full flow before mainnet.

---

## Technology Stack

| Layer | Technology |
|-------|------------|
| Smart contracts | Solidity ^0.8.9 (OpenZeppelin ERC20) |
| Build / test | Hardhat ^2.19.5 |
| Sniper / bundles | scripts/bloxroute.js, BSC RPC, bloXroute API |
| DEX | PancakeSwap V3 (Uniswap V3–compatible SDK/ABIs) |
| RPC | Configurable (e.g. QuickNode, public BSC RPC) |

---

## Security and Risk

- Never commit or share `.env` or private keys.
- Prefer a burner or dedicated wallet for testing.
- Always test on a BSC fork first (`npm run sn` + `npm run t`).
- Crypto and meme trading are high risk; use at your own risk.

---

## Disclaimer

This project is for **educational and research purposes**. Trading and sniping on BSC and Four.meme involve financial risk. Use responsibly and understand the code and flow before running with real funds.

---

## Author and Contact

**Author:** microRustyme  
**Telegram:** [@microRustyme](https://t.me/microRustyme)  
**TG ID:** microRustyme

---

<div align="center">

**Fourmeme Sniper Bot for BNB Chain**  
*PancakeSwap V3 • bloXroute • Four.meme*

</div>
