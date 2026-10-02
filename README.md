# GARUDA — Crypto Fraud Attribution Portal

**Operational tool for Indian cybercrime officers to attribute a victim-reported crypto wallet to a real exchange (VASP), score recoverable value, and draft a SAHYOG-ready freeze notice — in minutes, not days.**

Built for **Ministry of Home Affairs / I4C — Problem Statement ID-26183**  
*“Real-time identification of fraud-linked cryptocurrency exchanges from victim-reported wallet addresses through automated blockchain analytics.”*

**Live demo:** https://garuda-i4c-crypto-fraud-portal.vercel.app/trace

---

## Why this exists (real operational gap)

When a victim reports a crypto scam on the National Cyber Crime Reporting Portal (NCRP):

1. The only lead is often a wallet string (BTC / ETH / TRC-20 USDT).
2. Stolen funds typically reach an exchange or bridge within **minutes to a few hours**.
3. After roughly **48 hours**, cash-out or cross-chain bridging makes recovery rare.
4. Indian FIU-registered VASPs (WazirX, CoinDCX, ZebPay, etc.) can freeze funds quickly — **often within a 6–12 hour SLA** — but only if the officer sends a **correctly targeted, legally grounded notice** naming the exact deposit cluster.
5. Manual multi-hop tracing + notice drafting routinely exceeds that window.

**GARUDA closes that window.** The officer pastes the wallet (and optional NCRP ID). The system walks the graph, screens sanctions and repeat complaints, clusters co-spent addresses, identifies the highest-value Indian VASP sink, and drafts the freeze notice plus a bilingual officer brief.

---

## End-to-end officer workflow (real use case)

| Time | Action |
|------|--------|
| T+0 | Victim files NCRP complaint (wallet + amount + typology). |
| T+minutes | Cyber cell officer opens **Trace**, pastes wallet + complaint ID. |
| Seconds | Engine walks outbound hops (max 6, cycle-guarded), checks OFAC / FIU-style sanctions, mixers, bridges, and other NCRP complaints on file. |
| Seconds | Co-spend clustering groups wallets likely controlled by the same operator. |
| Seconds | Highest-leverage **Indian VASP** endpoint is ranked; recoverable ₹ is estimated. |
| ~1 min | Officer reviews graph → **Generate Notice**. |
| ~1 min | SAHYOG-style freeze request (CrPC 91, PMLA 17, IT Act) is drafted, addressed to the VASP nodal officer, ready to sign and send. |
| Parallel | Bilingual (EN / HI) plain-language brief for the case file. |
| Continuous | Every action is written to a **SHA-256 hash-chained audit log** for court integrity. |

This is the production use case the product is designed around — not a generic “blockchain explorer.”

---

## What GARUDA does (pipeline)

| Step | Capability | Location |
|------|------------|----------|
| 1 | Complaint / wallet intake | Cases, Ingest, Trace |
| 2 | Chain detection (BTC / ETH / TRON) | `live.ts` |
| 3 | Live hop fetch + multi-hop walk | Blockstream (BTC), Blockscout-style EVM, TronGrid (TRC-20) |
| 4 | Sanctions / mixer / bridge flags | Registry + patterns |
| 5 | Co-spend clustering | Engine |
| 6 | VASP attribution + risk score (0–100) | Registry + `ml-risk.ts` |
| 7 | Recoverable vs at-risk vs lost ₹ | Engine |
| 8 | SAHYOG freeze notice draft | `notices.ts` |
| 9 | Bilingual officer brief | `ai-brief.ts` + i18n |
| 10 | Tamper-evident audit trail | Store + Evidence |

---

## Tech stack

| Layer | Choice | Rationale |
|-------|--------|-----------|
| Full-stack | TanStack Start (React 19, file routes, server functions) | Fast SSR + type-safe server functions |
| UI | Radix UI + Tailwind CSS v4 | Accessible, dense operational UI |
| State | Zustand + localStorage | Demo / single-station use without forced login |
| Live chain data | Public indexers (Blockstream, EVM explorers, TronGrid) | No paid API key required for pilot |
| DB (optional path) | Neon / PGlite + Kysely | Ready when multi-officer persistence is required |
| Hosting | Vercel | Serverless functions match the architecture |

**Auth is intentionally off for the SIH / demo pilot** (local case store). A production pilot would enable officer accounts, station scoping, and server-side audit persistence.

---

## Architecture

```text
Victim NCRP complaint
        │
        ▼
 Officer → Trace page (wallet + case ID)
        │
        ▼
 ┌────────────────── engine ──────────────────┐
 │ detect chain → live hops (or simulated)    │
 │ walk ≤ 6 hops · cycle guard                │
 │ screen: sanctions · mixers · bridges       │
 │ cluster co-spend · match VASP registry     │
 │ risk score · recoverable INR               │
 └──────────────────┬─────────────────────────┘
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
   Freeze notice  Officer brief  Audit log
   (SAHYOG draft) (EN / HI)     (hash chain)
```

---

## Project layout

```text
src/
  routes/          Home, Cases, Trace, Dossier, Evidence, VASPs, Intel, Playbook, Ingest
  lib/garuda/
    engine.ts      Trace, clustering, recovery, recommendations
    live.ts        Live BTC / ETH / TRON hop fetch + walk
    registry.ts    VASPs, mixers, bridges, demo ledger, sample complaints
    notices.ts     SAHYOG-style freeze notice (EN)
    ai-brief.ts    Bilingual case brief
    ml-risk.ts     Feature-based risk score
    patterns.ts    Typology / fan-out / contract heuristics
    store.ts       Case + audit + SAHYOG outbox (local)
    i18n.ts        English / Hindi strings
  components/      Shell, hop graph, freeze panel, UI primitives
migrations/        Schema for optional Neon / PGlite path
scripts/           Dev env wrapper, migrate, smoke helpers
```

---

## Real-world readiness (honest status)

| Capability | Status | Notes |
|------------|--------|--------|
| BTC live hops (Blockstream) | **Production-ready** | Public API, cached, rate-limit aware |
| ETH / EVM live hops | **Operational** | Public explorers; token hops best-effort |
| TRON / TRC-20 (USDT) | **Operational** | TronGrid; critical for India USDT scams |
| Mixer / privacy-pool detection | **Operational** | Known Tornado / privacy contracts + heuristics |
| Indian VASP directory + nodal contacts | **Operational** | FIU-registered names & compliance desks |
| Deposit-address attribution | **Pilot** | Demo clusters + live matching; production needs LEA cluster feeds or exchange cooperation |
| SAHYOG notice draft | **Production-ready as draft** | Officer must review, sign, and send via official channel |
| Direct SAHYOG / NCRP API push | **Outbox only** | Queued locally; no live government API in this build |
| Multi-officer auth & central DB | **Designed, not required for demo** | Enable Better Auth + Neon when piloting across stations |
| Court-grade audit log | **Operational (local hash chain)** | Export chain of custody from Evidence |

**What “100% real world” means here**

- The **workflow** matches how cyber cells actually work under time pressure.
- **Live public ledger data** is used whenever the address is on a supported chain.
- **Legal notice language** cites the instruments officers already use (CrPC 91, PMLA 17, IT Act).
- **Limits are explicit**: the app does not auto-send notices to exchanges, does not invent KYC, and does not claim private deposit-address intelligence it does not have.

For a state / I4C pilot, the next integration steps are: (1) authorised VASP deposit-cluster feed or LEA portal, (2) officer SSO, (3) official SAHYOG outbox API.

---

## Run locally

```bash
npm install
npm run dev
```

App: `http://localhost:8080`  
Local PGlite mirrors the production schema when DB features are used — no manual Postgres setup required for the default demo path.

```bash
npm run typecheck
npm run build
```

---

## Demo path (when live hops are empty)

If a pasted address has no recent outbound activity on public indexers, GARUDA falls back to a **realistic simulated ledger** built from common Indian scam patterns:

- Fake investment / task apps → burner → layering → Indian VASP
- Sextortion / ransomware USDT flows
- Mixer and no-KYC bridge dead-ends

This keeps training and evaluation possible offline or under rate limits, while live mode is preferred for real complaints.

---

## Security & evidence notes

- No real victim PII is required to run a trace — only the wallet and optional complaint ID.
- Do not paste full NCRP PDFs or Aadhaar into the client store for a production deployment.
- Audit entries are hash-chained; export them with the case dossier for court.
- Freeze notices are **drafts**. Only an authorised officer may transmit them through official channels.

---

## Licence & attribution

Built for SIH / I4C problem 26183.  
Not affiliated with any exchange. VASP names and public compliance contacts are used for lawful interdiction workflow demonstration only.
