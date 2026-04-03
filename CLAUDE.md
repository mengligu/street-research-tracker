# CLAUDE.md — Street Research Tracker

## Project Overview

A single-page, self-contained macro research dashboard visualizing the geopolitical and economic impacts of the Hormuz Strait closure scenario ("Operation Epic Fury", Feb 28, 2026). Designed for financial analysts and traders at firms like Goldman Sachs, J.P. Morgan, Morgan Stanley, and Citi.

**Central Thesis**: The US/Israel–Iran conflict and Hormuz Strait closure triggered the largest energy supply shock in history — disrupting 17.6mb/d of Persian Gulf oil flows, forcing global stagflation, and creating the most significant central bank policy divergence since 2022.

---

## Architecture

This is a **zero-dependency, single-file application**. Everything lives in one file:

```
street-research-tracker/
└── index.html        # Entire app: HTML + embedded CSS + embedded JavaScript (778 lines)
```

No build system, no package manager, no server, no database. Deploy by copying `index.html` to any static host.

---

## Codebase Structure (index.html)

| Lines | Section | Purpose |
|-------|---------|---------|
| 1–6 | HTML boilerplate | DOCTYPE, `<head>`, meta, title |
| 7–305 | `<style>` | All CSS: dark theme, layout, animations |
| 307–335 | HTML markup | Header, thesis section, SVG container, cards grid, footer |
| 337–627 | `BRANCHES` constant | All 10 research topics with content |
| 629–682 | `buildMindMap()` | Renders SVG mind map (central node + 10 orbital nodes) |
| 683–750 | `buildCards()` | Renders expandable branch cards |
| 751–776 | Initialization | Calls both builders, wires up event listeners |

---

## Data Model

All content is stored in the `BRANCHES` array (JavaScript constant, `SCREAMING_SNAKE_CASE`). Each entry:

```js
{
  id: 'geopolitics',           // Unique string, used for DOM IDs and cross-referencing
  icon: '🌍',                  // Emoji for mind map and card header
  color: '#f59e0b',            // Hex color for theming this branch
  title: 'Geopolitics & Energy Shock',
  tagline: 'Short one-line summary shown in collapsed card',
  subthemes: [
    {
      label: 'Subtheme Title',
      color: '#hex',           // Can differ from parent branch color
      content: '<p>HTML content with <span class="data-pill">metrics</span></p>',
      trades: [                // Optional — omit if no trade ideas
        'Trade idea text'
      ]
    }
  ]
}
```

**10 Research Branches** (IDs): `geopolitics`, `central-banks`, `fx`, `rates`, `equities`, `credit`, `china`, `asia`, `us-econ`, `inflation`

Each branch has exactly 3 subthemes.

---

## Inline Data Pills

Metrics within content text use `<span>` tags with semantic color classes:

```html
<span class="data-pill">neutral value</span>       <!-- blue -->
<span class="data-pill up">positive value</span>   <!-- green -->
<span class="data-pill down">negative value</span> <!-- red -->
<span class="data-pill warn">warning value</span>  <!-- amber -->
```

---

## CSS Conventions

- **CSS variables** defined in `:root` — always use them, never hardcode colors inline:
  ```css
  --bg: #0a0e1a          /* page background (deep navy) */
  --surface: #111827     /* card background */
  --border: #1f2937      /* borders */
  --text: #e2e8f0        /* primary text */
  --muted: #64748b       /* secondary/muted text */
  --accent: #3b82f6      /* primary accent (blue) */
  --gold: #f59e0b        /* central thesis highlight color */
  ```
- **Naming**: BEM-style, e.g. `.branch-card`, `.branch-header`, `.subtheme-title`, `.data-pill`
- **Layout**: CSS Grid with `repeat(auto-fill, minmax(480px, 1fr))` for responsive cards
- **Expanded card state**: `.branch-card.expanded` — spans full width via `grid-column: 1 / -1`

---

## JavaScript Conventions

- **No frameworks** — plain ES6 vanilla JavaScript
- **Constants**: `SCREAMING_SNAKE_CASE` (e.g., `BRANCHES`)
- **Functions**: camelCase (e.g., `buildMindMap`, `buildCards`)
- **DOM manipulation**: Direct `innerHTML`, `appendChild`, `querySelector`
- **Template literals**: Used throughout for HTML generation
- **Event delegation**: Individual listeners attached to each card/node element

---

## UI Features

### Mind Map (SVG)
- Renders a central node ("HORMUZ SHOCK") with 10 orbital nodes
- Nodes are clickable: scrolls to and expands the corresponding branch card
- Hover effects via CSS (`.mind-node:hover`)

### Branch Cards
- Collapsed by default — shows icon, title, tagline, toggle arrow
- Click to expand: reveals subthemes with full analysis and trade ideas
- Expanded cards span full grid width

---

## Development Workflow

There is no build step. To develop:

1. Open `index.html` in a browser directly (`file://` protocol works)
2. Edit the file
3. Refresh browser to see changes

To run a local dev server (optional, for consistent behavior):
```bash
python3 -m http.server 8080
# then open http://localhost:8080
```

---

## Making Changes

### Adding a new research branch
1. Add a new entry to the `BRANCHES` array following the existing schema
2. The mind map and card grid will update automatically on next render

### Updating existing content
- Edit the `content` HTML string within the relevant `subthemes` entry in `BRANCHES`
- Use `<span class="data-pill [up|down|warn]">value</span>` for metrics

### Adding trade ideas to a subtheme
- Add a `trades: ['...']` array to the subtheme object
- If a subtheme has no trades, omit the `trades` key entirely

### Changing the central thesis
- Edit the `<div class="central-thesis">` section in the HTML markup (around line 315)
- The bold text, color, and supporting text can be updated directly in HTML

---

## Deployment

Static file — no server required:

```bash
# GitHub Pages, Netlify, Vercel — just push index.html
# AWS S3:
aws s3 cp index.html s3://your-bucket/index.html

# Any web server:
scp index.html user@server:/var/www/html/
```

Browser requirements: modern browser with ES6 + SVG + CSS custom properties support (Chrome 60+, Firefox 55+, Safari 12+).

---

## Testing

No automated tests exist. Manual testing checklist:
- All 10 mind map nodes are visible and clickable
- Clicking a node scrolls to and expands the correct card
- All cards expand/collapse correctly
- Data pills render with correct color states
- Trade idea sections appear only when trades are present
- Layout is responsive at different viewport widths

---

## Git Conventions

- **Main branch**: `main`
- **Feature branches**: `claude/<description>-<suffix>` for AI-authored changes
- There is currently one commit on `main` (initial commit)

---

## Key Constraints for AI Assistants

1. **Single-file only** — do not split into separate HTML/CSS/JS files unless explicitly asked
2. **No dependencies** — do not introduce npm packages, CDN links, or build tooling
3. **Hardcoded data** — the `BRANCHES` array is the source of truth; do not add a backend or API unless asked
4. **Preserve the dark theme** — all color changes must use or update CSS variables in `:root`
5. **Content is financial analysis** — maintain professional, precise language with specific numeric metrics
6. **No accessibility regressions** — if adding interactive elements, include keyboard support
