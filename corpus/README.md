# OpenPPL corpus

Shared by
`tree-sitter-openppl` and `openppl-ls`, same role `phh-data/corpus`/
`ohh-data/corpus` play for their languages.

Every fixture here is built directly from the manual's own *complete*
worked examples (not its many abbreviated `"..."` snippets, see
`tree-sitter-openppl/README.md`'s note on why those are treated as
documentation shorthand, not real syntax to support). `valid/`'s fixtures
predate `openppl-data/registry/symbols.json`  and have since been corrected
to pass cleanly against the real registry too, see "What wiring in the
registry found" below for what that correction surfaced.

## `valid/`

| File | What it exercises | Sourced from |
|---|---|---|
| `structure-example.ohf` | The four reserved sections, a comment, two `WHEN ... FORCE` rules | `Structure_Of_A_PPL_File.html`'s own code block |
| `operators.ohf` | Every confirmed operator (`=`, `!=`, `<`, `>`, `<=`, `>=`, `~~`, `+`, `-`, `*`, `/`, `And`, `Or`, `Not`), the `relative_quantity` idiom (`50% PotSize`), both `hand$`/`board$` forms including both confirmed `board$...SUITED` spellings, `Set` statements (both PPL-level and memory-symbol forms), two user-defined functions (`f$Outs`/`f$CardsLeft`) called from an odds-and-outs expression | Assembled from individually-confirmed examples across `Mathematical_Expressions.html`, `Hand_And_Board_Expressions.html`, `differences to shanky.html`, `User_Defined_Variables.html`, `OpenPPL_In_Practice.html` |
| `handlist.ohf` | Two handlists (`##list007##`/`##listOfBiggestTrash##`) and using one by name in a `WHEN` condition | `Handlists.html` |

## `invalid/`

| File | Violated rule |
|---|---|
| `missing-force.ohf` | A `WHEN hand$AA RaiseMax` rule with no `FORCE`, confirmed to produce a real grammar-level `ERROR` node (`FORCE` is mandatory in OpenPPL, unlike Standard PPL, per `Actions.html`'s own "Being strict: using 'FORCE'" section). The error-recovery cascade also trips two of `openppl-ls`'s new registry-backed checks on the *next* line's tokens, expected, not a separate bug (`openppl-ls/README.md`). |
| `handlist-wrong-name.ohf` | `openppl-ls`'s one registry-free semantic check, a section whose body looks like a handlist but whose name doesn't start with `list` |
| `unknown-symbol.ohf` | `openppl-ls`'s registry-backed checks: a genuinely unknown symbol, and a lowercase `bet` written where the action `Bet` was meant (confirms the "false friends" hint fires) |

## What wiring in the registry found

`operators.ohf` was written before `openppl-data/registry/symbols.json`
existed, directly from manual text, and running the registry-backed
`openppl-check` against it for the first time surfaced three real
things, not bugs in the checker:

1. **`Outs`/`CardsLeft` were never real registered symbols.**
   `Mathematical_Expressions.html`'s own odds-and-outs example uses them
   as bare, undefined names in a passage the manual itself calls "a bit
   simplistic", illustrative pseudo-code, not something a real file
   would contain. The real, complete pattern
   (`OpenPPL_In_Practice.html`'s "Calling According To Odds And Outs")
   defines them as user functions (`f$Outs`, `f$CardsLeft`), which the
   fixture now does too.
2. **A genuine spelling discrepancy in the manual itself**: `differences
   to shanky.html`'s own prose examples write `Stacksize`, but
   `betsizes.html`'s reference table lists `StackSize`, different
   capitalization for the same symbol, confirmed by comparing the two
   pages directly, not assumed. The fixture uses the reference table's
   spelling (`StackSize`), since that's the more authoritative form for
   what a "valid" example should contain; the discrepancy itself is
   recorded in `openppl-data/registry/README.md`, not silently resolved.
3. **Two real, confirmed named constants the symbol-reference tables
   never listed**: `First` and `Last`, both used directly in real manual
   examples (`Mathematical_Expressions.html`'s `Position != First`;
   `OpenPPL_In_Practice.html`'s `Position = Last`) but absent from every
   one of the 9 scraped reference tables. Added to
   `openppl-data/registry/symbols.json`'s `Technical` category with that
   provenance noted.

## Not added, and why

- **No fixture for the one-level-only open-ended-`WHEN` nesting limit.**
  The grammar doesn't model nesting depth at all (`tree-sitter-openppl`'s
  own README explains why, it's a flat-sequence property for a future
  checker, not a grammar-level construct), so there is no way to write a
  fixture that the *grammar* would reject for violating it; that
  diagnostic belongs to `openppl-ls`, not built yet.
- **No fixture using `hand$AKs`'s specific-suit-cards meaning
  contrasted against a handlist's `AKs`'s suited-hand meaning.** Both
  parse fine individually (confirmed in `operators.ohf` and
  `handlist.ohf` respectively); a dedicated "same text, different
  meaning" test would need semantic awareness this grammar deliberately
  doesn't have.
