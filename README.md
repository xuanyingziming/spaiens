# 人类简史 · 编年史 — Sapiens, An Illuminated Codex

An interactive single-page chronicle of **"Sapiens: A Brief History of Humankind"** (人类简史) by Yuval Noah Harari. The site reads like an antique illuminated manuscript: you scroll *down through the leaves* of a printed codex, era by era, from the Cognitive Revolution to the age of AI. Bilingual throughout (中文 / English).

## Visual Style

A classical printed-atlas aesthetic — *古典书籍质感* — fused with refined modern interaction:

- **Parchment & ink**: a warm aged-paper ground with grain and an edge vignette; text set in deep bistre ink, never pure black.
- **Pigment palette**: cinnabar (朱砂) seals and rules; antique gold leaf for ornament and the reading-progress spine. Each of the four eras carries a historical pigment — woad indigo (Cognitive), terre verte (Agricultural), Tyrian purple (Unification), verdigris (Scientific).
- **Letterpress typography**: Noto Serif SC (思源宋体) for Chinese, Cormorant Garamond & EB Garamond for English display/body, Spline Sans Mono for marginal years and labels. Drop caps, fleurons, Roman numerals, and small-caps running heads.
- **The codex spine**: a continuous ruled thread down the page whose gold fill tracks your reading position; events are nodes upon it — a cinnabar seal for *keystone* moments, a gold ring for *major*, plain ink for the rest.
- **Engraved frontispiece**: a hand-built armillary-sphere ornament, a double-rule frame with corner fleurons, and a cinnabar 「史」 seal.
- **Interaction**: scroll-revealed entries, a folio-style detail overlay (with era-tinted drop caps and a per-era seal), an active table-of-contents in the top bar, and a 双 / 中 / EN language toggle. Honors `prefers-reduced-motion`.

## How to Run

Zero dependencies, zero build steps. Just open `index.html` in any modern browser.

```
open index.html
```

Or double-click the file in your file explorer.

## Data Format

Timeline nodes are stored in `data/timeline.json`. Each node uses the following fields:

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier (e.g., `"cognitive-revolution"`) |
| `year` | number | Approximate year (negative for BCE) |
| `yearDisplay` | string | Chinese year label (e.g., `"约7万年前"`) |
| `yearDisplayEn` | string | English year label (e.g., `"~70,000 BCE"`) |
| `era` | string | Era category (e.g., `"Cognitive Revolution"`, `"Agricultural Revolution"`, `"Scientific Revolution"`) |
| `titleZh` | string | Event title in Chinese |
| `titleEn` | string | Event title in English |
| `summaryZh` | string | Short summary in Chinese |
| `summaryEn` | string | Short summary in English |
| `detailZh` | string | Extended description in Chinese |
| `detailEn` | string | Extended description in English |
| `significance` | string | Why this event matters |
| `tags` | string[] | Categorization tags |

### Example Entry

```json
{
  "id": "cognitive-revolution",
  "year": -70000,
  "yearDisplay": "约7万年前",
  "yearDisplayEn": "~70,000 BCE",
  "era": "Cognitive Revolution",
  "titleZh": "认知革命",
  "titleEn": "The Cognitive Revolution",
  "summaryZh": "智人发展出前所未有的语言和思维能力。",
  "summaryEn": "Homo sapiens developed unprecedented language and cognitive abilities.",
  "detailZh": "认知革命使智人能够以大规模、灵活的方式合作...",
  "detailEn": "The Cognitive Revolution enabled Sapiens to cooperate in large, flexible groups...",
  "significance": "Enabled large-scale cooperation through shared myths and language.",
  "tags": ["cognition", "language", "cooperation"]
}
```

## Adding New Timeline Nodes

`data/timeline.json` is the canonical source of the data. For zero-config `file://` use, `index.html` carries an **inlined copy** of that array (assigned to `TIMELINE_DATA`). To add or edit an event:

1. Edit the object in `data/timeline.json` following the schema above (use negative `year` for BCE).
2. Re-inline it into `index.html` so the live page picks it up:

   ```powershell
   $json = (Get-Content -Raw -Encoding UTF8 data/timeline.json).Trim()
   $html = Get-Content -Raw -Encoding UTF8 index.html
   $html = $html -replace '(?s)(const TIMELINE_DATA = )\[.*?\](;)', ('$1' + [regex]::Escape($json) + '$2')
   [IO.File]::WriteAllText("$PWD/index.html", $html, (New-Object Text.UTF8Encoding $false))
   ```

3. Refresh the page. Events are grouped into the four eras (by their `era` field) and sorted by `year` within each era; `significance` (`key` / `major` / `minor`) controls the node and heading treatment.

## Tech Stack

- **Pure HTML, CSS, and JavaScript** — no frameworks, no bundler. A single `index.html`.
- **Scroll-driven UI**: `IntersectionObserver` reveals, a `requestAnimationFrame`-throttled scroll handler drives the progress thread, spine fill, and active table-of-contents.
- **Google Fonts**: Noto Serif SC, Cormorant Garamond, EB Garamond, Spline Sans Mono.

## License

MIT