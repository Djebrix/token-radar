# token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/Djebrix/token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/Djebrix/token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Radar ranks every pre-token protocol by the implied value of its launch and shows how to farm each one.

## Run it

```sh
MAKEFUN_INPUT_DIR=<snapshot dir> MAKEFUN_OUTPUT_DIR=out node main.ts
```

---

Entered on [make.fun](https://make.fun/?ref=afvq43av&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards

## What it is

Token Radar reads a sealed DefiLlama snapshot (758 protocols, snapshot 2026-11-14) and scores
every protocol that records no token and is not a wrapped, staked or bridged product of a token
that already trades — the watchlist at its $50M floor, plus every rankable protocol below it.
That is 198 rows: 40 watchlist protocols and 158 early ones. Each row carries an implied value
in dollars and a six-step farm plan with costs and timeframes.

## The model (radar-2)

```
implied = round( tvl_usd x 0.20
  x (1 + clamp(change_7d, -50, 100) / 100 x 0.6)
  x (0.55 + min(age_days, 1095) / 1095 x 0.45)
  x reach x category )
```

- **reach**: 1.00 on 1 chain, 1.08 on 2–3, 1.15 on 4–7, 1.22 on 8+ chains.
- **category**: 1.15 for launch-led kinds (lending, exchanges, derivatives, yield, perps,
  prediction, RWA, restaking), 1.05 for the middle, 0.95 for kinds that rarely launch
  (stable assets, reserves, wrappers, lockers).
- **age**: days since the protocol was first listed; 365 when unlisted.

No protocol name is special-cased. Protocols whose token already trades are set aside in the
Live token view with their market caps shown, never mixed into the ranking.

Requires Node.js 22.18 or later (standard library only, nothing to install). The program reads
the snapshot, writes `board.json`, `data.js` and `dossier.json` to the output directory, and
copies `dashboard/` and `screenshots/` beside them. Two runs on the same snapshot write the
same bytes.

## Layout

- `main.ts` — the entrypoint: reads the snapshot, writes the board, the data and the dossier.
- `lib/model.ts` — the model, the board builder, the farm plans and the snapshot parser.
- `vendor/makefun-board.ts` — make.fun's dossier library, copied from the kit.
- `dashboard/` — the static page: `index.html`, `app.css`, `app.js`. No network, no framework.
- `screenshots/` — three screenshots of the dashboard: the board, a detail view, a filtered view.

Built with Djebrix's agent. Implied value is this radar's own claim, not a promise, and it
does not predict whether or when a token is issued.
