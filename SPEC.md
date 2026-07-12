# Archer — Technical Spec v0.2
### A stock-token gacha on Robinhood Chain: every pack guarantees 1 full share
*(Brand: "Archer" — thematically adjacent to Robinhood without using its marks. Original bow-and-arrow logotype; "the quiver" = the onchain prize vault. See README for the $10K single-pack pilot model.)*

**Author:** Sayeed Mehrjerdian · **Date:** July 2026 · **Status:** Draft for review

---

## 1. Concept

Buy a pack. Rip it. Get **one guaranteed full share** of a real stock (as a Robinhood Chain Stock Token) — usually a common stock worth less than the pack price, occasionally a mid-cap winner, rarely a grail like TSLA or NVDA. Instant buyback to cash, or keep the token in your wallet.

Why this works as a product:
- **Zero appraisal ambiguity.** Unlike cards, every prize has an exact, onchain, Chainlink-published price. "You won $312 of AMZN" is indisputable.
- **The prize is productive.** Winners hold a real financial asset that can appreciate, pay (reinvested) dividends via the token multiplier, and be used across the Robinhood Chain ecosystem.
- **Guaranteed full share** is a cleaner promise than card gachas' "value ranges" — the unit is emotionally legible.

---

## 2. ⚠️ Compliance reality check (read first)

Not legal advice — engage securities + gaming counsel before writing a line of production code. The four hard constraints:

1. **Robinhood Stock Tokens are Reg S instruments — not offerable to US persons.** Per RHJ's prospectus terms, Stock Tokens "may not be offered, sold, or delivered… in the United States or to US persons," with additional restrictions in Canada, UK, and Switzerland. **V1 must be geo-fenced non-US** with KYC + geo-IP + wallet screening. This is the single biggest constraint on the business.
2. **Chance-based distribution of securities** may be regulated as gambling, a lottery, and/or a securities offering depending on jurisdiction. Mitigations used by comparable products: fixed floor value (every pack pays ≥ X), full odds disclosure (we do this anyway), free-entry alternative (sweepstakes structure), and licensing in a permissive jurisdiction (e.g. Isle of Man, Curaçao for v1).
3. **The gacha operator never takes custody of user funds as an exchange/broker.** Structure as: user buys a *pack NFT* (a digital good); the pack redeems for a stock token prize from our treasury. Buyback = we bid on the user's token at oracle price; it's a secondary-market trade, not brokerage.
4. **Robinhood branding**: the prototype uses Robinhood Chain brand elements for internal demo purposes only. A launch requires either building as an independent third-party app on the chain (permissionless, but no RH trademark use) or a formal partnership (there's an "Interested in Building" form — worth pursuing given the pitch: *first native consumer gacha on Robinhood Chain*).

---

## 3. Architecture overview

```
┌───────────── Client (Next.js PWA) ─────────────┐
│ Pack store · Rip theater · Portfolio · Buyback │
│ Privy SDK (auth + embedded wallet + funding)   │
└───────┬────────────────────────────┬───────────┘
        │ REST/WS                    │ viem/wagmi
┌───────▼───────────┐      ┌─────────▼──────────────────────┐
│ Backend (Node/TS)  │      │ Robinhood Chain (Arbitrum Orbit)│
│ · pack catalog     │      │ · PackSale.sol                  │
│ · odds engine      │      │ · RipEngine.sol (commit-reveal) │
│ · commit-reveal    │      │ · PrizeVault.sol (per-band)     │
│   seed service     │      │ · BuybackDesk.sol               │
│ · treasury bot     │      │ · Chainlink stock feeds (read)  │
│ · risk/rebalancer  │      │ · Stock Tokens (ERC-20, 18 dec) │
│ · KYC/geo (vendor) │      │ · USDG (settlement stable)      │
└───────┬───────────┘      └─────────▲──────────────────────┘
        │                            │
┌───────▼────────────────────────────┴───────┐
│ Treasury ops: Safe multisig (cold inventory)│
│ + hot fulfillment wallet (capped)           │
└─────────────────────────────────────────────┘
```

**Stack choices**
- **Web app:** Next.js + Tailwind, PWA-installable (learn from Mnstr: test wallet flows on mobile relentlessly — their PWA broke here and it's the #1 UX complaint).
- **Auth:** Privy — email / social / passkey login provisioning an embedded EOA per user; external wallet linking optional (support WalletConnect properly — Monster's Rainbow issues are a cautionary tale).
- **Funding:** Privy's funding rails: Apple Pay + card via **Coinbase Onramp** (primary, best US→ non-US conversion; for our non-US audience route by region) and **Stripe** fallback; plus **direct crypto deposit with multi-chain support** (Last Pack's onboarding is the benchmark: accept USDC/ETH on Base, Arbitrum, Solana, mainnet → auto-bridge to Robinhood Chain USDG via the canonical bridge + LiFi-style router). Show the destination chain LOUDLY — Monster's MegaETH-only deposit trap (which cost me a private-key export to recover Base funds) is exactly what we will not ship.
- **Chain:** all prize/settlement logic on Robinhood Chain. Gas is cheap (Orbit L2); users never need gas — sponsor via paymaster (chain supports AA per docs).

---

## 4. Core contracts

### 4.1 `PackSale.sol`
- `buyPack(tierId, qty, clientSeed)` — payable in USDG; mints a **PackNFT** (ERC-721) with `tierId`, `clientSeed`, `serverSeedHash` (pre-committed, see §6), `blockNumberTarget`.
- Pack catalog (tier, price, odds table version) stored onchain; odds table hash anchors what the UI displayed at purchase time → **provable odds snapshot per pack**.
- Revenue splits: X% → BuybackDesk float, Y% → Treasury inventory budget, Z% → ops wallet.

### 4.2 `RipEngine.sol`
- `rip(packId, serverSeed)` — called by our reveal service (or user-triggered with the revealed seed): verifies `keccak256(serverSeed) == serverSeedHash`, computes `rand = keccak256(serverSeed, clientSeed, blockhash(target))`, maps to a **value band** per the pack's odds table, then to a concrete stock token whose Chainlink price sits in that band (see §5.3).
- Emits `Ripped(packId, ticker, tokenAmount, usdValueAtRip)`; transfers exactly `1e18 * (1e18 / uiMultiplier)`-adjusted amount so the user receives **1.00 underlying share-equivalent** (use `uiMultiplier()` per ERC-8056 so "1 full share" stays true after splits/dividends).
- Prize pulled from `PrizeVault`; if the band's vault is empty → fallback to next band UP (never down; user can only be upgraded) + alert.

### 4.3 `PrizeVault.sol` (one logical vault, per-band accounting)
- Holds stock-token inventory bucketed by price band; only `RipEngine` can withdraw; restocked by treasury bot from the Safe.
- Reads Chainlink feeds (with staleness + sequencer-uptime + `oraclePaused()` checks per RH docs) to re-bucket tokens whose prices drift across band boundaries — a daily keeper job.

### 4.4 `BuybackDesk.sol`
- `sellBack(token, amount)` — quotes `oraclePrice × amount × buybackRate` (default **90.0%**), pays USDG instantly, token returns to PrizeVault inventory.
- Circuit breakers: max per-tx, max per-day per-user, pause on stale oracle or sequencer-down (both per RH best-practice docs), and a dynamic rate floor if treasury float < threshold.
- 90% sits squarely in the card-gacha cluster (85–93% at Collector Crypt/Mnstr/Pull.fun) but costs us far less than it costs them: they eat comp-value uncertainty when re-selling cards, while we re-sell at exact oracle price — the 10% spread is nearly pure margin. It's also a tunable lever: raising toward 95–99% later is a headline growth move we hold in reserve.

---

## 5. Odds & EV model

### 5.1 What the Pokemon gacha data says
From the 11 platforms we analyzed (RipCheck dataset, July 2026): platform-level **headline EV medians ≈ 105.6% of pack price** (range 96.2%–110.7%), and disclosed buyback rates cluster at 85–100%. The market-wide *realizable* edge (buyback-adjusted) averages **−9.3%**.

### 5.2 Why we can't literally copy 105% — and what we do instead
Card platforms print >100% EV because card "FMV" is an optimistic comp and they acquire inventory below it. **Stocks have exact prices — a 105% cash EV means we lose 5% on every pack, guaranteed.** So we split the median into two honest components:

- **Cash EV: 96.5%** of pack price (matches the honest end of the market — Courtyard runs 97% and is the volume leader).
- **Rewards EV: ~+8.5%** in points (redeemable toward free packs, like Collector Crypt/Phygitals/Beezie) → **effective headline EV ≈ 105%, matching the market median** while gross margin stays positive.
- **Realizable EV after 90% buyback: ~86.9%** for instant sellers (−13.1% edge, vs the −9.3% market average) — holders who keep their share retain the full 96.5%. This positioning trades consumer edge for margin; the buyback rate is a lever we can raise later as a growth move.

House economics per $100 pack: $3.50 gross EV margin + ~$6.75 avg buyback spread (70% of pulls sold back × 10% of the 96.5% payout) + breakage + float ≈ **10–12% blended gross margin**, before points liability (~1.5–2% effective cost at realistic redemption rates). That margin materially self-funds ops and de-risks the treasury.

### 5.3 Tier tables (value bands, then stocks)
Odds map to **$ value bands**, and the engine picks uniformly among in-band inventory (weighted by overstock to self-balance). Bands per pack, tuned to cash EV = 96.5% (structure follows the market-standard 75/20/4/1 shape we saw on Collector Crypt/Slabz/Phygitals):

| $25 pack | Band | Odds | | $100 pack | Band | Odds |
|---|---|---|---|---|---|---|
| Common | $12–$25 | 75% | | Common | $55–$100 | 75% |
| Uncommon | $25–$50 | 20% | | Uncommon | $100–$200 | 20% |
| Rare | $50–$150 | 4% | | Rare | $200–$450 | 4% |
| Grail | $150–$600 | 1% | | Grail | $450–$1,500 | 1% |

*(Exact band edges are solved per tier so Σ p·E[band] = 0.965 × price, using live inventory prices — a solver recalcs bands daily; the odds % stay fixed, bands flex.)*

Illustrative inventory mapping from the current [token contract list](https://docs.robinhood.com/chain/contracts) (verify live prices via Chainlink feeds — these shift daily): low bands drawing from the cheaper end of the roster (e.g. INTC, BE, USAR, SNDK), mid bands from BABA, MU, CRCL, CRWV, PLTR, SLV, AMD, high bands from AAPL, AMZN, GOOGL, COIN, QQQ, and grail bands from MSFT, META, NVDA, TSLA, SPY, SPCX. The engine only ever selects tickers whose **live feed price** falls inside the band, so drift never breaks the promise.

**Launch pack lineup:** $10 / $25 / $50 / $100 / $250 (median platform lineup; add $500+ once treasury depth allows).

---

## 6. Randomness (provably fair, upgradeable)

**V1 — commit-reveal (provably fair, casino-standard):**
1. Seed service pre-generates batches of `serverSeed`s; publishes `keccak256(serverSeed)` onchain **before** any pack using it is sold.
2. User's `clientSeed` (auto-random, user-editable for the paranoid) is fixed at purchase.
3. Rip: `rand = keccak256(serverSeed ‖ clientSeed ‖ blockhash(purchaseBlock+N))`. Server can't grind (hash pre-committed), user can't predict (server seed secret), miner influence negligible on an L2 with centralized sequencer — which is exactly why we add:
4. Full verification page: anyone can recompute any historical rip. Every pack links to its seeds + math.

**V2 — onchain VRF:** Chainlink is already live on Robinhood Chain for price feeds; when VRF (or an alternative like Pyth Entropy) deploys there, swap the entropy source in `RipEngine` behind an interface. Design for it now: `IEntropySource.getRandomness(packId)`.

**Anti-abuse:** rate limits per wallet, sybil screening via Privy fingerprinting, no rip-result dependence on anything user-timeable.

---

## 7. Treasury & custody

```
[Safe multisig — COLD]  →  [PrizeVault contract — WARM]  →  winners
   inventory master           2–3 days of expected            
   3/5 signers, timelock      payouts, auto-restocked         
                                     ↑
[BuybackDesk — USDG float] ← pack revenue + bought-back tokens re-shelved
```

- **Cold:** Safe multisig (3/5: founders + counsel + independent) holds the bulk of stock-token inventory + USDG reserves. Timelocked withdrawals except to PrizeVault.
- **Warm:** PrizeVault holds ~2–3 days of expected payout value. Hot restock bot has a daily cap; breach = pause + page.
- **Acquisition:** we can't mint from RHJ (Authorised Participants only — at issuance just BBVI). Inventory is acquired on **secondary onchain markets** (Robinhood Chain DEX liquidity) via a TWAP bot, or via an AP/OTC relationship as volume scales (worth pursuing BBVI directly for primary-market access at size).
- **Rebalancing:** daily keeper re-buckets inventory by live price band, tops up thin bands, and sells overweight positions back into the market. Net market exposure of the treasury is hedged optionally (short index futures / inverse ETF sleeve) — at small scale, just accept beta; at $500K+ inventory, hedge.
- **Winner fulfillment:** direct ERC-20 transfer from PrizeVault at rip time — instant, no shipping. (Our "shipping" equivalent — bridging tokens off-chain to a Robinhood app account — is native to the chain's bridge; balance can pay for nothing extra because there's nothing to pay. Courtyard's balance-can't-pay-shipping complaint literally cannot exist here.)

---

## 8. Buybacks

- Instant, in-app, at **90% of Chainlink oracle price**, paid in USDG to the user's embedded wallet (withdrawable via Privy offramp or bridge).
- Bought-back tokens are re-shelved into PrizeVault at oracle price → inventory recycles at a 10% discount to market; the spread is the desk's primary profit center.
- Stale-price protection: quote requires feed freshness (heartbeat check), sequencer up, `oraclePaused() == false`; else buyback pauses for that ticker (market-hours reality: feeds update 24/5 — display "market closed, buyback locked at last price ±0%" or pause; recommend pause-outside-24/5 window in v1).
- Take-rate assumption for modeling: **70% of pulls sell back instantly** (matches card-gacha behavior); 30% hold in wallet.

---

## 9. Capital requirements

Parametric model (all figures scale linearly with volume). Assumptions: avg pack $30, cash EV 96.5%, 70% buyback take-rate, buyback at 90%, inventory acquisition on secondary at ~market price.

| | **Lean pilot** | **Recommended launch** | **Growth** |
|---|---|---|---|
| Packs/day | 50 | 150 | 500 |
| Daily GMV | $1,500 | $4,500 | $15,000 |
| Daily prize outflow (96.5%) | $1,448 | $4,343 | $14,475 |
| **1. Shelf inventory** (full band coverage ×5 tiers, ~10 days net burn) | $18K | $45K | $130K |
| **2. Grail escrow** (2× top prize per tier, held always) | $8K | $15K | $40K |
| **3. Buyback USDG float** (7 days × take-rate, recycles) | $7K | $21K | $70K |
| **4. Volatility + oracle-pause buffer** (15%) | $5K | $12K | $36K |
| **5. Ops/legal/audit runway (6 mo)** | $30K | $60K | $120K |
| **Total to be genuinely usable** | **~$70K** | **~$155K** | **~$400K** |

Notes: (1)+(3) recycle continuously — they're working capital, not burn; the true "cost" is exposure to inventory beta + the 3.5% gross margin funding ops. **$150K is the realistic number** to launch something that doesn't feel empty: every band stocked, grails visibly real (verifiable onchain — a marketing feature card gachas can't match: *"don't trust our top prize exists? Read the vault."*), and buybacks that never wobble.

---

## 10. MVP cut & sequence

1. **Weeks 1–3:** contracts (PackSale, RipEngine, PrizeVault, BuybackDesk) on testnet (faucet live per RH docs) + commit-reveal service + odds solver.
2. **Weeks 3–5:** webapp — Privy auth/funding, pack store, rip theater, portfolio, buyback. PWA + mobile wallet-connect testing as a first-class workstream.
3. **Weeks 5–6:** treasury ops (Safe, bots, keepers), provably-fair verification page, audits (contracts are small — one boutique audit ≈ $15–25K, in ops budget).
4. **Gate for mainnet:** legal opinion on jurisdiction + geo-fencing live + RH partnership conversation initiated.

**Open questions:** DEX liquidity depth on Robinhood Chain today (sizes our TWAP acquisition); VRF availability timeline; whether RHJ/BBVI will onboard us as an institutional buyer; points-program liability treatment.

---
*Companion artifact: `prototype.html` — clickable product prototype modeled on the best patterns from the 11 platforms tested (Mnstr's odds panel, Courtyard's polish, Last Pack's onboarding, Tilt Rips' EV badge), skinned in Robinhood Chain branding (internal demo only).*
