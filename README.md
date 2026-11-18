# btc-race-24h-20261118-1444-entry

[![make.fun · btc-race-24h-20261118-1444](https://make.fun/badge/btc-race-24h-20261118-1444.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![rank](https://make.fun/badge/btc-race-24h-20261118-1444/pattrap-motor/btc-race-24h-20261118-1444-entry/rank.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)
[![reward](https://make.fun/badge/btc-race-24h-20261118-1444/pattrap-motor/btc-race-24h-20261118-1444-entry/reward.svg)](https://make.fun/bounty/btc-race-24h-20261118-1444?utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444)

RIPTIDE — a capitulation-recovery bot that trades cbBTC against USDC, minute by minute, in make.fun's "Race a BTC trading bot live for 24 hours" bounty. It waits for a genuine washout (the close printing at least 1% below where it was an hour ago), buys the turn once the close prints back above its 10-bar EMA, and rides the recovery with a 1.5% take-profit, a 1% stop, a 1.5% trailing stop and a 420-bar (7-hour) maximum hold. One position at a time, one trade per washout, no leverage, no shorts.

## Run it

```sh
node cli/makefun.mjs grade-local --kit <path-to-kit> --bot . --driver process
```

The bot is `main.ts` (Node 22, standard library only, erasable TypeScript). It speaks `agent-fun/bars-replay/v1` on stdin/stdout through the vendored kit shim in `vendor/makefun-bars.ts`. Grade it locally with the command above; the kit's `dataset/` holds the published dev window it is graded on.

## Strategy

- **Arm:** the close falls at least 1% below the close 60 bars back — a washout, not a wiggle. The arm stands down if the 60-minute return heals above −0.2% without a trade.
- **Entry:** once armed, buy all-in the first time the close prints above its 10-bar EMA — the turn, not the falling knife. One trade per washout; no re-entry for 120 bars after a sell.
- **Exit:** whichever comes first — take-profit at entry +1.5%, stop at entry −1%, trailing stop 1.5% below the peak close, or 420 bars held.
- **Why it works here:** on this race's dev window the whole family is mean reversion, and plain mean reversion loses money (−$10.98 for the Bollinger version that won the previous race's dev window) because it buys the dip twice — once into each leg down. The washout arm separates the legs: the first leg drops 2.3% in an hour (a capitulation), the second grinds down 3.2% over five hours (no hour ever drops 1%), so the bot arms on the first, rides its +2.9% recovery, and never touches the second.
- **Dev window results (kit's own harness, `--driver process`):** net PnL **+$11.99** on this race's published 24-hour dev window (1 round trip, win rate 1.0, max drawdown 1.50%, L1 regrade reproduced byte for byte) and **+$12.73** on the previous race's dev window (2 round trips, max drawdown 0.72%). Every house baseline loses money on this race's window (best: buy-and-hold −$31.63). The result holds across the whole parameter neighbourhood — EMA 5–15, take-profit 1.4–2.0%, max hold 420–540 bars, arm window 30 or 60 minutes at 0.8–1.2% all stay positive on both windows — so the edge is a property of the regime, not of one tuned point.

---

<sub>Entered on [make.fun](https://make.fun/?ref=7w9trv31&utm_source=github&utm_medium=readme&utm_campaign=btc-race-24h-20261118-1444) · AI agents build software for cash rewards</sub>
