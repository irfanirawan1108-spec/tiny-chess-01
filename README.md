# tiny-chess-01 entry

A chess engine in 4 KB of Python, written from scratch for the make.fun
tiny-chess-01 bounty ("Build the strongest chess engine in 4 KB of
Python", $500).

## Files

- `chess.py` — readable source (12,145 bytes): 0x88 board, negamax
  alpha-beta, iterative deepening to a fixed node budget (100,000
  nodes/move, never a clock, so live trials and replays play the same
  moves), quiescence on captures (all evasions when in check, so mates
  score), Zobrist transposition table, MVV-LVA + killer ordering,
  null-move pruning, late-move reduction, delta pruning, incremental
  material + piece-square eval with a bishop-pair bonus. Resigns only
  on mate within 20 plies.
- `main.py` — the entrypoint (4,017 bytes), produced only by the kit:
  `node <kit>/cli/makefun.mjs compress chess.py --out main.py`.

## Local results (kit practice trials, official scorer)

Pre-compaction build: trial-0 Elo 1654, trial-1 1364, trial-2 1191,
mean **1403** (baseline mean 1365, floor 1154).

Packed build (this `main.py`): trial-0 **1364** (2W 3D 1L), trial-1
**1191** (1W 3D 2L), trial-2 **1279** (3W 0D 3L), mean **1249**.
The gap is small search-order drift from compaction cascading into
different game trajectories, plus more resigned losses (all vs the
node-budget houses; two were mate-in-6, the rest deep mates).

Smoke test of packed `main.py` vs a local env: 60 moves, all HTTP 200,
zero errors; think times mean 2.15 s, max 8.36 s (cold start under VM
contention), inside the 10 s limit. Perft-verified move generation
(start, Kiwipete and others match known counts exactly), re-checked
after every compaction pass. Start position: b1c3, score 20.
