# Archer — Stock Packs on Robinhood Chain

Rip a pack, win **1 guaranteed full share** of a tokenized stock. Instant 90% buyback at oracle price. Archer is an independent concept app built on Robinhood Chain — not affiliated with or endorsed by Robinhood Markets.

## Contents

- **`SPEC.md`** — full technical spec: architecture (Next.js + Privy auth/onramps), smart contracts (PackSale, RipEngine, PrizeVault, BuybackDesk), provably-fair randomness (commit-reveal → VRF), treasury/custody, 90% buyback mechanics, EV model derived from 11 live card-gacha platforms, capital requirements, and the $10K single-pack pilot below.
- **`prototype.html`** — clickable prototype (open in a browser, mobile viewport). Brat-lime pop-art theme, Archer branding, onboarding with **KYC + region gating** (geo-IP and ID-country must both pass — Reg S restricted regions like the US/UK are blocked, and the UI says so honestly), one live $25 pack, coming-soon tiers, rip theater, 90% sell-back, portfolio, and **live token prices read from Robinhood Chain** (Blockscout API; production reads Chainlink AggregatorV3 feeds).

## The pilot — one $100 pack, 6 canonical tickers

Inventory is restricted to the **canonical token list at docs.robinhood.com/chain/contracts** (same-ticker tokens at other addresses are not Robinhood Stock Tokens). $100 minimum pack; USAR is the cheap "dud" tier that funds the win tiers. Live onchain token prices (2026-07-12):

| Tier | Ticker | Odds | Prize value |
|---|---|---|---|
| Base | USAR (USA Rare Earth) | 31.0% | $18.54 |
| Common | SLV (iShares Silver) | 29.0% | $53.95 |
| Uncommon | BABA (Alibaba) | 27.0% | $96.45 |
| Rare | NVDA (NVIDIA) | 9.0% | $196.53 |
| Epic | TSLA (Tesla) | 3.0% | $428.32 |
| Grail | SNDK (Sandisk) | 1.0% | $1,956.81 |

**The Courtyard model:** odds strictly decrease from cheapest to rarest (market-standard shape), **EV $97.54 (97.5% of price)** displays proudly on the pack page, and the house still clears **~$9.29/pack** — the margin lives entirely in the buyback spread (70% of winners instantly sell back at 90%), not in the odds. 13% of pulls beat the pack price; grail is 1-in-100.

Why this works: with strictly decreasing odds and this price ladder, the $96→$197 gap between BABA and NVDA forces real weight onto TSLA/SNDK to reach 97% EV — one SNDK per ~100 packs is what funds the honest-looking (and honest) EV badge. Per 100 packs: $10,000 in, ~$6,145 buyback cash out, ~$2,926 in kept tokens, **~$929 margin**. The dial: money-mode (USAR 27/SLV 40/BABA 24/NVDA 7/TSLA 1.9/SNDK 0.1) shows 73.6% EV but clears $31.56/pack — 3.4× the margin at the cost of the EV badge.

**Minimum inventory to launch** (covers a ~50-pack opening run; buybacks recycle to the shelf):

| Ticker | Qty | Cost |
|---|---|---|
| USAR | 20 | $371 |
| SLV | 20 | $1,079 |
| BABA | 15 | $1,447 |
| NVDA | 6 | $1,179 |
| TSLA | 3 | $1,285 |
| SNDK | 1 | $1,957 |
| **Shelf total** | | **$7,318** |

Plus buyback USDG float $1,500 and gas/infra ~$300 → **~$9,120 all-in — inside $10K.** Watch-outs at this EV: margin is thin ($9.29/pack), so variance matters — a grail hits every ~100 packs and costs ~211 packs of margin to earn back (that's what 97.5% EV means; volume smooths it). A single SNDK sell-back (~$1,761) exceeds the float: pause the grail tier after a hit until revenue restocks it.

## Status

Concept draft, July 2026. Prices refresh live; verify before relying on figures.

**Compliance note:** Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons. The prototype models compliant gating (geo-IP + KYC document-country cross-check) — helping restricted users evade via VPN would defeat the legal structure the product depends on. Securities/gaming counsel required before launch.
