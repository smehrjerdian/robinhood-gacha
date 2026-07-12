# Archer — Stock Packs on Robinhood Chain

Rip a pack, win **1 guaranteed full share** of a tokenized stock. Instant 90% buyback at oracle price. Archer is an independent concept app built on Robinhood Chain — not affiliated with or endorsed by Robinhood Markets.

## Contents

- **`SPEC.md`** — full technical spec: architecture (Next.js + Privy auth/onramps), smart contracts (PackSale, RipEngine, PrizeVault, BuybackDesk), provably-fair randomness (commit-reveal → VRF), treasury/custody, 90% buyback mechanics, EV model derived from 11 live card-gacha platforms, capital requirements, and the $10K single-pack pilot below.
- **`prototype.html`** — clickable prototype (open in a browser, mobile viewport). Brat-lime pop-art theme, Archer branding, onboarding with **KYC + region gating** (geo-IP and ID-country must both pass — Reg S restricted regions like the US/UK are blocked, and the UI says so honestly), one live $25 pack, coming-soon tiers, rip theater, 90% sell-back, portfolio, and **live token prices read from Robinhood Chain** (Blockscout API; production reads Chainlink AggregatorV3 feeds).

## The $10K pilot — one $100 pack, 5 canonical tickers

Inventory is restricted to the **canonical token list at docs.robinhood.com/chain/contracts** (same-ticker tokens at other addresses are not Robinhood Stock Tokens). The cheapest canonical token with live pricing is SLV (~$54), which sets the minimum viable pack at ~$100 — a $25 pack ships later, when sub-$50 tokens list. Live onchain token prices (2026-07-12):

| Tier | Ticker | Odds | Prize value |
|---|---|---|---|
| Base | SLV (iShares Silver) | 44.0% | $53.95 |
| Common | BABA (Alibaba) | 48.0% | $96.45 |
| Rare | NVDA (NVIDIA) | 6.0% | $196.53 |
| Epic | TSLA (Tesla) | 1.6% | $428.32 |
| Grail | SNDK (Sandisk) | 0.4% | $1,956.81 |

**EV $96.51 (96.5% of price)** · floor 54% · buyback-adjusted 86.9% · ~$10.25 gross margin/pack (10.2%) at a 70% instant-sellback rate. "Win a $1,957 stock from a $100 pack" is the headline.

**$10K allocation:** shelf inventory $7,557 (25 SLV + 25 BABA + 5 NVDA + 2 TSLA + 1 SNDK), buyback USDG float $1,800, buffer $500, gas + infra $150. Shelf covers ~260 packs of kept-share drain (buybacks recycle to inventory); SNDK hits ~1 in 250 packs with 1 escrowed — after a grail hit, restock from revenue before re-enabling the tier. Variance is spicy at this scale: a single grail sell-back (~$1,761 cash) nearly drains the float, so either accept one-grail-at-a-time exposure or swap the grail to a second TSLA for smoother variance at the cost of the headline. Verdict: **viable, but $10K is the true floor.**

## Status

Concept draft, July 2026. Prices refresh live; verify before relying on figures.

**Compliance note:** Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons. The prototype models compliant gating (geo-IP + KYC document-country cross-check) — helping restricted users evade via VPN would defeat the legal structure the product depends on. Securities/gaming counsel required before launch.
