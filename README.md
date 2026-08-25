# AI, Productivity & Work

A four-page static site. No build step, no dependencies, no framework. **Each page is a single self-contained HTML file** — styles, data and behaviour are all inlined, so any page works on its own whether you open it locally, drop it in a CMS, or serve it from Pages.

| Page | File | What it does |
|---|---|---|
| Comparing TFP Equations | `index.html` | Interactive version of the task-based TFP formula: four inputs, live result, Acemoglu / Aghion presets |
| AI Throughout the World | `world.html` | Clickable world map; each country opens a bubble fed from `country-notes.js` |
| Agentic AI | `agentic.html` | Adviser vs. autonomous decision-maker, linking to Acemoglu's Project Syndicate commentary |
| AI Throughout Occupations | `occupations.html` | Occupation search with exposure bars, plus exposure by level of the organisation |

```
index.html          TFP calculator
world.html          map — country notes block sits at the top of the file,
                    the generated geometry below it
agentic.html
occupations.html    occupation rows + org-level definitions at the top of the file
```

Each file opens with the same shared token/component stylesheet, then a page-specific `<style>` block. Editing a brand colour means changing the `:root` block in all four files — a find-and-replace on the hex value does it.

## The four inputs (page 1)

```
Cumulative TFP gain = GDP share of exposed tasks
                    × share profitable to automate
                    × average labour cost savings
                    × AI-exposure-adjusted labour share
```

| Component | Acemoglu | Aghion & Bunel | Ratio |
|---|---|---|---|
| GDP share of exposed tasks | 20% | 60% | 3.0× |
| Share profitable to automate | 23% | 50% | 2.2× |
| Average cost savings | 27% | 40% | 1.5× |
| AI-exposure-adjusted labour share | 53.5% | 57% | 1.07× |
| **Cumulative, 10 years** | **0.66%** | **6.84%** | |
| **Annual** | **0.07pp** | **0.68pp** | **≈10×** |

## The map (page 2)

Four layers, one map, switched with the buttons at the top:

| Layer | What it measures | Where to get it |
|---|---|---|
| **1 — AI and cognitive work** | Employment in cognitive and routine-cognitive occupations exposed to AI | ILO GenAI index (ISCO-08) for coverage; Cazzaniga et al. / Pizzinelli et al. for the income-group anchors |
| **2 — Robotics and manual work** | Employment in the routine-**manual** occupations robotics targets | Lewandowski country-specific RTI, manual component only |
| **3 — Labour scarcity** | Incentive to automate, not risk | Old-age dependency, working-age population growth inverted, wage level, vacancy rates |
| **4 — One technology** | Employment in the occupations a single named technology targets | Your own directory, in `TECHNOLOGIES` |

Layer 3 is not a peer of 1 and 2 — it's a moderator. The **Weight by labour scarcity** control multiplies whichever exposure layer is active by the scarcity index, which is where the analysis actually happens: on the placeholder numbers, layer 2 unweighted ranks Vietnam and Bangladesh top, and weighted it flips to China, Poland, Korea and Japan. Exposure says where the work is; scarcity says where anyone has a reason to replace it.

Two design rules baked in, worth keeping:

- **Each layer has its own colour ramp and its own unit string.** They are not comparable to each other, so they must never share a legend. Blue for AI, maroon for robotics, sage for scarcity, slate for a selected technology. Layer 4 renders flat grey until a technology is chosen, rather than guessing at an aggregate across technologies with uneven coverage.
- **Missing data is hatched, not pale.** A light colour means "low"; the hatch means "no value loaded". Conflating the two would read as "safe" for countries where much employment is informal or agricultural and simply isn't covered by these measures.

Do not put a composite RTI in layer 2. Composite RTI carries routine *cognitive* work — clerical, the single most AI-exposed group — which belongs in layer 1. Using it in both blurs exactly the two channels the page separates.

### Version 4 — one technology at a time

Selecting layer 4 opens a search panel above the map. Search runs across name, developer, developer's country, status, work type, occupation codes and tags; two dropdowns filter by work type (Cognitive / Manual) and status. Below it, **Browse all N technologies** opens a sortable directory table — the browse path for when the list is long and you don't know what you're looking for. Search results are capped at 60 rows; the directory shows everything.

Selecting a technology shades every country by how much of the targeted work is done there and outlines the developer's country, so invention and displacement appear in one frame. The scarcity weighting reverses the reading: unweighted shows *where this work is done*, weighted shows *where the machine plausibly pencils out*. On the placeholder data the berry harvester runs from Madagascar and Ethiopia to Mexico and Poland.

Technology schema:

```js
{
  id: "berry-harvester",              // unique slug
  name: "Autonomous berry harvester",
  status: "Pilot",                    // Deployed | Pilot | In development | Patent only
  developer: "—",
  developerCountry: "United States of America",  // outlined on the map
  target: "Manual",                   // Cognitive | Manual — drives the filter
  occupations: ["ISCO 9211"],         // searchable
  tags: ["Agriculture"],              // searchable
  note: "",                           // shown under the map when selected
  exposure: { "Madagascar": 78 }      // your number per country, 0-100
}
```

The filter dropdowns build themselves from whatever `target` and `status` values exist in the data, so adding a new status string is enough — no code change.

**On the Webb link.** The panel links to Webb's page as the source and method, not as a device directory, because it isn't one: it gives occupation-level scores for three broad categories (AI, software, robotics). A specific machine needs its own patent selection run through the verb-noun matching, or a hand-coded target-occupation list. Hand-coding is the tractable route for a curated set. The real constraint is employment data — ILOSTAT reports ISCO at 2-digit for most countries and 3- or 4-digit for few, so berry pickers (ISCO 9211) will resolve for fewer countries than clerical work (ISCO 41xx) no matter how good the patent matching is.

## Adding country notes (page 2)

Open `world.html`. The first `<script>` in the document holds `COUNTRY_METRICS` and `TECHNOLOGIES`, ahead of the map geometry so you never scroll through it. One entry per country, keyed by name as shown on the map or by UN M49 numeric code:

```js
"Nigeria": { ai: 16, robotics: 26, scarcity: 13, wage: 7,
             tags: ["Textiles"], notes: "<p>Your text here.</p>" }
```

Every field is optional. Any country you click opens a bubble showing all three layer values, with the active one bolded and missing values marked "no value" rather than zero. Country name spellings follow Natural Earth — a few to watch: `United States of America`, `Dem. Rep. Congo`, `Vietnam`, `South Korea`, `Bosnia and Herz.`, `Central African Rep.`, `Dominican Rep.`, `S. Sudan`, `Côte d'Ivoire`, `Eq. Guinea`, `Solomon Is.`.

### Regenerating the map

The second `<script>` in `world.html` holds 176 country outlines from Natural Earth 110m (via the `world-atlas` npm package, ISC licence), pre-projected to Equal Earth and written as SVG path strings — so the map needs no mapping library and works offline. Antarctica is dropped and antimeridian-crossing rings are split. Regenerate only if you want a different projection or resolution; otherwise leave it alone.

## Occupation data (page 3)

**Every number in this repo is a placeholder.** Both `world.html` and `occupations.html` carry invented values so the controls can be tested; each shows a red banner that disappears when you set `MAP_DEMO` / `OCC_DEMO` to `false`.

**The values currently at the top of `occupations.html` are placeholders** — round illustrative numbers so the page renders. A red banner says so on the page; it disappears when you set `window.OCC_DEMO = false`.

Each row takes `title`, `soc`, `family`, `exposure` (% of tasks exposed), `hard` (% of *those* tasks that are hard to learn), `tier` (1 = top of the organisation, 5 = entry level) and optional `wage`. The org-chart ladder is computed from `tier`, so new rows appear there automatically. The `hard` field is what separates exposure from replaceability — it's the easy/hard-to-learn split Acemoglu uses to argue the headline exposure figures overstate near-term displacement.

## Branding

All colours and type are CSS custom properties in the `:root` block at the top of each page, sampled from shapingwork.mit.edu:

| Token | Value | Used for |
|---|---|---|
| `--navy` | `#15265B` | headings, footer, tabs, Acemoglu series |
| `--blue` | `#2456C8` | links, tab labels |
| `--sage` | `#A8BC9A` | hero gradient terminus, button chips |
| `--sage-dark` | `#5F7A4F` | active tab, Aghion series, hard-to-learn bars |
| `--maroon` | `#8B2226` | topic pills, live results |
| `--page` / `--pale` | `#EFEFEF` / `#EEF2FA` | page ground, callouts |

Type is Neue Haas Grotesk first, falling back to Inter Tight from Google Fonts for visitors without the licensed face; Roboto Mono for figures.

## Running it

Open `index.html` in a browser. That's it — nothing to serve, nothing to install. The only external requests are to Google Fonts, and the pages fall back to Helvetica/Arial without them.

## Publishing on GitHub Pages

1. Push everything to the default branch.
2. Settings → Pages → Source: *Deploy from a branch* → `main`, folder `/ (root)`.
3. Live at `https://<username>.github.io/<repo>/` in a minute or two.

```bash
git init
git add .
git commit -m "AI, Productivity & Work — four-page site"
git branch -M main
git remote add origin git@github.com:<username>/<repo>.git
git push -u origin main
```

## Sources

Acemoglu, *The Simple Macroeconomics of AI* (NBER WP 32487, 2024) · Aghion & Bunel, *AI and Growth: Where Do We Stand?* (2024) · Acemoglu, *Two Models for Agentic AI*, Project Syndicate, 28 March 2025 · component inputs from Eloundou et al. (2024), Pizzinelli et al. (2023), Svanberg et al. (2024), Besiroglu & Hobbhahn (2022), Noy & Zhang (2023), Brynjolfsson et al. (2023), Peng et al. (2023).
