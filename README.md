# Archer — Stock Packs on Robinhood Chain

Rip a pack, win **1 guaranteed full share** of a tokenized stock. Instant 90% buyback at oracle price. Archer is an independent concept app built on Robinhood Chain — not affiliated with or endorsed by Robinhood Markets.

## Contents

- **`SPEC.md`** — full technical spec: architecture (Next.js + Privy auth/onramps), smart contracts (PackSale, RipEngine, PrizeVault, BuybackDesk), provably-fair randomness (commit-reveal → VRF), treasury/custody, 90% buyback mechanics, EV model derived from 11 live card-gacha platforms, capital requirements, and the $10K single-pack pilot below.
- **`prototype.html`** — clickable prototype (open in a browser, mobile viewport). Brat-lime pop-art theme, Archer branding, onboarding with **KYC + region gating** (geo-IP and ID-country must both pass — Reg S restricted regions like the US/UK are blocked, and the UI says so honestly), one live $25 pack, coming-soon tiers, rip theater, 90% sell-back, portfolio, and **live token prices read from Robinhood Chain** (Blockscout API; production reads Chainlink AggregatorV3 feeds).

## The pilot — one $100 pack, 6 canonical tickers

Inventory is restricted to the **canonical token list at docs.robinhood.com/chain/contracts** (same-ticker tokens at other addresses are not Robinhood Stock Tokens). $100 minimum pack; USAR is the cheap "dud" tier that funds the win tiers. Live onchain token prices (2026-07-12):

| Tier | Ticker | Odds | Prize value |
|---|---|---|---|
| Base | USAR (USA Rare Earth) | 20.5% | $18.54 |
| Common | SLV (iShares Silver) | 20.0% | $53.95 |
| Uncommon | BABA (Alibaba) | 48.0% | $96.45 |
| Rare | NVDA (NVIDIA) | 9.0% | $196.53 |
| Epic | TSLA (Tesla) | 2.0% | $428.32 |
| Grail | SNDK (Sandisk) | 0.5% | $1,956.81 |

**EV $96.93 (96.9% of price)** · floor 18.5% · 11.5% chance to pull above price · buyback-adjusted 87.2% · ~$9.85 gross margin/pack (9.8%) at a 70% instant-sellback rate. Headline: "win a $1,957 stock from a $100 pack."

**Minimum inventory to launch** (covers a ~50-pack opening run; buybacks recycle to the shelf):

| Ticker | Qty | Cost |
|---|---|---|
| USAR | 25 | $464 |
| SLV | 20 | $1,079 |
| BABA | 30 | $2,894 |
| NVDA | 6 | $1,179 |
| TSLA | 2 | $857 |
| SNDK | 1 | $1,957 |
| **Shelf total** | | **$8,430** |

Plus buyback USDG float $1,200 and gas/infra ~$300 → **~$9,930 all-in, so $10K is exactly the floor.** BABA is the expensive line (48% mass tier) — expect to restock it from revenue weekly. A single SNDK sell-back (~$1,761) exceeds the float: pause the grail tier after a hit until revenue restocks. Comfortable number remains $12–15K.

## Status

Concept draft, July 2026. Prices refresh live; verify before relying on figures.

**Compliance note:** Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons. The prototype models compliant gating (geo-IP + KYC document-country cross-check) — helping restricted users evade via VPN would defeat the legal structure the product depends on. Securities/gaming counsel required before launch.
