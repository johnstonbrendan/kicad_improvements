# kicad_improvements

Small CLI tools that wrap `easyeda2kicad` with my usual defaults and add an
opt-in AI cleanup pass for symbol metadata.

## Tools

### `lcsc <LCSC_ID> [...]`
Runs `easyeda2kicad --full --overwrite --output $KICAD9_3RD_PARTY/libraries/easyeda.kicad_sym`
for one or more LCSC IDs. Any extra flags pass straight through.

```
lcsc C2453576
lcsc C2453576 C137381
lcsc C2453576 --use-cache
```

`--overwrite` here only refreshes the entry for the specific LCSC ID — it does
not wipe the library.

### `lcsc <LCSC_ID> --enrich`  (or `lcsc-enrich <LCSC_ID>`)
After import, looks the part up on lcsc.com via `claude -p` and proposes a
cleaner `Description` if the current one is weak/empty. **Preview only — never
writes by default.** Shows a unified diff; pass `--apply` to actually patch the
file. Other weak fields (`ki_keywords`, `Value`) are surfaced as warnings with a
copy-paste follow-up command, not auto-changed.

```
lcsc-enrich C2453576              # preview
lcsc-enrich C2453576 --apply      # write the description shown above
lcsc-enrich C2453576 --set ki_keywords "buck converter 3A"
```

### `easyeda2kicad`
Transparent wrapper around the real `easyeda2kicad`. Identical behavior, except
on `--help` / `-h` it appends a one-paragraph reminder that `lcsc` exists.

## Install

```sh
ln -sf "$PWD/bin/lcsc"          ~/.local/bin/lcsc
ln -sf "$PWD/bin/lcsc-enrich"   ~/.local/bin/lcsc-enrich
ln -sf "$PWD/bin/easyeda2kicad" ~/.local/bin/easyeda2kicad
```

`~/.local/bin` is on `PATH` ahead of the real `easyeda2kicad` location, so the
wrapper takes precedence. Requires `easyeda2kicad` and (for enrichment) the
`claude` CLI on `PATH`.

## Environment

- `KICAD9_3RD_PARTY` — defaults to `/Users/brendan/Documents/KiCad/9.0/3rdparty`.
  The library file written to is always `$KICAD9_3RD_PARTY/libraries/easyeda.kicad_sym`.
