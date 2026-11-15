# openppl-data

Shared, non-code data for OpenPPL (`.ohf`) tooling: the symbol registry
`openppl-ls` and `tree-sitter-openppl` validate/highlight against, the
test corpus every OpenPPL-consuming package (grammar, language server,
editor integrations) runs its checks against, and the script used to
regenerate the registry from source material. Not a standalone tool
itself, every other OpenPPL package expects this repo as a sibling
checkout.

## Structure

```
openppl-data/
├── registry/   # symbols.json: known OpenPPL/OpenHoldem symbols and
│               # lists, the source of truth `openppl-ls` reads instead
│               # of hard-coding. Regenerated, not hand-maintained.
├── corpus/     # Sample .ohf files, split into valid/ and invalid/,
│               # used as test fixtures by tree-sitter-openppl,
│               # openppl-ls, and the editor packages' own test suites.
└── scripts/    # extract_symbols.py: builds registry/symbols.json from
                # source material; rerun this instead of hand-editing
                # the registry.
```

See `registry/README.md` and `corpus/README.md` for what each file
inside covers and how confident each fact/fixture is.
