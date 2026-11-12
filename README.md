# tiny-chess-01: noiruxx engine

A 0x88-board alpha-beta chess engine in a single `main.py` (zlib-compressed
payload + unpacker, within the 4,096-byte cap), Python 3.13 standard
library only. It plays every game of the gauntlet through the env at
`MAKEFUN_ENV_URL`: `POST /game`, `POST /move`, until the env answers
`done`.

## How it plays

- Negamax alpha-beta with iterative deepening to a fixed node budget
  (150,000 nodes per move), never a clock, so a trial replay makes the
  same moves. Null-move pruning (R=2, zero window), late-move
  reductions (R=1 on quiet moves past the third), and check extensions
  (in-check nodes search at full depth).
- Quiescence search on captures and promotions; when in check it searches
  all legal evasions and scores checkmate, so it sees mates and avoids
  walking into them.
- Transposition table, MVV-LVA capture ordering, killer moves and a
  history heuristic; repetition inside the search scores a draw.
- Evaluation: material plus procedural piece-square terms (pawn advance
  and centrality, piece centralisation, rook on the 7th, bishop pair,
  king safety). Move generation is verified against known perft counts
  (start 20/400/8902/197281, Kiwipete 48/2039/97862).

## The size rule

- `main.py` is the whole engine: at most 4,096 bytes as committed.
  Compression is allowed and counted: this file unpacks and execs a
  compressed payload (`zlib` + `base64`); payload and unpacker both
  count toward the 4,096 bytes.
- The rest of the tree (this README, LICENSE) is at most 8,192 bytes
  in all, and no file over 4,096. MIT licence.

## Grade it locally

You need the `makefun` CLI and the competition's dev kit. Download the
kit, unpack it, and from inside it run:

    makefun grade-local --kit . --bot <your repository> --driver docker

It plays one practice trial against the house engines, replays it, and
prints the Elo the scorer gives it. Before you submit, `makefun check`
runs the site's own pre-submit checks, size caps included.
