# 01: touchy-claude avatar

First real recipe anyone ran through `mt-logo-render`
([Twin-Cities-Open-Systems/MT-logo-render](https://github.com/Twin-Cities-Open-Systems/MT-logo-render)) --
the tool existed but had never generated a real, used image before this.

## What's here

- `recipe.json` -- the recipe passed to `logo-render render`
- `derivation.txt` -- exactly how every field in the recipe was derived
- `avatar.png` -- the rendered output, 512x512, transparent background

## Methodology: quantitative, not aesthetic

Every field was derived from touchy-claude's real GitHub identity data --
`id`, `node_id`, `created_at` -- rather than picked by eye. touchy-claude
has no GPG fingerprint on file (a known gap in the fleet roster), so the
next-most-stable real identifier was used instead of a subjective color
choice.

```
seed = f"{github_id}:{github_node_id}:{created_at}"
hash = sha256(seed)
```

Every recipe field (shape, base/accent color, fill, mark, badge) comes from
successive byte offsets of that hash -- see `derivation.txt` for the exact
byte -> field mapping. Fully reproducible from the same three inputs.

## Bugs found along the way

Running one real recipe end-to-end surfaced 4 real bugs in `mt-logo-render`
against its own documented `CLI_CONTRACT.md` -- all fixed in
[PR #13](https://github.com/Twin-Cities-Open-Systems/MT-logo-render/pull/13):

1. `resolve -` / `render -` never actually read stdin.
2. Bare hex colors (`"00ff00"`, no `#`) were documented as valid but rejected.
3. Badge values are documented kebab-case (`corner-dot`) but the code required snake_case.
4. Every shape was rendered invisible against its own background -- `render_png`
   filled the canvas in `base_color` and then drew the shape *also* in
   `base_color`. Canvas is now transparent; the shape is the only opaque region.

## Status

This PNG is not (yet) the account's actual GitHub avatar -- GitHub's API has
no endpoint to upload one, and no browser-automation tool was available to
do it through the web UI. It's parked here until that gets done by hand.
