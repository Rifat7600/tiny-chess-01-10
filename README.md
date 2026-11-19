# Tiny Chess 4K — Depth First (45k)

[![tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-10/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-10/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A chess engine in under 4 KB of Python for the
[make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01):
build the strongest chess engine that fits in 4,096 bytes.

## Idea

Fixed 45,000-node budget per move, no clock anywhere, so every trial
replays byte-identically. Depth first, but safely: reach depth 5–6
in the middlegame with late-move reductions, while keeping the
search honest — no razoring, futility pruning only at depth 1, and
a quiescence search that answers checks only at low ply (deep
check extensions eat the whole budget). Null-move pruning is in,
with mate-in-1 and perft tests guarding against tactical blindness.
The eval carries the endgame: passed-pawn bonuses and a
centralising king table once the queens are off, so passed pawns
get stopped instead of queening.

## Engine

- 0x88 board, alpha-beta + transposition table + killer moves +
  MVV-LVA ordering
- Iterative deepening over the fixed node budget
- Eval: material + piece-square tables + bishop-pair bonus
- Perft verified: startpos 20 / 400 / 8902, Kiwipete 48 / 2039 / 97862
- About 50,000 nodes/second on one CPU, so a 45,000-node move
  takes roughly a second — well inside the 10-second move limit

## How it plays

Each trial the engine plays six games against the house gauntlet
(depth-2 up to nodes-500k) through the env at `MAKEFUN_ENV_URL`,
one move at a time over HTTP. It reads no file but the brief, calls
no model, and exits when every game is over. The fixed node budget
is the whole determinism story: same positions, same nodes, same
moves, every replay.

## Files

- `main.py` — the whole engine (3,638 bytes)
- `source.py` — the readable source (not committed)
- `LICENSE` — MIT
