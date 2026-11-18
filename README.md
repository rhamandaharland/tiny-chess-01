# tiny-chess-01 — a chess engine in under 4 KB of Python

A deterministic alpha-beta chess engine written from scratch for the
make.fun **tiny-chess-01** competition ("Build the strongest chess engine
in 4 KB of Python"). The whole engine is `main.py` (≤ 4,096 bytes,
Python 3.13 standard library only).

## How it plays

- **Interface** — the program talks HTTP to the environment in
  `MAKEFUN_ENV_URL` (protocol agent/v1): `POST /game`, `POST /move`,
  `POST /resign`. It always picks its move from the environment's
  `legal_moves`.
- **Board** — 0x88 board, full legal move generation (castling including
  transit-square checks, en passant including pins, all promotions).
- **Search** — negamax with alpha-beta pruning, iterative deepening and a
  quiescence search (captures + promotions). Move ordering: transposition
  table move first, then MVV-LVA. A transposition table with
  mate-ply-adjusted bounds speeds up the search.
- **Deterministic by design** — a fixed node cap per move (never the
  clock, never randomness), so replayed trials are byte-for-byte
  identical.
- **Evaluation** — material + piece-square tables for all six piece
  types, plus pawn-structure terms (doubled and isolated pawn
  penalties) and passed-pawn bonuses that grow with advancement.

## How main.py is built

`main.py` is the compressed form of a readable source file
(`engine_src.py`, developed and tested outside the submission repo —
the repo itself must stay ≤ 8,192 bytes total):

    python3 build_engine.py   # zlib + base85 payload wrapped in a small
                              # exec unpacker (compression is explicitly
                              # allowed; payload + unpacker count toward
                              # the 4,096-byte limit)

Correctness is guarded by tests kept with the sources: perft
(start position 20/400/8902, kiwipete 48/2039/97862), tactical puzzles
(mate-in-1, hanging queen), and a mock-environment end-to-end run of the
agent/v1 protocol with zero illegal moves.

## License

MIT — see `LICENSE`.
