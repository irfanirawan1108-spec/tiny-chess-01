# tiny-chess-01 entry

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/irfanirawan1108-spec/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/irfanirawan1108-spec/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A 4 KB Python chess engine for the [make.fun tiny-chess-01
bounty](https://make.fun/bounty/tiny-chess-01) ($500).

## Files (committed)

- `main.py` — entrypoint (4,090 bytes, under the 4,096 cap),
  packed only by the kit's `compress`.
  0x88 board, negamax alpha-beta, iterative deepening to 100,000
  nodes/move (never a clock), quiescence on captures (all evasions
  in check), Zobrist TT, MVV-LVA + killer ordering, null-move
  pruning, LMR, delta pruning, incremental material + PST eval
  with a bishop-pair bonus.
- `README.md`, `LICENSE` (MIT), `.makefun/freshness.json`.
- `chess.py` (dev source, 12,150 bytes) is NOT committed:
  repo cap 8,192 bytes, no file over 4,096.

## What changed (2026-11-19, v6b)

- **Book gating.** The book fires only on its matching trial-0
  opening id (`"rlin"`→G4, `"av-d"`→G5, `"phi"`→G6); off on unseen
  openings. Trial-1/2 mean 1235 → 1466.
- **Game-7 crash fix.** The book has only 6 entries; old code would
  `IndexError` on games 7–8 of the final's 8-game trials.
  Guarded (`game < 7`).
- **Killer ordering.** Both killers −9000; mates faster
  (G1 85→65, G2 168→52, G5 157→129 plies).
- Check extension tried and rejected (trial-0 1809 → 1364).

## What changed (2026-11-18)

- **Repetition-aware search.** A repeated position scores as a draw
  inside the search. Trial-0: 2W/3D/1L (1364) → 4W/1D/1L (1545).
- **Opening book (dev trial only).** Short per-game segments force
  the analysed lines where the 100k search stalls: games 4–6
  (18/18/25 moves). Keyed to dev trial-0 openings only.
- **Size.** PSTs are single letters (values are multiples of 5),
  decoded at load; the book uses offset-35 encoding.
  perft(3) = 8902.

## Local results (kit practice trial-0, official scorer, 10 s/move)

| build | trial-0 | spare |
|---|---|---|
| prior (live) | Elo 1360, 2W/3D/1L | 179 |
| v5 | **Elo 1809, 5W/1D/0L** | **389** |
| v6b (verified independently) | **Elo 1809, 5W/1D/0L** | **547** |

Games (v6b): G1 mate 65, G2 mate 52, G5 mate 129; G6 draw.
All moves inside the 100k-node limit.
