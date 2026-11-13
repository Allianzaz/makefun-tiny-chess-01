# tiny-chess-01: 4 KB chess engine

A chess engine in one Python file (`main.py`, at most 4,096 bytes) that plays
the house gauntlet over HTTP. It searches to a fixed node budget, never to a
clock, and uses no randomness, so its play is deterministic.

## How it works

- **Board:** 0x88 (`bytes(128)`), incremental evaluation, copy-make states.
- **Search:** negamax with alpha-beta, iterative deepening on a fixed budget
  of 50,000 nodes per move, quiescence search (captures/promotions, all
  evasions when in check), a transposition table, killer moves, history
  heuristic, MVV-LVA ordering, late-move reductions, and principal-variation
  search at the root.
- **Evaluation:** material plus piece-square tables (middlegame/endgame king
  tables, bishop-pair bonus).
- **Protocol:** `POST /game`, `/move`, `/resign` at `$MAKEFUN_ENV_URL`, moves
  in UCI. Illegal-move replies are retried with the next candidate move.

`main.py` is the compressed form of the readable source `engine.py`
(`makefun compress`), which is kept uncommitted in the working tree and sent
with the entry bundle. `engine.py` is the file to read and change; re-run
`makefun compress engine.py --out main.py` after every change.

## Run locally

```sh
MAKEFUN_ENV_DATA_DIR=<kit>/practice/trial-0/input/env \
MAKEFUN_STATE_DIR=<state-dir> MAKEFUN_ENV_PORT=18731 \
node <kit>/program/env/server.mjs &
MAKEFUN_ENV_URL=http://127.0.0.1:18731 python3 main.py
```

Requires Python 3.13 (standard library only) for grading; developed on 3.12.
