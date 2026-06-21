# Ojibwe (Red Lake / Anishinaabemowin) locale — design

**Date:** 2026-06-20
**Status:** Approved, pending build. Site copy is **pending a Red Lake elder review** (same discipline as the Vietnamese native-review hold, except the reviewer is a fluent Red Lake speaker, not AI).
**Locale code:** `oj`  ·  **Switcher native name:** Anishinaabemowin  ·  **Short label:** OJ

## Why this is different from `es` / `vi`

Spanish and Vietnamese were added as **full** AI translations (VI flagged pending native review). Ojibwe is **not** done that way, on purpose:

- AI translation quality for Ojibwe is far below ES/VI — little training data, polysynthetic/animacy-based grammar, and a specialized Red Lake (Ponemah/Obaashiing) dialect. A machine draft would likely be wrong-to-invented.
- This is Mike's **heritage language** (Anishinaabe, Red Lake Nation descendant, raised in Obaashiing/Ponemah and Redby). The bar for correctness is higher here than for any other locale.

**Decision:** a **curated, verified subset**. `oj` is a first-class locale (full `/oj/` routes, switcher, hreflang), but only a small set of high-value, low-risk strings are in Ojibwe. Everything else **intentionally falls back to English** via the existing `useTranslations` fallback (`ui[lang][key] ?? ui[defaultLang][key]`). Long-form Work/Writing pages under `/oj/` render the English content (no `oj` markdown by design).

## Source of truth

All Ojibwe strings were sourced **verbatim** from the [Ojibwe People's Dictionary](https://ojibwe.lib.umn.edu) (UMN; Minnesota / Southwestern Ojibwe), then independently re-verified against a second lookup. Region legend confirmed from the dictionary's own [Key to Regions](https://ojibwe.lib.umn.edu/help/regions): **RL = "Red Lake (Ponemah)"**. Many of the chosen words carry speaker audio from **Eugene Stillday** and **Rose Tainter**, both Ponemah / Red Lake elders — i.e. attested in Mike's own dialect's voice.

Orthography: standard **double-vowel (Fiero)** Roman system, the convention for Minnesota / Red Lake Ojibwe. Every shipped string is basic Latin letters + a straight apostrophe, so it renders in the site's existing Inter Latin subset — **no font work required** (unlike Vietnamese, which is currently rendering via OS-fallback fonts on the live site — separate known issue).

## Shipped strings (verified, safe)

| Where it appears | `ui` key(s) | Ojibwe | Meaning (dictionary, verbatim) | Source |
|---|---|---|---|---|
| Homepage hero eyebrow + identity statements | `home.hero.kicker`, leads `home.why.statement` & `about.story.p1` | **boozhoo** | "hello!; greetings!" (pc interj) | [entry](https://ojibwe.lib.umn.edu/main-entry/boozhoo-pc-interj) — audio: Stillday (RL), Tainter (RL) |
| Language switcher native name | `languages.oj` | **Anishinaabemowin** | "Ojibwe language; an American Indian language" (ni) | [entry](https://ojibwe.lib.umn.edu/main-entry/anishinaabemowin-ni) — audio: Stillday (RL), Jones |
| Nav: Work | `nav.work` | **Anokiiwin** | "work, a job" (ni) | [entry](https://ojibwe.lib.umn.edu/main-entry/anokiiwin-ni) — audio: Stillday (RL) |
| Nav: Writing | `nav.writing` | **Ozhibii'igan** | "a piece of writing (a paper, essay, report); mail" (ni) | [entry](https://ojibwe.lib.umn.edu/main-entry/ozhibii-igan-ni) — audio: Stillday (RL) |
| Footer sign-off | `footer.signoff` (new key) | **Miigwech** | "thanks!" (pc disc) | [entry](https://ojibwe.lib.umn.edu/main-entry/miigwech-pc-disc) — audio: Jones |
| Identity statements | within `home.why.statement`, `about.story.p1` | **Miskwaagamiiwi-zaaga'igan** | "Red Lake Nation" (name place) | [entry](https://ojibwe.lib.umn.edu/main-entry/miskwaagamiiwi-zaaga-igan-name-place-8b8a179c-9bfe-4024-b0f8-cdf36550a8d8) — audio: Stillday (Ponemah/RL) |
| `about.story.p1` (Ponemah) | within `about.story.p1` | **Obaashiing** | Ojibwe name for Ponemah (confirmed by Mike; dictionary attests it as the Ponemah place name) | dictionary speaker/region notes |

### The two new/edited English-with-Ojibwe statements

`home.why.statement` (oj):
> Boozhoo. I'm Anishinaabe, a descendant of Miskwaagamiiwi-zaaga'igan (Red Lake Nation), and I came to AI through people: who it helps, who it leaves out, and how organizations bring their communities along as work changes.

`about.story.p1` (oj):
> Boozhoo. I am Anishinaabe, a descendant of the Red Lake Nation, Miskwaagamiiwi-zaaga'igan. I grew up in Obaashiing (Ponemah) and Redby on the reservation before moving to the Twin Cities. That background is not a footnote to this work; it is the reason for it. I saw early how change arrives in a community: who it serves, who it leaves behind, and how a place carries its people through it. Growing up on the Red Lake Nation taught me that strong organizations start with relationships, integrity, and cultural awareness. That shapes how I think about every part of human resource development, from how we train people to how we build systems that actually include them.

## Deferred (NOT shipped) — for the elder to decide

| Wanted | Candidate | Why deferred |
|---|---|---|
| Nav: Research | *gikendaasowin* | Real word, but means "intelligence; knowledge," **not** research/learning. Wrong fit. Nav stays English. |
| Footer: goodbye | *giga-waabamin* | A genuine taught farewell ("I'll see you again") but an inflected verb phrase, not a dictionary headword. Not safe to assert as a label. |
| Welcome CTA | *biindigen* | Verified ("come in!/welcome", imperative) but the only dictionary audio is Border Lakes, **not** Red Lake; register caveat. Held in reserve. |
| Alt. greeting | *aaniin* | Verified indigenous-origin greeting; held in reserve (we lead with boozhoo, Mike's community's everyday greeting). |

## Elder review checklist (when Mike is next at Red Lake)

- Confirm **boozhoo** (French-origin loan, universal) is the desired hero greeting vs. **aaniin** (indigenous-origin).
- Confirm **Anokiiwin** / **Ozhibii'igan** read naturally as one-word nav labels (vs. verb forms anokii / ozhibii'ige).
- Confirm preferred spelling of **Miigwech** (some write *miigwetch*).
- Confirm **Miskwaagamiiwi-zaaga'igan** (the Nation) is the intended Red Lake referent (vs. *Ogaakaaning*, the town).
- Confirm **Obaashiing** spelling and that the parenthetical English glosses read respectfully.
- Decide whether to translate more (Research, a farewell, a welcome line) and whether to add a visible "Anishinaabemowin in progress" note.

## Technical shape (implemented by Codex)

- `src/i18n/ui.ts`: add `oj` to `languages` ('Anishinaabemowin'), `languageShortLabels` ('OJ'), `locales`; add a full `oj` block = copy of `en`, overriding only the curated keys above; add new `footer.signoff` key (en/vi/es = `''`, oj = `'Miigwech'`).
- `astro.config.mjs`: add `'oj'` to `i18n.locales`.
- `src/components/Footer.astro`: render `t('footer.signoff')` only when non-empty.
- `src/pages/oj/`: mirror `src/pages/es/`; static pages use `lang="oj"`; the four content-collection pages fall back to **English** entries (filter `localeFromEntryId(...).locale === 'en'`) so nothing 404s.
- `AGENTS.md`: document `oj` as a curated partial locale, pending Red Lake elder review, **do not machine-translate the rest**.
- No font changes, no new dependencies, no `oj` markdown content.
