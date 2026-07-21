# Portfolio Redesign: "Bento & Bold"

Date: 2026-07-21
Status: Approved for implementation planning

## Context

`kanim21.github.io` currently uses a dark terminal/control-plane aesthetic
(green-on-black, fake terminal windows with red/yellow/green dots, a
multi-line typed boot sequence, YAML/`ls`-styled content blocks). It's
consistent across `index.html` and five project deep-dive pages under
`projects/`, all with duplicated inline `<style>` blocks (~800 lines each).

Goal: move to a visually playful but still clearly technical direction —
closer to modern dev-tool marketing sites (Linear, Vercel, Raycast) than a
literal terminal emulator. Chosen direction from visual review: **"Bento &
Bold"** — near-black canvas, one confident violet accent, rounded cards with
hover glow, bold sans headings, monospace demoted to an accent face.

## Scope

**Phase 1 (this implementation pass):**
- New shared `css/style.css` design system.
- Full rebuild of `index.html` on top of it (nav, hero, projects, skills,
  training, contact, footer).

**Phase 2 (explicitly out of scope for this pass — separate follow-up):**
- Apply the new tokens/typography/nav/card components to the shared chrome
  of the five project pages (`projects/*.html`): topbar, section headers,
  tags, callouts, buttons.
- Restyle each project page's custom interactive components (SVG
  architecture diagram with nodes/tooltips, step-through narration bar,
  glossary grid, risk table, chip panels) to match the new palette/shape
  language, without redesigning their structure or behavior.

Do not touch project-page content/copy or diagram logic in Phase 1.

## Visual system

**Color** (dark theme only, no light mode):
- `--bg: #0d0d14` — primary section background (near-black, cool undertone)
- `--bg2: #101019` — alternating section background
- `--surface: rgba(255,255,255,0.04)` / `--surface-border: rgba(255,255,255,0.08)` — card fill/border
- `--fg: #f2f1f8` — primary text
- `--muted: rgba(242,241,248,0.65)` — secondary text
- `--dim: rgba(242,241,248,0.4)` — tertiary/label text
- `--accent: #7c6cff` — primary violet (buttons, links, glow, headline highlight)
- `--accent-soft: #a599ff` — lighter violet (tag text, hover states)
- `--status: #5ff59b` — status/uptime dot (the one "server status" signal kept from the old identity)
- `--cyan: #22e0ff` — secondary accent (sparingly, e.g. secondary tags)
- `--amber: #ffb454` — note/callout accent (carried over semantic meaning from current `--amber`)

**Typography:**
- Headings: bold system sans (`-apple-system, 'Inter', 'Segoe UI', Roboto, sans-serif`), tight letter-spacing, same `clamp()`-based responsive scale as today just recolored/re-weighted.
- Body copy: same sans stack, regular weight.
- Monospace (`ui-monospace, 'SFMono-Regular', 'JetBrains Mono', ... monospace`) is demoted to an *accent* face only — not the body font.

**Shape:**
- Card/button border radius: 12–16px (up from 6–8px).
- Buttons: primary = violet fill + soft glow shadow on hover; ghost = subtle border, no fill.
- Cards: border + very subtle fill, hover = lift + glow border (no more terminal-window chrome).

## Component changes from current site

| Current | New |
|---|---|
| Fake terminal window (dots + titlebar) wrapping every content block | Plain rounded card, border + hover glow |
| Multi-line typed "boot sequence" in hero | Single line types out once (e.g. role line), cursor keeps blinking after — one nod, not a console log |
| `status.yaml` term-window table above project grid | Removed — duplicated info already on each card |
| Project cards: term-window chrome, status/CI text separate | Plain card; mono project name; status/CI as a small mono line inside the card; tag pills |
| Skills: fake `yaml` text block in a term-window | Grid of category cards (same card component as projects) |
| Training: fake `ls -la` listing in a term-window | Grid of cards grouped by issuer, checkmarks kept |
| Contact: term-window wrapper | Plain card |
| Section eyebrows (`# comment` prefix) | Kept as-is — still reads technical, not terminal-chrome-heavy |
| Monospace project names / tag pills | Kept — the intentional "light nod" to the old identity |

**Projects section layout:** uniform grid (equal-size cards), not a
varied-size bento grid — confirmed by visual review. ("Bento & Bold" is the
style name, not a literal layout requirement.)

## Technical approach

- Create `css/style.css` containing: CSS custom properties (tokens above),
  base/reset, nav, buttons, cards, tags, section-shared classes (container,
  section-comment, section-title), footer. This is linked by `index.html`
  (Phase 1) and will be linked by the five project pages in Phase 2.
- `index.html`: remove the inline `<style>` block, link `css/style.css`,
  rebuild markup section by section (nav → hero → projects → skills →
  training → contact → footer) using the new component set. Content
  (copy, stats, tags, links) is preserved from the current page — only
  presentation changes.
- JS: trim the boot-sequence script down to a single type-in + blink
  effect. Keep the scroll `fade-up` `IntersectionObserver` and the
  project-card click-through behavior — both work fine and aren't part of
  the terminal aesthetic being replaced.
- No build tooling introduced — stays plain HTML/CSS/JS, matching the
  rest of the site.

## Verification

Since this is a static site with no test suite, verification is manual:
open `index.html` in a browser (local server or file://) and check:
- Hero type-in/blink animation plays once correctly.
- Card hover states (glow/lift) on projects, skills, training cards.
- Project card click-through still navigates to `projects/*.html`.
- Responsive layout at mobile width (~375px) — nav collapse, single-column
  grids, stat row wrapping.
- No leftover references to removed classes (`term-window`, `term-dots`,
  etc.) in `index.html` after the rebuild.

## Explicitly out of scope

- Light mode / theme toggle.
- Redesigning project-page structure, diagrams, or narration UI (Phase 2,
  separate spec).
- Adding new content sections beyond what exists today.
- Any backend/build tooling.
