# GARUDA — Officer operations guide

For cyber cell / I4C pilots. Keep this short; the product is designed to be usable under time pressure.

## When to use GARUDA

Use as soon as an NCRP complaint includes a **crypto wallet** (BTC, ETH/USDT, TRC-20 USDT) and the victim amount is material.

Do **not** wait for a blockchain analyst if the 48-hour cash-out window is open.

## Standard playbook (10 minutes)

1. **Cases / Ingest** — open or create the complaint (NCRP ID, state, typology, amount).
2. **Trace** — paste the victim-reported wallet. Prefer **live** mode.
3. Review the hop graph:
   - Green / high priority = Indian FIU-registered VASP with recoverable value
   - Mixer / Tornado = treat as terminal for recovery; preserve pre-mix hops as evidence
   - Foreign VASP = still send freeze request + plan MLAT / LEA portal if INR recovery is needed
4. Open the top recommendation → **Generate Notice**.
5. Review the draft (officer name, station, exact wallets, amount). Sign and send via official SAHYOG / nodal email channel.
6. Save the bilingual brief into the case file.
7. Confirm the Evidence page shows the audit entries for this session.

## Legal basis used in drafts

- **Section 91 CrPC** — production / preservation of documents and electronic records
- **Section 17 PMLA** — attachment of proceeds of crime
- **IT Act** — preservation of digital evidence and subscriber information
- I4C / MHA advisories on crypto-asset tracing

The generated text is a **draft**. Local standing orders and the nodal officer’s preferred format may require edits before transmission.

## Chain coverage

| Chain | Live source | Typical scam asset |
|-------|-------------|--------------------|
| Bitcoin | Blockstream API | BTC |
| Ethereum / EVM | Public explorer APIs | ETH, USDT ERC-20 |
| TRON | TronGrid | USDT TRC-20 (very common in India) |

If live hops return empty (new wallet, rate limit, or indexer lag), use the simulated path only for training — re-run live before sending a real notice.

## What GARUDA will not do

- Send the notice to the exchange automatically
- Access private exchange deposit databases without authorised feeds
- Replace FIU-IND / ED / formal investigation
- Guarantee freeze success (SLA is on the VASP side)

## Evidence hygiene

- Export the hash-chained audit log with the dossier.
- Keep original NCRP complaint number on every notice.
- Record the exact time the notice was transmitted (outside the app if sent by email).

## Escalation

| Situation | Action |
|-----------|--------|
| Funds already at Indian VASP | Freeze notice first, then full investigation |
| Funds in mixer | Preserve graph; recovery unlikely post-mix |
| Foreign VASP only | Freeze request + LEA portal / MLAT track |
| Multiple NCRP hits on same cluster | Priority; note correlation in brief |
| Amount still moving | Re-trace immediately; update notice wallet list |

## Pilot checklist (station)

- [ ] Officer can open Trace and paste a live BTC / TRON address
- [ ] Notice PDF or text can be copied into official channel
- [ ] Hindi brief is readable for non-specialist supervisors
- [ ] Audit export works without internet (after the session)
- [ ] Local SOP maps GARUDA notice → SAHYOG desk
