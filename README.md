# tiny-chess-01 entry

`main.py` is the competition entry. `engine_source.py` is the readable source it was derived from.

## Engine design

- Board representation: a 128-cell 0x88 array of single characters; off-board squares are detected with `&136`.
- Search: negamax with alpha-beta pruning, iterative deepening, transposition table, and quiescence search over captures (all evasions when in check).
- Move ordering: transposition-table move first, then two killer moves, history heuristic, and MVV-LVA for captures/promotions.
- Evaluation: material plus compressed piece-square tables, a tapered king table, and a bishop-pair bonus.
- Determinism: each move searches to a fixed node budget (`BUDGET=300000`); there is no clock use and no randomness.

## Game protocol

The engine POSTs JSON to `$MAKEFUN_ENV_URL`: `{}` to `/game` to learn the current game, then `{"game": n, "move": "<uci>"}` to `/move` while `status` is `your_move`. It stops when `/game` reports `status: done`. Each game answer supplies `fen`, `start_fen` and `moves`, which are used to rebuild the game history for repetition avoidance, plus `legal_moves`, which is the authoritative move list. The chosen move is checked against `legal_moves`; illegal or failing choices fall back to its first entry. Moves must arrive within the 10-second per-move limit.

## Session history

This engine was built iteratively during this session for the tiny-chess-01 competition: starting from a readable baseline, adding repetition avoidance, castling generation, tapered king evaluation and a bishop-pair term, then validating repeatedly against the practice gauntlet before golfing the final entry.
