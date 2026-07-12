# Archer — Stock Packs on Robinhood Chain

Rip a pack, win **1 guaranteed full share** of a tokenized stock. Instant 90% buyback at oracle price. Archer is an independent concept app built on Robinhood Chain — not affiliated with or endorsed by Robinhood Markets.

## Contents

- **`SPEC.md`** — full technical spec: architecture (Next.js + Privy auth/onramps), smart contracts (PackSale, RipEngine, PrizeVault, BuybackDesk), provably-fair randomness (commit-reveal → VRF), treasury/custody, 90% buyback mechanics, EV model derived from 11 live card-gacha platforms, capital requirements, and the $10K single-pack pilot below.
- **`prototype.html`** — clickable prototype (open in a browser, mobile viewport). Brat-lime pop-art theme, Archer branding, onboarding with **KYC + region gating** (geo-IP and ID-country must both pass — Reg S restricted regions like the US/UK are blocked, and the UI says so honestly), one live $25 pack, coming-soon tiers, rip theater, 90% sell-back, portfolio, and **live token prices read from Robinhood Chain** (Blockscout API; production reads Chainlink AggregatorV3 feeds).

## The $10K pilot — $25 mini + $100 starter, 6 canonical tickers

Inventory is restricted to the **canonical token list at docs.robinhood.com/chain/contracts** (same-ticker tokens at other addresses are not Robinhood Stock Tokens). USAR (USA Rare Earth) trades at ~$18.54 with 1,400+ holders, which unlocks a $25 mini pack.

**$25 mini pack** (EV $24.43 · 97.7% · floor 74%): USAR 90% ($18.54) · SLV 8% ($53.95) · BABA 1.2% ($96.45) · NVDA 0.5% ($196.53) · TSLA 0.3% ($428.32) — a 17× TSLA grail on a $25 pack.

**$100 starter pack** — live onchain token prices (2026-07-12):

| Tier | Ticker | Odds | Prize value |
|---|---|---|---|
| Base | SLV (iShares Silver) | 44.0% | $53.95 |
| Common | BABA (Alibaba) | 48.0% | $96.45 |
| Rare | NVDA (NVIDIA) | 6.0% | $196.53 |
| Epic | TSLA (Tesla) | 1.6% | $428.32 |
| Grail | SNDK (Sandisk) | 0.4% | $1,956.81 |

**EV $96.51 (96.5% of price)** · floor 54% · buyback-adjusted 86.9% · ~$10.25 gross margin/pack (10.2%) at a 70% instant-sellback rate. "Win a $1,957 stock from a $100 pack" is the headline.

**$10K allocation (both packs):** shelf inventory $8,486 (100 USAR + 20 SLV + 20 BABA + 4 NVDA + 2 TSLA + 1 SNDK), buyback USDG float $1,200, gas + infra ~$300. Buybacks recycle tokens to the shelf, and the mini pack's cheap base tier (USAR) means most pulls barely dent inventory. Watch-outs: a single SNDK sell-back (~$1,761 cash) exceeds the float — pause the $100 grail tier after a hit until revenue restocks it — and running two packs on $10K leaves zero slack, so $12–15K is the comfortable number. Alternatives: launch mini-only (~$4K total need) or swap SNDK for a second TSLA for smoother variance. Verdict: **viable, with the $25 mini pack as the safest wedge.**

## Status

Concept draft, July 2026. Prices refresh live; verify before relying on figures.

**Compliance note:** Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons. The prototype models compliant gating (geo-IP + KYC document-country cross-check) — helping restricted users evade via VPN would defeat the legal structure the product depends on. Securities/gaming counsel required before launch.
