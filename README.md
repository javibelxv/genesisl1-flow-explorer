# GenesisL1 Flow Explorer

A single-file, serverless dashboard for **L1 supply and flow on Osmosis** plus **outbound IBC from GenesisL1** — all client-side. No backend, no keys. Open the HTML, point it at any public RPC/LCD, and it reads the chains directly.

It answers, for a thin-liquidity asset:

- Net buy/sell pressure on L1 right now, and who's driving it.
- How much L1 is latent in wallets vs sitting in the pools.
- How concentrated the holder base is.
- What fresh supply is arriving (IBC) or about to unlock (unstaking).

---

## The overhang thesis

Most bridged L1 sits in wallets, not in the pools. That supply is latent — unpriced until someone sells it. The premise of this tool is that the overhang's size, its holder concentration, and the rate of incoming IBC / unlocking stake predict pressure better than the spot swap feed alone. So the dashboard shows three layers at once: what's flowing now, what's latent, and what's scheduled to unlock.

---

## The boxes

**Market stats** — L1 price, 24h change, volume, and market cap (price × on-chain total supply, read live from the GenesisL1 LCD).

**Net pressure (hero)** — L1 sold vs bought over a 1–168h window, with routing swaps excluded so pass-through trades don't inflate it. Shows the count of filtered routing txs.

**Metric cards** —
- Sell/buy ratio + unique sellers vs buyers.
- Share of all selling coming from one wallet.
- Traded price range in USD (p10–p90 of swaps ≥100 L1).

**🛢 Overhang** — total L1 on Osmosis minus pool reserves: the latent wallet-held supply, as an absolute, a % of all L1 on Osmosis, and a multiple of pool liquidity.

**Holder concentration** (in the overhang panel) — reads the full live holder set and shows:
- Top 1 / 3 / 5 / 10 / 20 share of total.
- Ranked table of the 15 largest holders (balance, USD, share).
- Configurable "wallets holding ≥ X L1" histogram.

**🎯 Top sellers** — sellers over the window, aggregated per wallet.

**⚠ Watchlist selling** — watchlisted wallets that are selling. The watchlist auto-adds any receiver of a large inbound IBC transfer, on the premise that freshly bridged supply is the likeliest to sell next.

**🟡 IBC out → Osmosis** — outbound L1 from GenesisL1, decoded from the packet. Leading signal of supply heading to market.

**🟢 IBC in ← Osmosis** — L1 bridging back to GenesisL1; the counterweight.

**📅 Unstaking calendar** — each unbonding as its own row (amount, delegator, USD, completion time, days left), sorted by completion, with ≥50k unlocks flagged.

**🔴 Sells / 🟢 Buys** — the raw, timestamped swap feed behind the net-pressure number.

**💧 Pool depth** — price impact of a 1,000 L1 sell per pool (falls back to window volume if reserves aren't reachable).

**🔀 Multi-hop audit** — collapsible list of every routing tx excluded from net pressure, so the headline is verifiable by hand.

Wallets and txs link to Mintscan (Osmosis) and the UTSA explorer (GenesisL1).

---

## Run it

One file. Open `index.html` from `file://`, or host it on any static host (GitHub Pages, Netlify, S3). No build, no deps.

Config lives in the ⚙ drawer, persisted to `localStorage`: endpoints, swap/IBC thresholds, timeframe, pool list, watchlist, and wallet labels.

---

## Data sources

- **GenesisL1 LCD / RPC** (UTSA) — supply, validators, unbonding, outbound IBC.
- **Osmosis RPC / LCD** — swaps, balances, reserves.
- **Osmosis SQS** — prices and pools.
- **Holder set** — keyless `denom-owners` endpoint from a community Osmosis full node (see credits).

Sections degrade gracefully and cache last-good data if an endpoint drops (e.g. the holder node rotating its snapshot ~monthly).

---

## Notes

- Split-route swaps are netted per tx, so a trade routed across several pools counts once, in the right direction.
- Everything is derived in-browser from public chain data; nothing is logged server-side.

---

## Credits

Built by **JLO (FirstBlock)**.

Special thanks to **ZEN (anodeofzen)** 🌙 — per-wallet holder data on Osmosis isn't exposed by any public API, and ZEN built and hosts the keyless endpoint that makes the holder-concentration section possible. Put together late at night as a favor to the ecosystem, and this tool wouldn't be complete without it.

RPC and explorer infrastructure by **UTSA** 🛰️.

---

## License

MIT © 2026 JLO (FirstBlock). See [LICENSE](./LICENSE).
