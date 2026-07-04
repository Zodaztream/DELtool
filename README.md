# Duizend / Tusen / Mil — jouw eerste 1000 woorden

Offline-first language app, now generalized to support any number of
independent learning profiles on one device: **Duizend** teaches Dutch
(for the Swedish partner), **Tusen** teaches Swedish (for the Dutch
partner), **Mil** teaches Spanish (Spain, general-purpose, for English
speakers) — same app, same phone, switchable with one tap, fully
independent progress per profile.

## Update an existing deployment

Add two new files (`data-es.js`, `stories-es.js`) alongside the existing
ones, and replace `index.html` and `sw.js`. Cache bumps to `duizend-v7`
so phones auto-update. **Your existing Dutch and Swedish progress is
untouched** — each still lives under its own storage key; Spanish uses a
new, separate key.

## First deployment (3 steps)

1. Create a public repo on github.com (e.g. `duizend`).
2. Upload: `index.html`, `data.js`, `data-sv.js`, `data-es.js`,
   `stories.js`, `stories-sv.js`, `stories-es.js`, `manifest.json`,
   `sw.js`, `icon-180.png`, `icon-512.png`.
3. Settings → Pages → Deploy from branch `main`, folder `/ (root)`.
Then on iPhone: open the URL in Safari → Share → **Add to Home Screen**.

## N profiles, one device

The header now renders a horizontally-scrollable row of tabs generated
from a `PROFILES` config object — adding a fourth, fifth, etc. language
in the future means adding one config entry and two content files, not
touching the header markup or click-handling code. Each profile has its
own: word progress, streak, journal, and settings, stored under separate
localStorage keys so borrowing the phone never mixes progress.

**UI chrome language is now per-profile, not fixed.** Dutch and Swedish
keep Dutch chrome (native language for one partner, light immersion for
the other — unchanged from before). Spanish/Mil uses **English** chrome
instead, because its learner's native language is English and Dutch
button labels would just be friction, not immersion. This is driven by
a small `UI_STRINGS` dictionary (`nl` / `en`) and each profile's
`uiLocale` field — a future profile for, say, a German speaker learning
French could set `uiLocale: "nl"`, `"en"`, or a new locale added to the
dictionary, whichever actually helps that learner.

Two other things are now per-profile config rather than hardcoded:
- **Streak icon**: canal-house gables (NL), dala horse (SV), flamenco
  guitar body (ES) — set via `PROFILE.icons`.
- **Native-language gloss line**: Dutch/Swedish show it (their learners'
  native language differs from the English gloss already shown). Spanish
  hides it (`showNativeLine: false`) since the learner's native language
  *is* English — showing the English gloss twice would just be
  duplicate clutter.

## Content design decisions

- **Not a blind mirror.** Word frequency is language-specific — Spanish's
  real top-1000 isn't a translation of Dutch's or Swedish's. The Mil
  dataset is built from genuine Peninsular Spanish frequency and usage
  (vosotros, coche, tú — Spain conventions, not Latin American ones).
- **General-purpose, not couple-specific.** Unlike the Dutch/Swedish
  tracks (built around a specific Amsterdam relationship), Mil is for a
  general English-speaking learner: themes are daily life, travel, food,
  work, and socializing rather than a specific household or relationship.
  The AI-teacher prompt (`buildAiPrompt()`) was also genericized to drop
  the old relationship-specific framing, so it now works for any learner.
- **Genuine Spanish culture in the stories**, not reskinned Dutch/Swedish
  ones: siesta, tapas, flamenco in Sevilla, the Camino de Santiago,
  Nochevieja and the twelve grapes, Reyes Magos — the 20 Mil stories are
  Spain-first, unlocking at the same 50-word milestones as the other
  tracks.
- **Spanish articles are handled like Dutch de/het.** Citation forms like
  "la casa" or "el coche" needed the same treatment Dutch nouns already
  got: `stripArticle()` and the typing-mode `hintFor()` filler-word list
  were extended to strip `el/la/los/las`, so hints and Schrijven word-
  matching work correctly for Spanish nouns too. Checked for zero
  collision risk against existing Dutch/Swedish vocabulary first.
- **Cognates are symmetric within each language pair**, and Spanish gets
  its own honest cognate count against English (214 of 1000) rather than
  inheriting the Dutch/Swedish figures.

## The learning system (unchanged mechanics, research notes)

- SRS with learning steps (each new word seen twice before graduating);
  mature lapses halve the interval instead of resetting.
- 5/8/15/20 new words per day (research: diminishing returns above ~20).
- **Schrijven / Write**: app picks your 7 weakest learned words; write a
  mini-text using them; AI-check via API key (Settings) or copy-paste
  prompt into any AI app. Journal saved per profile.
- **Lezen / Read**: 20 graded stories per language, unlocking every 50
  words, built from vocabulary already learned. Tap any word for its
  gloss; audio at normal or slow speed.
- **Shadowing**: reveal → say it aloud → 🐢 replay slow to compare.

## Your data

Progress lives in localStorage, split by profile key. **Export** exports
the *currently active* profile only — export each profile separately for
full backups. **Import** restores into whichever profile is active when
you import, and warns (in that profile's own chrome language) if the
backup file belongs to a different profile.

## Editing content

- Words: `data.js` (Dutch) / `data-sv.js` (Swedish) / `data-es.js`
  (Spanish), unique `i` per file.
- Stories: `stories.js` / `stories-sv.js` / `stories-es.js`, `m` = unlock
  milestone.
- Adding a fourth language: add a new `data-xx.js` / `stories-xx.js` pair,
  register a new entry in the `PROFILES` object and `PROFILE_ORDER` array
  in `index.html` (storage key, brand, flags, `ttsLang`, `uiLocale`,
  `icons`, `showNativeLine`), add its `<script src>` tags, and list the
  two new files in `sw.js`'s `ASSETS`. No other code changes needed.
- After any edit, bump the cache name in `sw.js` (v7 → v8).
