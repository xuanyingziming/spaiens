# Sapiens Timeline

An interactive single-page timeline website for **"Sapiens: A Brief History of Humankind"** (人类简史) by Yuval Noah Harari. Explore the journey of humankind through a dark star-chart interface — zoom and pan across the ages as if navigating a celestial map.

## Visual Style

- **Dark star-chart aesthetic**: deep space blue-black background with drifting star particles
- **Gold accents** highlighting key eras and milestones
- **Canvas 2D** rendering for smooth zooming and panning across time

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

1. Open `data/timeline.json`
2. Add a new JSON object to the array following the schema above
3. Ensure `year` is numeric (use negative numbers for BCE dates)
4. Refresh the page — the node will appear on the timeline automatically

The timeline canvas auto-sorts nodes by their `year` value and places them along the chronological axis.

## Tech Stack

- **Pure HTML, CSS, and JavaScript** — no frameworks, no build tools
- **Canvas 2D API** for rendering the interactive star-chart timeline
- **Google Fonts** for typography

## License

MIT