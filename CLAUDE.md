# CLAUDE.md — Insights Mind Map

## Project Overview

A **single-file, static interactive mind map** for exploring 67 categorized insights. The entire application (HTML, CSS, JavaScript, and data) lives in one file: `index.html`. There is no build system, no package manager, and no backend.

## Repository Structure

```
insights-mind-map/
├── index.html      # Entire application — 571 lines
└── CLAUDE.md       # This file
```

## Technology Stack

| Concern          | Choice                                          |
|------------------|-------------------------------------------------|
| Visualization    | vis-network v9.x (CDN via unpkg)                |
| Language         | Vanilla JavaScript (ES6+), HTML5, CSS3          |
| UI framework     | None — direct DOM manipulation                  |
| Build system     | None — open `index.html` directly in a browser  |
| Package manager  | None                                            |
| Testing          | None                                            |
| Linting          | None                                            |

External dependency loaded from CDN:
```html
<script src="https://unpkg.com/vis-network/standalone/umd/vis-network.min.js"></script>
```

## Application Architecture

### Single-File Layout (`index.html`)

| Lines     | Section                        |
|-----------|--------------------------------|
| 1–7       | HTML head / CDN script tag     |
| 8–202     | Embedded `<style>` block       |
| 205–290   | HTML markup (sidebar + canvas) |
| 292–362   | Insights data array            |
| 363–415   | vis.js node/edge construction  |
| 416–453   | vis.js Network instantiation   |
| 454–568   | Event handlers and functions   |

### Node Hierarchy

```
Central Hub (id: 0)
└── Category nodes (ids: 100–104, one per category)
    └── Insight nodes (ids: 1–67)
```

### Insight Data Schema

Every entry in the `insights` array follows this shape:

```javascript
{
  id: number,            // 1–67, unique
  label: string,         // Short title shown on the graph node
  category: string,      // One of the five category values below
  description: string,   // One-sentence summary
  tags: string[],        // Lowercase kebab-case strings
  impact: string         // One-sentence outcome/value statement
}
```

**Valid category values:**
- `"Technical"`
- `"Tool & Workflow"`
- `"Documentation"`
- `"Problem-Solving"`
- `"Methodology"`

### Category Color Map

| Category         | Color     |
|------------------|-----------|
| Technical        | `#f093fb` |
| Tool & Workflow  | `#4facfe` |
| Documentation    | `#43e97b` |
| Problem-Solving  | `#fa709a` |
| Methodology      | `#feca57` |
| Central Hub      | `#667eea` |

## Key Functions

| Function                | Location  | Purpose                                              |
|-------------------------|-----------|------------------------------------------------------|
| `showDetails(insight)`  | line 466  | Populates and reveals the `#detail-panel` sidebar    |
| `filterByCategory(cat)` | line 502  | Shows only nodes matching the given category         |
| `filterByTag(tag)`      | line 522  | Toggles tag filter; supports multi-select            |
| `resetView()`           | line 541  | Clears all filters, hides detail panel, fits canvas  |
| Search listener         | line 477  | Filters nodes by label/description/tags/category     |

## Development Conventions

### Naming
- JavaScript variables and functions: `camelCase`
- CSS classes: `kebab-case`
- Insight tags: `lowercase-kebab-case`
- Category labels: Title Case with spaces

### Insight IDs
- IDs **1–67** are occupied; new insights must use the next available integer.
- The central hub is always id `0`; category nodes are ids `100–104`.
- Do not reuse or reorder IDs — vis.js references them by identity.

### Adding a New Insight
1. Append a new object to the `insights` array (line ~362) using the schema above.
2. Assign the next sequential `id`.
3. Use one of the five valid `category` values exactly as written.
4. Update the hub node label and the stats panel if the count changes:
   - Hub node label: line 375 (`"Key Insights\n(67)"`)
   - Stats panel HTML: line 211 (`<p><strong>Total Insights:</strong> 67</p>`)
5. If introducing a new popular tag, add a `<button>` to the tag filter group (lines 237–248).

### Modifying Styles
All styles are in the `<style>` block (lines 8–202). There is no external stylesheet. Follow the existing 4-space indentation and property ordering patterns.

### Modifying vis.js Options
Physics and interaction options are in the `options` object (lines 420–450). Key physics knobs:
- `gravitationalConstant` — repulsion between nodes (more negative = more spread)
- `springLength` — default edge length
- `stabilization.iterations` — higher = more settled initial layout (slower load)

## How to Run

No installation required. Open `index.html` in any modern browser:

```bash
# Option 1 — file:// protocol (works for most browsers)
open index.html

# Option 2 — local HTTP server (avoids any CORS quirks)
python3 -m http.server 8080
# then visit http://localhost:8080
```

Requires an internet connection on first load to fetch vis-network from unpkg CDN.

## Git Workflow

- **Default branch:** `master`
- **Remote:** `eatenbywo1ves/insights-mind-map`
- Commit messages follow conventional-commits style (`feat:`, `fix:`, `docs:`, etc.)
- There is only one source file (`index.html`); keep all changes in that file unless adding documentation.

## What This Project Does Not Have

Noting absences prevents accidental introduction of complexity:
- No `package.json`, no `node_modules`, no npm scripts
- No TypeScript — plain JavaScript only
- No test suite
- No linter or formatter config
- No CI/CD pipeline
- No backend / API
- No environment variables
- No authentication
- No service workers / PWA manifest
