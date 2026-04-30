# Project Context

## 1. Project Overview

**drew & friends** is a landing page and game-collection hub for a small indie browser game collective built by Drew and friends. It is the public-facing home for all games the group ships — currently featuring Billy Bouncer as the lead title, with space for future entries on the "shelf."

- **What it does:** Presents the collective's identity, spotlights the current featured game, and maintains an expanding collection of game cards linking out to individual game deployments.
- **Who it's for:** Players finding the games organically, friends sharing the link, and the collective itself as a living record of what they've shipped.
- **Current status:** V1 live — single page, one shipped game (Billy Bouncer), two coming-soon placeholder cards.
- **Main goals for this repo:**
  - Be the canonical home URL for the collective.
  - Make adding a new game entry as low-friction as possible (copy a card, fill it in).
  - Maintain a consistent visual identity across all future game entries.
  - Stay lightweight, fast, and dependency-free.

---

## 2. Tech Stack

| Layer | Choice |
|---|---|
| Languages | HTML5, CSS3, vanilla JavaScript (ES6+) |
| Frameworks | None — zero dependencies, zero build tools |
| Runtime | Browser-native; no Node.js required to run locally |
| Package manager | None |
| Database / storage | None — static site, no server-side data |
| Hosting / infra | GitHub (`onjamm/drew-n-friends`) — deploy target TBD (Vercel recommended for consistency with Billy Bouncer) |
| Testing | None currently — visual QA by opening `index.html` in a browser |
| Build step | None — `index.html` is the artifact |

---

## 3. Repo Map

```
drew-n-friends/
├── index.html          # The entire site — HTML, CSS, and JS in one file
├── assets/
│   ├── billy-bouncer-gameplay.png   # Screenshot used in featured section + shelf card
│   └── billy-idle.png               # Billy Bouncer pixel sprite (idle-south, 76×76px)
└── context.md          # This file
```

**Why a single file?** Matches the philosophy of Billy Bouncer itself (single `index.html`). Easy to deploy anywhere, easy to fork, zero configuration overhead. When the site grows enough to need components or a build step, that's the moment to revisit.

---

## 4. Architecture

**This is a static HTML file. There is no backend, no API, no state.**

- Request flow: user hits URL → browser downloads `index.html` + `assets/` → renders, done.
- All game links are external `<a>` tags pointing to individual game deployments (e.g., `https://billy-bouncer.vercel.app`).
- The collection ("shelf") is hard-coded HTML. Adding a game = adding a card in the markup.
- The footer year updates via a 2-line inline `<script>` — the only JavaScript on the page.
- No routing, no SPA, no hydration. One URL, one page.

**Visual architecture** (how the design system is structured inside the CSS):

```
CSS custom properties (:root)  ← single source of truth for color/spacing
  └── Layout utilities (.wrap, .ruled, .label)
        └── Section components (nav, .hero, .featured, .shelf, .about, footer)
              └── Sub-components (.game-card, .game-frame, .tag, .btn, etc.)
```

Design tokens live in `:root`. Touch those first when rebranding or adjusting the palette.

---

## 5. How to Run Things

**Local development — zero setup:**
```bash
# Option 1: just open the file
open drew-n-friends/index.html

# Option 2: local server (avoids any file:// quirks)
npx serve .
# or
python3 -m http.server 3000
```

**Deploy to Vercel:**
```bash
npx vercel --prod
# No configuration needed — Vercel serves index.html as the root automatically
```

**Deploy to GitHub Pages:**
- Push to `main` → Settings → Pages → Source: `main`, root `/`
- No `_config.yml` or anything else needed

**Add a new game card (the whole workflow):**
1. Drop a screenshot into `assets/`.
2. Copy the Billy Bouncer `<article class="game-card lead">` block in `index.html`.
3. Update: image src, title, description, link, status tag.
4. If it's not the lead game, remove the `lead` class so it uses the standard vertical card layout.
5. Push. Done.

---

## 6. Conventions

**HTML**
- Semantic elements throughout: `<nav>`, `<section>`, `<article>`, `<footer>`, `<hr>`, `<h1>`–`<h3>`.
- Every `<section>` has an `aria-label` or `aria-labelledby`.
- Decorative images get `alt=""` and `aria-hidden="true"`. Content images get descriptive alt text.
- `loading="lazy"` on below-fold images.

**CSS**
- All design decisions flow from `:root` tokens — **never hardcode a color value outside `:root`**.
- Class naming is flat BEM-lite: `.game-card`, `.card-body`, `.card-name`. No deep nesting.
- Layout via CSS Grid. No Flexbox hacks for two-dimensional layouts.
- Responsive breakpoints: `940px` (tablet collapse) and `600px` (mobile, hides margin line).
- Section structure order: token → reset → base → paper effects → layout helpers → nav → hero → buttons → featured → shelf → cards → about → footer → responsive.
- No `!important`. No inline styles except the one `font-size` override in the featured section (legitimate one-off, not a pattern).

**JavaScript**
- One `<script>` block at end of `<body>`. No external scripts. No modules.
- Currently only sets footer year. Keep it that way unless there's a real reason to add more.

**Typography**
- `--serif`: Georgia, Times New Roman — used for all headings, body, and buttons.
- `--mono`: Courier New — used exclusively for labels, tags, metadata, and UI chrome (nav, footer, stamps).
- Don't introduce web fonts unless the licensing and performance tradeoff is clearly worth it.

**Color palette** (the full token set):
```
--parchment:      #F4E4BC   ← page background
--parchment-warm: #EDD8A2   ← card backgrounds, shadow gap color
--cream:          #FAF3E0   ← live game card background
--ink:            #1C1208   ← primary text, borders, strong elements
--ink-mid:        #3D2B1A   ← body text, descriptions
--ink-light:      #6B4E35   ← secondary text, metadata, footer
--rust:           #B83020   ← accent, CTAs, live indicators, hover states
--gold:           #C9960A   ← tape decoration, brass accents
--brass:          #A07820   ← section labels, frame annotations
--burnt:          #D4622A   ← available if needed (Billy's poncho color)
--teal:           #3D7D7D   ← available if needed (enemy color in game)
```

**Copy tone**
- Short. Specific. Human. Lightly playful. Not corporate.
- Write like a person who made the thing, not like a marketer describing it.
- Bad: "Experience an immersive score-chasing adventure." Good: "See how high you can go before the floor forgets you exist."

---

## 7. Important Constraints

- **Do not introduce a build step** without team agreement. The whole point of this stack is that there's nothing to install or configure.
- **Do not add framework dependencies** (React, Vue, etc.) for what is fundamentally a static marketing page.
- **Do not use web fonts from Google Fonts or similar** without considering the privacy and performance tradeoff. Georgia covers the aesthetic.
- **Keep the visual identity consistent** with Billy Bouncer's palette (parchment, ink, rust, gold). New game entries should not bring in clashing colors or design languages.
- **The margin line and ruled paper texture are load-bearing design elements** — they establish the notebook aesthetic. Don't remove them to "simplify" the CSS.
- **No external analytics or tracking scripts** without explicit decision from the group.
- **The `assets/` folder is manually managed** — no automated pipelines or asset optimization. Keep files reasonably sized before committing.
- **Game card image aspect ratios matter** — the lead card uses `object-fit: cover` on a fixed height. Landscape screenshots (4:3 or 16:9) work best. Portrait screenshots need the `object-position` tuned.

---

## 8. Known Gotchas

- **The red margin line is `position: fixed`** — it overlays the page content. If you add any `z-index` to page elements and something appears under the line, the line's z-index is `8000`. The grain texture is `9000`. Stay below those or adjust both.
- **The paper grain texture is an inline SVG data URI** — this is intentional (no extra network request) but the URL-encoded SVG in `body::after` looks gnarly. Don't touch it unless the texture needs to change.
- **The `billy-idle.png` sprite is 76×76px raw pixel art**, displayed at 152px via `image-rendering: pixelated`. If you swap the sprite for a different resolution, update the display size or the pixel doubling breaks.
- **Coming-soon cards use `opacity: .68`** — if the dashed border feels too faint on certain screens/OS dark modes, this is the knob to turn.
- **The `game-frame` has `transform: rotate(-0.6deg)`** — remember this when adding anything that needs to align precisely to the frame (e.g., a play button overlay). You'll need to counter-rotate or work in the frame's local coordinate space.
- **Tape tab angles are set per `nth-child`** — if you add more shelf cards, the stagger pattern only covers `:nth-child(2)` and `:nth-child(3)`. Add more selectors or refactor to alternating-angle logic if the shelf grows significantly.
- **No Vercel config exists yet** — first deploy may need a `vercel.json` if custom headers (cache control, CSP) are required. The defaults work fine for a plain static site.
- **The `section-sep` `<hr>` elements are full-bleed** — they intentionally cross the red margin line. This is a deliberate design choice (the ruled line concept), not a layout bug.

---

## 9. Key Files

| File | Purpose |
|---|---|
| `index.html` | The entire site — everything is here |
| `assets/billy-bouncer-gameplay.png` | Primary visual for featured section and Billy's shelf card |
| `assets/billy-idle.png` | Billy sprite used in the hero (pixel art, 76×76px raw) |
| `context.md` | This file |

**Related external resources:**
- Billy Bouncer live: `https://billy-bouncer.vercel.app`
- Billy Bouncer repo: `C:\Users\azmio\personal-apps\billy-bouncer` (local) — see its `context.md` for game-specific details
- GitHub remote: `https://github.com/onjamm/drew-n-friends`

---

## 10. How to Help Best

- **When touching the CSS**, work with the token system. Change a color in `:root`, not in a component rule.
- **When adding a game**, copy the existing card markup — don't build a new card structure from scratch. The patterns are intentional.
- **Prefer minimal diffs.** This is a small, readable file. A focused 10-line edit beats a wholesale rewrite.
- **Don't redesign without cause.** The visual language is deliberate and tied to Billy Bouncer's aesthetic. "Cleaner" or "more modern" is not an improvement here.
- **Cite line numbers or class names** when pointing to something specific — the file is long enough that "in the shelf section" is ambiguous.
- **Test at three widths**: full desktop (1200px+), tablet (768px), and mobile (375px). The margin line hides at 600px and the shelf reflows — verify both.
- **If adding JavaScript**, ask whether it genuinely needs to be on the page or whether it belongs in a game's own repo. Keep the landing page dumb.
- **Ask before restructuring the file into multiple files.** The single-file approach is load-bearing for simplicity right now. That conversation is worth having out loud.
