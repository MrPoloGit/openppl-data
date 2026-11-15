# OpenPPL registry

`symbols.json` is the real, usable registry, **273 directly-named
symbols plus two fully parametric categories** (`Technical`'s range
entries and `PokerTracker`'s prefix/postfix template), covering
`BettingAction`, `BetsizesAndStacksizes`, `Board`, `Handstrength`,
`Other`, `Player`, `Position`, `Technical`, and `PokerTracker`. This is
what `openppl-ls` should load once it's wired up to use it.

## Licensing posture, read this before adding or editing an entry

The OpenPPL manual (`docs/openppl stuff/*.html`) is GPLv3-licensed.
This project is going open source, so the registry could not ship as a near-verbatim
extraction of the manual's own table cells, that would make the
registry (and arguably anything that statically includes it) a
derivative work of GPLv3 text.

**The posture taken**: every `meaning` string in `symbols.json` was
composed independently, working from each symbol's **name alone** (plus
general poker and PokerTracker domain knowledge, VPIP, 3-bet, overpair,
backdoor draw, etc. are standard industry terminology, not expression
original to the OpenPPL manual), not copied or closely paraphrased from
the manual's own sentences. The intermediate, near-verbatim extraction
that *was* read while first researching this project
(`raw_symbols.json`, produced by `../scripts/extract_symbols.py`) is
**gitignored and never published** (see this directory's `.gitignore`
and that script's own header), it exists only as a private, local
regeneration for cross-referencing names, not as something this project
distributes.

**What this is, and isn't**: this is a good-faith, documented effort at
a defensible posture (facts and short functional descriptions in fresh
wording, not the manual's expression), not a legal certification. A
handful of entries are marked `"confidence": "medium"` where the bare
symbol name was genuinely ambiguous enough that the independently-
composed guess might be wrong about what the symbol actually does, that uncertainty is about correctness, not about licensing risk, and is
flagged for a different reason (don't trust those entries' *meaning*
without checking against a running OpenHoldem instance; the wording
itself still wasn't copied from the source).

## Two confirmed data artifacts, carried over from `raw_symbols.json` and recorded here

- **`Board`'s `SecondTopFlopCardPairedOnRiver`/`SecondTopFlopCardPairedOnTurn`
  entries were each listed twice** in the manual's own HTML, the second
  occurrence's description self-referentially reading "synonym for
  <same name>", almost certainly a markup artifact that collapsed two
  originally-distinct names (e.g. via lost formatting) into identical
  text, not a real duplicate. Recorded as found, not resolved.
- **One `Technical` entry's name had literal word-processor formatting-
  control text glued to it** in the manual's own HTML export
  (`\strikeout off\uuline off\uwave off` prefixed onto
  `RankOfSpareCardWhenTwoPairOnBoard`). Cleaned in `symbols.json` with a
  note, not silently dropped.

## Still not done

- **Not wired into `openppl-ls` yet.** The server currently builds and
  runs with zero registry dependency (`openppl-ls/README.md`); loading
  this file and using it for unknown-symbol diagnostics, the "false
  friends" case-collision warning, and
  real hover/completion is the next step, not done in this pass.
- **No cross-check against a second, independent source**:
  there is no vendored reference OpenHoldem implementation to validate
  these `meaning` strings or `type` guesses against.
- **`Board`'s own intro note** (that some board symbols imply others,
  e.g. a board set implies `PairOnBoard` is also true) isn't captured as
  a registry-level relationship, just as prose knowledge in this file.
