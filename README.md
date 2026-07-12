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

**$20K launch treasury** (covers a ~150-pack opening run with zero recycling assumptions; buybacks recycle ~70% of payouts back to the shelf, stretching effective coverage ~3×):

| Ticker | Qty | Cost | Covers |
|---|---|---|---|
| USAR | 50 | $927 | 161 packs |
| SLV | 45 | $2,428 | 155 packs |
| BABA | 40 | $3,858 | 148 packs |
| NVDA | 15 | $2,948 | 167 packs |
| TSLA | 5 | $2,142 | 167 packs |
| SNDK | 2 | $3,914 | 200 packs |
| **Shelf total** | | **$16,216** | |

Plus buyback USDG float **$3,000** and gas/infra ~$500 → **~$19,716 all-in.** The upgrade over the $10K version: 2 SNDK escrowed (the grail tier survives a hit without pausing), and a grail sell-back (~$1,761) now fits inside the float with room to spare. Margin is ~$9.29/pack at these odds, so a grail costs ~211 packs of margin to earn back — volume smooths it, and the fatter treasury is what lets you ride out the variance instead of flinching. The pack page also renders a "what's in the pack" series sheet: every 100-pack series holds exactly 31 USAR + 29 SLV + 27 BABA + 9 NVDA + 3 TSLA + 1 SNDK — the contents literally are the odds, committable onchain per series.

## Status

Concept draft, July 2026. Prices refresh live; verify before relying on figures.

**Compliance note:** Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons. The prototype models compliant gating (geo-IP + KYC document-country cross-check) — helping restricted users evade via VPN would defeat the legal structure the product depends on. Securities/gaming counsel required before launch.
