# Archer — Stock Packs on Robinhood Chain

Rip a pack, win **1 guaranteed full share** of a tokenized stock. Instant 90% buyback at oracle price. Archer is an independent concept app built on Robinhood Chain — not affiliated with or endorsed by Robinhood Markets.

## Contents

- **`SPEC.md`** — full technical spec: architecture (Next.js + Privy auth/onramps), smart contracts (PackSale, RipEngine, PrizeVault, BuybackDesk), provably-fair randomness (commit-reveal → VRF), treasury/custody, 90% buyback mechanics, EV model derived from 11 live card-gacha platforms, capital requirements, and the $10K single-pack pilot below.
- **`prototype.html`** — clickable prototype (open in a browser, mobile viewport). Brat-lime pop-art theme, Archer branding, onboarding with **KYC + region gating** (geo-IP and ID-country must both pass — Reg S restricted regions like the US/UK are blocked, and the UI says so honestly), one live $25 pack, coming-soon tiers, rip theater, 90% sell-back, portfolio, and **live token prices read from Robinhood Chain** (Blockscout API; production reads Chainlink AggregatorV3 feeds).

## The pilot — one $100 pack, 6 canonical tickers

Inventory is restricted to the **canonical token list at docs.robinhood.com/chain/contracts** (same-ticker tokens at other addresses are not Robinhood Stock Tokens). $100 minimum pack; USAR is the cheap "dud" tier that funds the win tiers. Live onchain token prices (2026-07-12):

| Tier | Ticker | Odds | Prize value |
|---|---|---|---|
| Base | USAR (USA Rare Earth) | 27.0% | $18.54 |
| Common | SLV (iShares Silver) | 40.0% | $53.95 |
| Uncommon | BABA (Alibaba) | 24.0% | $96.45 |
| Rare | NVDA (NVIDIA) | 7.0% | $196.53 |
| Epic | TSLA (Tesla) | 1.9% | $428.32 |
| Grail | SNDK (Sandisk) | 0.1% | $1,956.81 |

**"Money mode" odds** — commons pay 19–54¢ on the dollar at 67% combined probability, the aggressive end of the card-gacha market. **EV $73.59 (73.6% of price)** · 9% chance to pull above price · **~$31.56 gross margin/pack (31.6%)** at a 70% instant-sellback rate and 90% buyback. Headline stays: "win a $1,957 stock from a $100 pack."

Tradeoff to know: the UI discloses EV honestly, so the pack page shows ~74% avg pull value, vs the 96–105% every platform in the RipCheck dataset displays (their margin hides in comps and buyback spreads instead). A softer preset — USAR 15 / SLV 40 / BABA 32 / NVDA 10 / TSLA 2.9 / SNDK 0.1 — displays ~89% EV and still clears ~$17/pack, roughly 2–3× Courtyard-style margins.

**Minimum inventory to launch** (covers a ~50-pack opening run; buybacks recycle to the shelf):

| Ticker | Qty | Cost |
|---|---|---|
| USAR | 25 | $464 |
| SLV | 35 | $1,888 |
| BABA | 20 | $1,929 |
| NVDA | 4 | $786 |
| TSLA | 2 | $857 |
| SNDK | 1 | $1,957 |
| **Shelf total** | | **$7,881** |

Plus buyback USDG float $1,000 and gas/infra ~$300 → **~$9,180 all-in — comfortably inside $10K**, and at ~$31/pack margin the treasury replenishes fast. SLV is the mass tier (40%) — restock weekly from revenue. A single SNDK sell-back (~$1,761) exceeds the float: pause the grail tier after a hit until revenue restocks.

## Status

Concept draft, July 2026. Prices refresh live; verify before relying on figures.

**Compliance note:** Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons. The prototype models compliant gating (geo-IP + KYC document-country cross-check) — helping restricted users evade via VPN would defeat the legal structure the product depends on. Securities/gaming counsel required before launch.
