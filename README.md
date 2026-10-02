# GARUDA — Crypto Fraud Attribution Portal

**Operational tool for Indian cybercrime officers to attribute a victim-reported crypto wallet to a real exchange (VASP), score recoverable value, and draft a SAHYOG-ready freeze notice — in minutes, not days.**

Built for **Ministry of Home Affairs / I4C — Problem Statement ID-26183**  
*“Real-time identification of fraud-linked cryptocurrency exchanges from victim-reported wallet addresses through automated blockchain analytics.”*

**Live demo:** https://garuda-i4c-crypto-fraud-portal.vercel.app/trace

**Clean source (no build artifacts):** https://github.com/sandeepkaringu2-bot/GARUDA-I4C-Crypto-Fraud-Portal  
**Full app branch:** https://github.com/sandeepkaringu2-bot/CryptoFraudDetectionPortel/tree/clean/real-world-ready

---

## Why this exists (real operational gap)

When a victim reports a crypto scam on NCRP:

1. The only lead is often a wallet string (BTC / ETH / TRC-20 USDT).
2. Stolen funds typically reach an exchange or bridge within **minutes to a few hours**.
3. After roughly **48 hours**, cash-out or cross-chain bridging makes recovery rare.
4. Indian FIU-registered VASPs can freeze funds in **6–12 hours** — but only with a correctly targeted, legally grounded notice.
5. Manual multi-hop tracing + notice drafting routinely exceeds that window.

**GARUDA closes that window.**

---

## Officer workflow (10 minutes)

| Step | Action |
|------|--------|
| 1 | Open **Trace**, paste victim wallet + NCRP complaint ID |
| 2 | Engine walks ≤6 hops (live BTC / ETH / TRON when available) |
| 3 | Screens mixers, bridges, sanctions; clusters co-spent wallets |
| 4 | Ranks Indian VASP endpoints + recoverable ₹ |
| 5 | **Generate Notice** → SAHYOG draft (CrPC 91, PMLA 17, IT Act) |
| 6 | Bilingual officer brief (EN / HI) + hash-chained audit log |

See **[OPERATIONS.md](./OPERATIONS.md)** for the full pilot checklist and escalation matrix.

---

## Pipeline

| Step | Capability | Code |
|------|------------|------|
| Intake | Wallet + NCRP ID | Cases, Ingest, Trace |
| Chain detect | BTC / ETH / TRON | `live.ts` |
| Live hops | Blockstream, EVM explorers, TronGrid | `live.ts` |
| Graph walk | Max 6 hops, cycle guard | `engine.ts` |
| Screening | Sanctions, mixers, bridges | `registry.ts`, `patterns.ts` |
| Clustering | Co-spend analysis | `engine.ts` |
| Risk | Transparent 0–100 score | `ml-risk.ts` |
| Notice | SAHYOG EN/HI draft | `notices.ts` |
| Brief | Bilingual summary | `ai-brief.ts` |
| Audit | SHA-256 hash chain | `store.ts` |

---

## Tech stack

| Layer | Choice |
|-------|--------|
| Full-stack | TanStack Start (React 19, file routes, server functions) |
| UI | Radix UI + Tailwind CSS v4 |
| State | Zustand + localStorage (pilot; no forced login) |
| Live chain | Blockstream (BTC), public EVM explorers, TronGrid (TRC-20) |
| Hosting | Vercel |

---

## Real-world readiness

| Capability | Status |
|------------|--------|
| BTC live hops | Production-ready |
| ETH / TRON live hops | Operational |
| Mixer / bridge detection | Operational |
| Indian VASP directory + nodal contacts | Operational |
| Deposit-address attribution | Pilot (demo clusters + live match; LEA feeds for production) |
| SAHYOG notice draft | Production-ready **as draft** (officer must sign & send) |
| Direct SAHYOG / NCRP API | Local outbox only |
| Multi-officer auth | Designed; off for SIH demo |

---

## Run locally

```bash
npm install
npm run dev
```

App: `http://localhost:8080`

```bash
npm run typecheck
npm run build
```

---

## Changelog (clean release)

### Added
- **`.gitignore`** — excludes `.vercel/`, `node_modules/`, env files, local DB, build output
- **`OPERATIONS.md`** — officer playbook, legal basis, escalation matrix, pilot checklist
- **Hardened freeze notices** (`notices.ts`) — SLA line, 180-day preservation, 72h withdrawal block, recoverable flag, explicit “draft only / officer must sign” disclaimer (EN + HI)
- **Production notes** on VASP registry — demo clusters vs LEA deposit feeds
- **Package identity** — renamed to `garuda-i4c-crypto-fraud-portal`

### Changed
- **README** rewritten for operational / I4C pilot audience (workflow, honest limits, multi-chain status)
- **AGENTS.project.md** — real-world scope and pilot next steps

### Removed / prevented
- Future commits of **Vercel build output** (via `.gitignore`)
- New clean public repo without historical `.vercel` blobs: [GARUDA-I4C-Crypto-Fraud-Portal](https://github.com/sandeepkaringu2-bot/GARUDA-I4C-Crypto-Fraud-Portal)

### Limits (by design)
- Notices are **drafts** — not auto-sent to exchanges
- No private exchange deposit databases without authorised feeds
- Auth remains off for SIH demo (local case store)

---

## Licence & attribution

Built for SIH / I4C problem 26183.  
Not affiliated with any exchange. VASP names and public compliance contacts are used for lawful interdiction workflow demonstration only.
