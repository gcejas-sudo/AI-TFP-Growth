# Working notes for this repo

## Architecture — deliberate, do not "clean up"
- Each page is a **single self-contained HTML file**: styles, data and behaviour
  inlined. Do NOT extract a shared stylesheet or split data into .js files.
  A previous attempt at this broke single-file preview.
- Four pages, four tabs: index (TFP calculator), world (map), agentic,
  occupations. Tab links are relative filenames; all four must be siblings,
  index.html at repo root.

## Data status
- **Every number in this repo is a placeholder.** `MAP_DEMO` in world.html and
  `OCC_DEMO` in occupations.html gate red warning banners. Do not remove the
  banners while the values are invented.

## Analytical rules that must not be silently changed
- Map layer 2 (robotics) takes routine-**manual** employment only. A composite
  RTI carries routine-cognitive work — clerical — which belongs in layer 1.
  Using a composite in both blurs the two channels the page exists to separate.
- Layer 3 (labour scarcity) is a moderator, not a risk measure. It is also
  available as a multiplier on layers 1, 2 and 4 via the weighting checkbox.
- Map layer 2 measures **vulnerability, not adoption**. Robot density would
  light up the same countries as layer 1 and show nothing.
- Missing data renders **hatched, never pale**. Pale means low; hatched means no
  value loaded. Conflating them reads as "safe" for countries whose employment
  these measures don't cover.
- Each map layer keeps its own colour ramp and its own unit string. They are not
  comparable and must never share a legend.

## Page 1 number line
- Three lanes because TFP, labour productivity and GDP growth are different
  quantities. Never merge them onto one axis.
- Goldman's 7% is a GDP *level* effect and appears only in detail text, never as
  a position on a per-year axis.
- Ranges (OECD, McKinsey) render as bands, not pins — they are scenario ranges,
  not point estimates.
- Keep the Goldman Nov 2023 revision marker. Same authors revising down by
  two-thirds; it shows updating rather than two camps shouting.
- Keep the Hulten caveat in the closing box: first-order approximation, valid
  for small changes, drops reallocation and price effects. It's the objection to
  the whole family of estimates, not to any input.

## Verified figures
- Acemoglu (2024/25): 20% x 23% x 27% x 53.5% = 0.66% cumulative, 0.07pp/yr.
  April 2024 draft used 57% labour share and gave 0.71%.
- Aghion & Bunel (2024): 60% x 50% x 40% x 57% = 6.84%, 0.68pp/yr.
- Acemoglu's parameters with the OECD's 40% adoption gives only 0.12pp/yr — the
  adoption assumption alone does not explain the OECD's 0.25-0.6 range.
