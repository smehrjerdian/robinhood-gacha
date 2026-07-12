# Archer — Stock Packs on Robinhood Chain
### Grant proposal · $25K ask · live prototype at [sayeed.work/robinhood-gacha](https://sayeed.work/robinhood-gacha)

Rip a pack, win **1 guaranteed full share** of a tokenized stock. Instant buyback at oracle price. Archer is an independent consumer app built on Robinhood Chain Stock Tokens — the first gacha/pack-ripping primitive for tokenized equities, bringing the mechanics that power the $5B+ card-ripping market to real-world assets.

**Why chains should want this:** every pack is a stock-token transaction, every buyback is a swap, every prize sits self-custodied in a user wallet. Archer converts entertainment demand into RWA volume, wallet activations, and sticky onchain balances — the exact consumer behavior Robinhood Chain, Arbitrum, and Uniswap are funding.

---

## What's already built

- **Clickable prototype** (this repo, live): pack store, odds disclosure, rip theater, portfolio, instant sell-back, KYC/region-gated onboarding (geo-IP + ID-country cross-check for Reg S compliance), and **live token prices read from Robinhood Chain** — canonical Stock Token contracts only, per [docs.robinhood.com/chain/contracts](https://docs.robinhood.com/chain/contracts).
- **Full technical spec** (`SPEC.md`): PackSale / RipEngine / PrizeVault / BuybackDesk contracts, commit-reveal provably-fair randomness (upgradeable to VRF), Safe-based treasury custody, Privy auth + onramps (Apple Pay, card, any-chain crypto deposits auto-bridged).
- **Market research**: EV/odds analysis of 11 live card-gacha platforms (RipCheck dataset) informing the pack economics below.

## The pack (live economics)

One $100 pack, six canonical Stock Tokens, odds strictly decreasing by rarity, priced off live Chainlink feeds:

| Tier | Ticker | Odds | Prize (snapshot) |
|---|---|---|---|
| Base | USAR | 31% | $18.54 |
| Common | SLV | 29% | $53.95 |
| Uncommon | BABA | 27% | $96.45 |
| Rare | NVDA | 9% | $196.53 |
| Epic | TSLA | 3% | $428.32 |
| Grail | SNDK | 1% | $1,956.81 |

**EV $97.54 (97.5% of price)** — displayed honestly in-app. House margin (~$9.29/pack) lives in the 90% buyback spread, not the odds. Every 100-pack series holds exactly these 100 shares; series composition is committed onchain before sale, so odds are provable, and the prize vault ("the quiver") is publicly verifiable.

## Use of funds — $25K

| Allocation | Amount |
|---|---|
| Prize inventory (50 USAR, 45 SLV, 40 BABA, 15 NVDA, 5 TSLA, 2 SNDK) | $16,216 |
| Buyback USDG float | $3,000 |
| Gas, infra, monitoring | $784 |
| Build-out: contracts + audit + provably-fair verifier page | $5,000 |
| **Total** | **$25,000** |

The inventory covers a **~150-pack opening run** with every tier fully stocked and the grail double-escrowed. Run economics: $15,000 GMV in → ~$9,218 buyback cash out → ~$4,389 restock of winner-kept shares → **~$1,393 expected operating profit per cycle (9.3% of GMV)**. Treasury runs as a waterfall: rebuild float → restock shelf to par → variance reserve → only cash above par is profit. At steady state the treasury is self-sustaining; the grant seeds the flywheel once.

## $ARCHER token — the complementary revenue engine

Fair-launched on Robinhood Chain via **Bankr**, with creator swap-fee share routed to the Archer treasury. Early comparable launches generate **$10–15K in swap fees** — enough to fully fund shelf replenishment during the opening months, making token volume and gacha liquidity mutually reinforcing.

**Holder utility (drives demand):**

- **Staking tiers → better buyback.** Stake $ARCHER to raise your instant sell-back rate: 90% base → **92% Bronze / 94% Silver / 96% Gold**. The single number every ripper cares about becomes a reason to hold.
- **Burn-to-re-roll.** Don't like your pull? Burn $ARCHER for one re-roll at the same odds. A pure token sink with real emotional utility at the peak-excitement moment.
- **Staker-gated packs.** Limited series (rarer grails, collab packs) rippable only by staked wallets.
- **Fee-free swap days & early access** for top-tier stakers.

**Deflationary loop (drives attention):** 50% of all swap-fee revenue market-buys $ARCHER and burns it; burn-to-re-roll compounds the sink; a live burn counter renders in-app and every burn is a social object ("🔥 12.4M $ARCHER burned"). The other 50% of fees restocks the prize vault — visible on the quiver page, so token volume literally refills the grails.

## Uniswap v4 hooks (why this fits a Uniswap grant)

Archer's token + treasury design is a natural v4 hooks showcase:

1. **Flywheel hook (`afterSwap`)** on the ARCHER/USDG pool: skims the fee split onchain — 50% buy-and-burn, 50% streamed to the PrizeVault. The revenue loop becomes trustless and publicly auditable instead of a manual payout.
2. **Loyalty dynamic-fee hook (`beforeSwap`)**: staked wallets get discounted LP fees on the ARCHER pool — staking utility enforced at the AMM layer, a genuinely novel consumer use of dynamic fees.
3. **TWAMM rebalancing hook**: the BuybackDesk re-sells bought-back Stock Tokens through time-weighted orders, minimizing impact in thin RWA pools — a public-good pattern for every RWA app on v4.
4. **Onchain revenue oracle**: hook-level fee accounting gives grantors and users a live, verifiable dashboard of protocol revenue.

Pools deploy on Robinhood Chain (permissionless, Uniswap-compatible) with an Arbitrum One mirror for depth.

## Milestones

| # | Deliverable | Timeline |
|---|---|---|
| 1 | Contracts on testnet + provably-fair verifier | Weeks 1–3 |
| 2 | Mainnet launch, $100 pack live, KYC/geofence enforced | Weeks 4–6 |
| 3 | $ARCHER launch via Bankr + staking tiers + burn-to-re-roll | Weeks 6–8 |
| 4 | v4 flywheel + dynamic-fee hooks live | Weeks 8–12 |

**KPIs:** packs ripped, GMV, unique KYC'd wallets, Stock Token transfer volume, buyback swap volume, $ARCHER burned, fee revenue routed to vault.

## Compliance

Robinhood Stock Tokens are Reg S instruments not offerable to US, UK, Canadian, or Swiss persons; Archer enforces geo-IP + KYC document-country cross-checks (VPNs fail the document check) and the app states restrictions plainly. Chance-based mechanics, buyback structuring, and token utility will be reviewed by securities/gaming counsel before mainnet — budgeted within build-out. Archer is not affiliated with or endorsed by Robinhood Markets.

---
*Contact: Sayeed Mehrjerdian · sayeed@metaseedlabs.com · [sayeed.work](https://sayeed.work)*
