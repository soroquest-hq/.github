# SoroQuest

**Open source bounties, powered by USDC on Stellar.**

SoroQuest is a trustless bounty platform built on Stellar's Soroban smart contract platform. Project owners post work with USDC locked in escrow. Contributors claim it, do the work, and get paid automatically — no middleman, no manual payouts, no trust required.

---

## How it works

```
Owner posts bounty  →  USDC locked in escrow contract
Contributor claims  →  Bounty reserved exclusively
Work is approved    →  USDC released instantly on-chain
```

Every state change is an on-chain event. Every payout is a smart contract call. The contract is the source of truth.

---

## Live Links & Addresses

| Network | Contract ID | Live App URL |
|---------|-------------|--------------|
| **Testnet** | `CA7C6LGWPTZVUTZX7EXJVLWCQG6DVWPROBPO3FGXYKEY4W5H4HRPO5UV` | [soroquest-app.vercel.app](https://soroquest-app.vercel.app) |
| **Mainnet** | `TBD - Pending Deployment` | [soroquest-app.vercel.app](https://soroquest-app.vercel.app) |

---

## Repositories

| Repo | What it is |
|------|------------|
| [**soroquest-contracts**](https://github.com/soroquest-hq/soroquest-contracts) | Soroban escrow contract — holds USDC, enforces the bounty lifecycle |
| [**soroquest-indexer**](https://github.com/soroquest-hq/soroquest-indexer) | Go service — indexes contract events into PostgreSQL, serves a REST API |
| [**soroquest-app**](https://github.com/soroquest-hq/soroquest-app) | Next.js frontend — browse, post, claim, and approve bounties |

---

## Stack

- **Smart contract** — Rust · Soroban SDK · USDC Stellar Asset Contract
- **Indexer** — Go · PostgreSQL · chi router · Stellar Go SDK
- **Frontend** — Next.js 14 · TypeScript · Tailwind CSS · Freighter wallet

---

## Status

> 🚧 Active development. Testnet deployment in progress.

- [x] Contract architecture and data model
- [x] Indexer ingestion loop and REST API shape
- [x] Frontend scaffold with all pages, hooks, and components
- [ ] Contract deployment on testnet
- [ ] Indexer live on Render
- [ ] App live on Vercel

---

## Contributing

Each repo has its own `CONTRIBUTING.md`. The short version:

1. Browse [open bounties on soroquest-app](https://github.com/soroquest-hq/soroquest-app) once it's live
2. Or pick an issue in any repo and open a PR
3. No private keys, no trust assumptions — everything runs through Freighter and Soroban

---

## Security

Report vulnerabilities to **security@soroquest.xyz** — not in a public issue. See [`SECURITY.md`](https://github.com/soroquest-hq/soroquest-contracts/blob/main/SECURITY.md) in any repo.

---

<sub>Built on Stellar · Powered by Soroban · Paid in USDC</sub>
