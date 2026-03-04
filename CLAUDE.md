# CLAUDE.md – dgpar-demo

Guidance for AI assistants working on this repository.

## Project Overview

**dgpar-demo** is a static, single-file HTML wizard that helps medical practice staff embed a patient self-service portal link (patienten.dgpar.de) into their practice website. The entire application lives in one file: `index.html`.

- **Language:** German (de-DE) — all user-facing text must remain in German
- **Type:** Static HTML5 SPA — no server, no build step, no dependencies
- **Purpose:** 3-step interactive guide for integrating the dgpar patient portal link into WordPress, TYPO3, Wix, Jimdo, Joomla, or custom-built medical websites

---

## Repository Structure

```
dgpar-demo/
├── index.html      ← Entire application (HTML + embedded CSS + embedded JS)
└── README.md       ← Minimal project title only
```

There is no `package.json`, no `node_modules`, no build tooling, no test suite, and no CI/CD configuration. This is intentional — keep it simple.

---

## Architecture

`index.html` has three clearly separated sections:

| Section | Lines (approx.) | Purpose |
|---|---|---|
| `<style>` | 8–118 | All styling via CSS custom properties + component classes |
| `<body>` HTML | 120–309 | Three wizard panels + header + progress bar |
| `<script>` | 311–355 | Wizard navigation, CMS selection, snippet switching, clipboard |

### Wizard Flow

```
Panel 1 (Was & Warum)
  → goTo(2)
Panel 2 (Mein System) — user selects a CMS
  → selectCms() → goTo(3)
Panel 3 (Anleitung) — CMS-specific steps + code snippets
  → goTo(2) (back)
```

### JavaScript State

Only two pieces of runtime state exist:

```js
let selectedCms = null;  // 'wordpress' | 'typo3' | 'wix' | 'jimdo' | 'joomla' | 'eigenbau'
const cmsNames = { ... }; // display labels for each CMS key
```

### Key Functions

| Function | Signature | What it does |
|---|---|---|
| `goTo(step)` | `step: 1 \| 2 \| 3` | Shows/hides panels and updates progress indicator |
| `selectCms(id, card)` | `id: string, card: HTMLElement` | Sets `selectedCms`, toggles `.selected` on CMS cards, shows the "Weiter" button |
| `switchSnippet(id, btn)` | `id: string, btn: HTMLElement` | Switches active code snippet tab |
| `copyCode(btn)` | `btn: HTMLElement` | Copies code from sibling `<code>` element to clipboard |
| `copyText(btn, text)` | `btn: HTMLElement, text: string` | Copies a plain text string to clipboard |

---

## CSS Conventions

### Custom Properties (theme tokens)

Always use these — never hardcode colors:

```css
--primary:  #0A4F6E   /* dark blue — headers, buttons, labels */
--accent:   #00A89D   /* teal — active states, highlights, dots */
--bg:       #F4F8FA   /* page background */
--border:   #D6E4EB   /* card/input borders */
--text:     #1A2E3A   /* body text */
--muted:    #6B8899   /* secondary text, hints */
--white:    #ffffff
--code-bg:  #0D2233   /* dark code block background */
```

### Naming

- **CSS classes:** kebab-case (`.prog-step`, `.cms-card`, `.step-row`, `.stab-panel`)
- **HTML IDs:** kebab-case with semantic prefixes:
  - Panels: `panel-1`, `panel-2`, `panel-3`
  - Progress steps: `prog-1`, `prog-2`, `prog-3`
  - CMS instruction blocks: `instr-wordpress`, `instr-typo3`, etc.
  - Code snippet panels: `snip-link`, `snip-button`, `snip-banner`, `snip-text`

### Component Classes Reference

| Class | Purpose |
|---|---|
| `.panel` / `.panel.active` | Wizard step visibility |
| `.prog-step` / `.active` / `.done` | Progress indicator states |
| `.cms-card` / `.selected` | CMS selection cards |
| `.stab` / `.stab.active` | Snippet tab buttons |
| `.stab-panel` / `.stab-panel.active` | Snippet tab content |
| `.instr-block` | Per-CMS instruction container (shown via `display` inline style) |
| `.step-row` | Numbered instruction step row |
| `.code-block` | Dark code display area |
| `.btn-primary` / `.btn-secondary` | Action buttons |
| `.why-card` / `.service-item` | Info grid cards on Panel 1 |

### Responsive Breakpoint

One breakpoint at `max-width: 580px`:
- 2-column grids (`why-grid`, `services-grid`) collapse to 1 column
- `cms-grid` stays 2 columns (from 3)
- Progress labels hidden; padding reduced

---

## JavaScript Conventions

- **Variables:** camelCase
- **Functions:** camelCase, short and focused (do one thing)
- **DOM selection:** `document.getElementById` and `document.querySelectorAll`
- **No framework, no modules, no transpilation** — plain ES6 inline in a `<script>` tag
- **Event handlers** are `onclick` attributes on HTML elements (inline style)
- Keep the `<script>` block minimal; avoid growing it unnecessarily

---

## Content Conventions

- All UI text is in **German**. Do not switch to English.
- The patient portal URL is always `https://patienten.dgpar.de` — do not change it.
- CMS keys are lowercase short strings: `wordpress`, `typo3`, `wix`, `jimdo`, `joomla`, `eigenbau`.
- Adding a new CMS requires three touches:
  1. A new `.cms-card` in Panel 2 with `onclick="selectCms('newkey', this)"`
  2. A new `instr-newkey` block in Panel 3
  3. A new entry in the `cmsNames` object in `<script>`

---

## Fonts

The application uses Google Fonts (loaded from an external CDN):

- **DM Serif Display** — headings (`.step-title`)
- **DM Sans** — all body text, buttons, UI elements

Do not add other fonts. Fallback is `sans-serif`.

---

## Development Workflow

Since there is no build system, development is simple:

1. Edit `index.html` directly in any text editor
2. Open `index.html` in a browser to preview
3. No compilation, bundling, or server required
4. For live reload, any static file server works (e.g., `python3 -m http.server`)

### Testing

There are no automated tests. Manual browser testing covers:
- Stepping forward and backward through all 3 wizard steps
- Selecting each CMS option and verifying the correct instruction block appears
- Switching between all 4 code snippet tabs (Text-Link, Button, Info-Banner, Fertige Texte)
- Testing "Kopieren" (clipboard) buttons
- Responsive behavior at ≤ 580px width

---

## Git Workflow

- **Active development branch:** `claude/claude-md-mmbuq3bcq9tzfjtv-FE4dU`
- **Remote:** `http://local_proxy@127.0.0.1:21996/git/laptoph123/dgpar-demo`
- Push with: `git push -u origin <branch-name>`
- Keep commits focused and descriptive

---

## Key Rules for AI Assistants

1. **Do not add a build system** unless explicitly requested. The zero-dependency design is intentional.
2. **Do not split the single file** into separate HTML/CSS/JS files without explicit instruction.
3. **Keep all UI text in German.**
4. **Use CSS custom properties** (the `--variable` tokens) for all colors — never hardcode hex values.
5. **The patient portal URL** (`https://patienten.dgpar.de`) is a fixed external URL — do not modify it.
6. **Do not add a backend** — this is a static file.
7. **Do not add testing infrastructure** unless explicitly requested.
8. **Preserve the inline `onclick` pattern** for event handlers — do not refactor to `addEventListener` without good reason.
9. When adding a new CMS, touch all three required locations (Panel 2 card, Panel 3 instruction block, `cmsNames` object).
10. Validate changes by mentally walking through the 3-step wizard for each supported CMS.
