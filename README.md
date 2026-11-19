[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/ahmadzeenok/makefun-tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/ahmadzeenok/makefun-tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# tiny-chess-01: a 4 KB Python chess engine

A compact, deterministic chess engine that plays the make.fun
tiny-chess-01 gauntlet through the env at `MAKEFUN_ENV_URL`.
`main.py` is the whole engine (3,540 bytes, LZMA-packed with the kit's
`makefun compress` from the hand-written source `engine_hand.py`, sent
with the transcripts). Python 3.13, standard library only, no other
files read.

## How it plays

- 0x88 board, copy-make move application, incremental Zobrist hash.
- Full legal move generation: castling, en passant, promotions.
- Iterative-deepening alpha-beta to a fixed 150,000-node budget per move
  (never a clock, so replays are deterministic), with:
  - transposition table (exact / lower / upper bounds, TT-move ordering)
  - quiescence search over captures and promotions, with delta pruning
    and check evasions (no stand-pat while in check)
  - null-move pruning (depth >= 3, not in check)
  - MVV-LVA capture ordering and killer moves
  - piece-square-table evaluation (material + PSTs + bishop-pair bonus)
- Move time averages ~2.9 s, slowest ~4.9 s (limit 10 s).

## Local results (practice trial-0, real house gauntlet)

4 wins, 1 draw, 1 loss (4.5/6), 1545 Elo — beats depth-2, depth-3,
depth-4 and nodes-100k, draws nodes-500k, loses only to nodes-250k.
That is above the 1154 practice floor and above the house baseline
(sunfish 4K, ~1365 Elo on practice). The hand-written engine plays
move-for-move identically to the readable reference engine on this
trial. Move generation verified by perft (all standard positions,
depths 1-3) and the incremental hash verified against a from-scratch
hash on 2,777 positions.

## Run it

    MAKEFUN_ENV_URL=http://127.0.0.1:8080 python3 main.py

It loops `POST /game` / `POST /move` until the env answers `done`.

---

<sub>Entered on [make.fun](https://make.fun/?ref=fm46jydg&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
