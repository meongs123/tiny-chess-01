# tiny-chess-01: noiruxx engine — v28

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/meongs123/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/meongs123/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A 0x88-board alpha-beta chess engine in a single `main.py` (lzma-compressed
payload + unpacker, within the 4,096-byte cap), Python 3.13 standard
library only. It plays every game of the gauntlet through the env at
`MAKEFUN_ENV_URL`: `POST /game`, `POST /move`, until the env answers
`done`.

## How it plays

- Negamax alpha-beta with iterative deepening to a fixed node budget
  (150,000 nodes per move), never a clock, so a trial replay makes the
  same moves. PVS at the root with aspiration windows.
- Transposition table and history persist across moves
  (cleared only past 200,000 entries); repetition inside the search
  scores a draw. Killers, MVV-LVA capture ordering.
- Adaptive extension: positions with 3 or fewer legal moves search
  one ply deeper — narrow forcing lines and endgames.
- Quiescence on captures and promotions; in check it searches
  all evasions and scores mate.
- Evaluation: material plus piece-square terms, bishop pair, and
  king safety. Move generation is verified against known perft counts
  (start 20/400/8902/197281, Kiwipete 48/2039/97862).

## Trial-0 result

Elo **1464** (7W-4D-5L, spare_plies 723) vs the 8 house
engines — 104 above the sunfish-4K baseline mean of 1360. Think
mean 3.04s, max 5.72s. Determinism verified by a 16-game replay proxy.

## The size rule

- `main.py` is the whole engine: at most 4,096 bytes as committed
  (here 4,023). Compression is allowed and counted: this file unpacks
  and execs a compressed payload (`lzma` + `base85`); payload and
  unpacker both count toward the 4,096 bytes.
- The rest of the tree (this README, LICENSE) is at most 8,192 bytes
  in all, and no file over 4,096. MIT licence.

## Grade it locally

You need the `makefun` CLI and the competition's dev kit. Download the
kit, unpack it, and from inside it run:

    makefun grade-local --kit . --bot <your repository> --driver docker

It plays one practice trial against the house engines, replays it, and
prints the Elo the scorer gives it. Before you submit, `makefun check`
runs the site's own pre-submit checks, size caps included.

---

<sub>Entered on [make.fun](https://make.fun/?ref=vh95w5pk&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
