# Wider layout, home-page dedup, and a dedicated Marked in Red section — design

**Date:** 2026-07-01
**Status:** Approved (design), pending implementation plan.
**Scope:** Three related changes to michaelvanhorn.me: (1) widen grid/gallery sections so the site stops reading as a narrow centered column on modern viewports, (2) remove content the home page currently repeats from About/Work/Open-source, (3) give Marked in Red its own elevated home-page section and detail page instead of the generic Work case-study treatment.

## Goal

An experienced HRD/people-ops/AI-enablement reviewer should be able to land on the home page, get the gist without re-reading the same claim twice, and find Marked in Red — Mike's Claude Corps Essay 1 anchor and most personally significant project — without it being buried as one card of six in a generic grid. The layout itself should use modern desktop viewport width instead of leaving large empty margins on both sides.

Non-goals: no new nav item, no changes to About/Research/Contact page content, no rewrite of the i18n system, no changes to the Marked in Red project itself (repo at `~/Downloads/05 - Dev Projects/Code/marked-in-red` — untouched; this spec only covers how the portfolio *links to and represents* it).

## Decisions locked during brainstorming

| Decision | Choice |
|---|---|
| Marked in Red prominence | Dedicated home-page section (separate from "Selected work") + elevated detail page. No 5th nav item — still reachable via Work and still listed in the Work index grid for completeness. |
| Wide layout max-width | `--maxw-wide` 980px → **1280px**. Applies to hero, grid/gallery sections, the new Marked in Red section. |
| Prose max-width | Unchanged at 720px (`--maxw-prose`) — About story, Research body, Work detail body, Contact. Long text lines get harder to read past ~75 characters; widening these would hurt readability. |
| Home page section order | Hero → Featured Research → Selected Work (3 cards) → Marked in Red → Writing → Recommendation → Contact band |
| Home page cuts | "Why this work" folds into Hero (no separate section). "Experience" section removed (About owns the full timeline); current roles become a one-line strip near the hero instead of a full section. "Open Source" grid removed from Home, stays on the Work index only. |
| Marked in Red visual treatment | **Subtle brand nod**: paper background (site's Editorial Warm palette, unchanged), 4px left-border accent in Marked in Red's own red (`#b70011`), small red kicker/badge, standard ink/Inter heading + body, primary CTA button in `#b70011` ("View live demo"), outline secondary ("View source"), map preview thumbnail with red-tinted border. Same language carries into the detail page. |

Three width options (980/1120/1280) and three MiR visual-identity options (fully absorbed / subtle nod / full-bleed dark) were shown via the brainstorming visual companion; 1280px and "subtle nod" were the picks.

## Layout system

**File:** `src/styles/tokens.css:14`

```css
--maxw-wide: 1280px;  /* was 980px */
```

`--maxw-prose` (`tokens.css:13`, 720px) is unchanged. No other token changes.

**Sections affected by the width bump** (all currently use the `.wide` class per `src/styles/global.css:85-93`, which does `width: min(100% - 2rem, var(--maxw-wide))`):
- Home hero, Selected Work, the new Marked in Red section, Recommendation, Open Source (Work index only after this change)
- Work index intro + projects grid
- Any other section currently on `.wide` (About intro, etc.) — inherits the same bump; no section-specific override needed since `.wide` already is the "gallery-width" class, `.prose` is the "reading-width" class, and this change only edits `.wide`'s underlying token.

**Selected Work grid column count:** Home's Selected Work grid drops from 4 projects (2×2) to 3 (Waaban, Woodhouse, Obsidian AI) once Marked in Red moves to its own section. At 1280px, lay these out as a single 3-across row rather than keeping a 2-column grid with a gap — reads as intentional, not sparse. (`src/components/pages/HomePage.astro:74-99`; exact grid CSS to be located and adjusted during implementation.)

## Home page restructure

**File:** `src/components/pages/HomePage.astro`

1. **Hero** (currently `:23-51`) — absorb the identity statement. Add the existing `home.why.statement` copy (`src/i18n/ui.ts:56`, already translated en/vi/es) as an additional paragraph inside the hero, after the lead and before the action buttons. Add a new one-line "current roles" strip (new i18n keys, e.g. `home.hero.currentRoles` — "Currently: Meet Minneapolis · Waaban") near the hero actions, so "what does he do right now" doesn't disappear from the home page once the standalone Experience section is cut.
2. **"Why this work" section** (currently `:53-58`) — **deleted**. Its content now lives in the Hero (see above). Remove the section's own kicker/heading; that copy was section-framing only, not reused elsewhere.
3. **Featured Research** (currently `:60-72`) — unchanged. This is a genuine teaser → `/research` full page, not a duplicate.
4. **Selected Work** (currently `:74-99`) — remove Marked in Red from the grid (3 cards remain: Waaban, Woodhouse, Obsidian AI). Layout becomes a 3-across row at the new 1280px width (see above).
5. **Marked in Red section — new.** Inserted immediately after Selected Work. See below.
6. **Writing** (currently `:101-109`) — unchanged, shifts down in source order.
7. **Experience** (currently `:111-126`) — **deleted**. About (`src/components/pages/AboutPage.astro:95-106`) already has the full 4-role timeline; duplicating a 2-role subset on Home added nothing beyond "what's current," which now lives in the Hero strip (see #1).
8. **Recommendation** (currently `:128-147`) — unchanged, shifts down.
9. **Open Source** (currently `:149`, `<OpenSourceSection lang={lang} />`) — **removed from Home.** Stays on the Work index (`src/components/WorkIndex.astro:36`), which is now the single place it renders. No changes to the `OpenSourceSection.astro` component itself.
10. **Contact band** (currently `:151`) — unchanged.

## Marked in Red section (new, home page)

**New file:** `src/components/MarkedInRedSection.astro`, rendered from `HomePage.astro` right after Selected Work, using the same section-shell pattern as `OpenSourceSection.astro` (`<section class="home-section card-section"><div class="wide section-inner">`) so it inherits standard spacing/width for free, then layers the accent styling on top.

**Visual (Option B — subtle brand nod):**
- Background stays `--paper` (no full-bleed, no dark band) — consistent with every other home section.
- `border-left: 4px solid #b70011` on the section's inner card/container.
- Small kicker/badge in `#b70011`: "MMIWG2S awareness map" (new i18n key, e.g. `home.markedInRed.kicker`).
- Heading "Marked in Red" — standard ink/Inter, same heading scale as sibling section titles.
- Body copy — pull from `src/content/work/marked-in-red.md` frontmatter `summary` (existing content, no new authoring).
- Two CTAs: primary "View live demo" in `#b70011` (not the site's clay `#B5532A`), secondary outline "View source" in ink. Reuse whatever label keys `WorkDetail.astro` already uses for these two links (confirm exact key name during implementation — do not invent a new translated string for labels that already exist).
- Map preview thumbnail (static image or screenshot, red-tinted border) — asset to be produced during implementation; not a live embed (keeps the portfolio build free of the marked-in-red app's own dependencies).

**Data source:** same `src/content/work/marked-in-red.md` entry used everywhere else (frontmatter: title, summary, status, demo/source links). No duplicate copy authored — this section is a different *renderer* of the same content, same principle as how `OpenSourceSection` and the Selected Work grid both read from single sources today.

## Marked in Red detail page

**Route:** `/work/marked-in-red`, currently served by `src/pages/work/[...slug].astro` → generic `src/components/WorkDetail.astro:23-70` (same template as Waaban, Woodhouse, Obsidian AI, m365-bridge, FIM).

**Change:** add a **new component** `src/components/MarkedInRedDetail.astro` carrying the same red-accent/badge visual language as the home section, with room for a larger map preview. In `src/pages/work/[...slug].astro`, branch on slug: `marked-in-red` renders `MarkedInRedDetail.astro`; every other slug renders the existing generic `WorkDetail.astro` unchanged. Keeping this as a separate small component (rather than adding conditional branches inside `WorkDetail.astro`) keeps the generic template generic — it stays simple for the 5 case studies that still share it, and the one-off styling doesn't leak into a file five other pages depend on.

**Work index card:** Marked in Red's card in the Work index grid (`src/components/WorkIndex.astro:38-53`) stays the **plain generic `ProjectCard`**, same as its five siblings there — the Work index is deliberately the uniform "full catalog" view; the elevated treatment is specific to its home-page section and its own detail page. (Flagged as an open item below in case Mike wants the red accent to show up there too.)

## Content ownership / dedup summary

So nothing silently disappears in the trim:

| Content | Before | After |
|---|---|---|
| Identity statement (Anishinaabe/Red Lake, short form) | Standalone "Why this work" home section | Folded into Hero, same translated copy reused verbatim |
| Identity statement (full form) | About "story" section | Unchanged — this was always the intentional full version |
| Current roles (Meet Minneapolis, Waaban) | Standalone "Experience" home section (2 of 4 roles) | One-line strip near the Hero |
| Full career timeline (4 roles) | About "Experience" section | Unchanged |
| Open Source repo grid | Rendered on both Home and Work index | Renders on Work index only |
| Marked in Red summary copy | `content/work/marked-in-red.md`, read by home ProjectCard + Work index ProjectCard + Open Source seed fallback | Same source file, now also read by the new `MarkedInRedSection` + `MarkedInRedDetail` |

## i18n

New keys needed in `src/i18n/ui.ts` for **en/vi/es** (Ojibwe `oj` intentionally falls back to English for new keys, consistent with the existing curated-Ojibwe discipline documented for the Open Source section):

- `home.hero.currentRoles` — current-roles strip copy
- `home.markedInRed.kicker` — "MMIWG2S awareness map" (or equivalent)
- Possibly `home.markedInRed.title` if the section heading isn't just the literal project name

No new translation needed for:
- `home.why.statement` — reused verbatim in its new location, already translated
- The CTA button labels — reusing the existing view-demo/view-source keys from `WorkDetail.astro`'s translation set

Keys to **remove** (no longer referenced) once confirmed unused elsewhere: `home.why.kicker` / `home.why.title` (the standalone section's own heading strings), `home.experience.*` (if home used section-specific keys distinct from About's `about.experience.*`).

## Out of scope (YAGNI)

- Live map embed in the home section or detail page (static preview image only)
- Any change to the Marked in Red app itself
- A 5th nav item
- Touching About, Research, or Contact page content
- Re-authoring any project's summary/body copy — only *where and how* Marked in Red's existing content renders changes

## Open items

- Should the Work index card for Marked in Red also get a light red accent, or stay fully uniform with its five siblings? Current default: stays uniform.
- Exact wording for `home.hero.currentRoles` and the map-preview asset (screenshot vs. custom static graphic) to be finalized during implementation.
- Confirm the exact existing i18n key names for the demo/source CTA labels before reusing them in the new components.
