# Duizend / Tusen / Mil / Mille — jouw eerste 1000 woorden

Offline-first language app supporting any number of independent learning
profiles on one device: **Duizend** teaches Dutch (for the Swedish
partner), **Tusen** teaches Swedish (for the Dutch partner), **Mil**
teaches Spanish (Spain, general-purpose, English speakers), **Mille**
teaches French (France, general-purpose, English speakers) — same app,
same phone, switchable with one tap, fully independent progress per
profile.

## Update an existing deployment

Add two new files (`data-fr.js`, `stories-fr.js`) alongside the existing
ones, and replace `index.html` and `sw.js`. Cache bumps to `duizend-v8`
so phones auto-update. **All existing progress (Dutch, Swedish, Spanish)
is untouched** — each lives under its own storage key; French uses a new,
separate key.

## First deployment (3 steps)

1. Create a public repo on github.com (e.g. `duizend`).
2. Upload: `index.html`, `data.js`, `data-sv.js`, `data-es.js`,
   `data-fr.js`, `stories.js`, `stories-sv.js`, `stories-es.js`,
   `stories-fr.js`, `manifest.json`, `sw.js`, `icon-180.png`,
   `icon-512.png`.
3. Settings → Pages → Deploy from branch `main`, folder `/ (root)`.
Then on iPhone: open the URL in Safari → Share → **Add to Home Screen**.

## N profiles, one device

The header renders a horizontally-scrollable row of tabs generated from
a `PROFILES` config object — adding a fifth language means adding one
config entry and two content files, not touching the header markup or
click-handling code. Each profile has its own: word progress, streak,
journal, and settings, stored under separate localStorage keys so
borrowing the phone never mixes progress.

**UI chrome language is per-profile.** Dutch and Swedish keep Dutch
chrome; Spanish and French both use English chrome, since both learners'
native language is English and Dutch button labels would just be
friction, not immersion. Driven by a small `UI_STRINGS` dictionary
(`nl` / `en`) and each profile's `uiLocale` field.

Per-profile config also covers:
- **Streak icon**: canal-house gables (NL), dala horse (SV), flamenco
  guitar body (ES), Eiffel Tower (FR) — set via `PROFILE.icons`.
- **Native-language gloss line**: shown for Dutch/Swedish (native
  language differs from the English gloss already shown), hidden for
  Spanish/French (`showNativeLine: false`) since the learner's native
  language *is* English — showing the English gloss twice would just be
  duplicate clutter.

## Content design decisions

- **Not a blind mirror.** Word frequency is language-specific — French's
  real top-1000 isn't a translation of Dutch's, Swedish's, or Spanish's.
  The Mille dataset is built from genuine spoken-French frequency and
  usage (tu/vous, standard France conventions).
- **General-purpose, not couple-specific**, matching the Mil (Spanish)
  pattern rather than the original Amsterdam-relationship framing: themes
  are daily life, travel, food, work, and socializing for a general
  English-speaking learner.
- **Genuine French culture in the stories**: café terrace culture,
  crêpes, the Camino de Compostelle, Bastille Day imagery woven into the
  vocabulary, bistrot culture, château day-trips — the 20 Mille stories
  are France-first, not reskinned versions of the other tracks, unlocking
  at the same 50-word milestones.
- **French articles and elision are handled like Dutch de/het and
  Spanish el/la.** Citation forms like "la maison", "le vélo", and
  especially the elided "l'eau" / "l'ami" needed care: `stripArticle()`
  and `hintFor()` now strip `le/la/les/un/une/des` and the apostrophe-
  attached `l'` form (which has no space, so it's handled as a special
  case rather than a simple filler-word list entry), so hints and
  Schrijven word-matching work correctly for French nouns too. Checked
  for collisions against all three existing languages first — the only
  overlap found was a minor hint-quality *improvement* for one existing
  Spanish entry, not a regression.
- **Cognates are honestly counted per language**, not inherited: French
  gets 270 of 1000 (Norman-French influence on English makes French the
  highest cognate rate of the four tracks so far), separate from Dutch,
  Swedish, and Spanish's own counts.

## The learning system (research notes)

- **FSRS-6 scheduler** (upgraded from SM-2-style): the modern
  spaced-repetition algorithm with default parameters trained on ~700M
  real reviews, as adopted by Anki. Benchmarks show ~20-30% fewer
  reviews for the same retention. Existing progress migrates
  automatically and losslessly (stability := old interval, difficulty
  derived from ease factor). A **target retention** setting (85/90/95%)
  controls the workload/retention trade-off.
- Learning steps preserved: each new word is seen twice before
  graduating; lapses reschedule via the FSRS forget-stability formula.
- **Four exercise types**, interleaved automatically (research:
  interleaving beats blocking): flashcards, type-the-sentence,
  **dictation** (hear → transcribe; aural vocabulary knowledge predicts
  ~52% of listening-comprehension variance), and **cloze** (fill the
  blanked word in its sentence). Each is also selectable as a fixed
  mode in Settings.
- **Spreken / Speak**: pronunciation practice on your 8 soonest-due
  sentences using the browser's speech recognition (meta-analyses show
  ASR practice is moderately effective with a large effect on segmental
  pronunciation). Falls back to self-judged shadowing where speech
  recognition isn't available. Correct sentences count as retrieval and
  feed the scheduler.
- **Sprint**: 60-second fluency rounds on well-known words only —
  Nation's "fluency development" strand (the most neglected of his four
  strands): no new language, just speed pressure to build automaticity.
  Tracks your personal best; deliberately does not touch the SRS.
- Mature lapses reschedule rather than reset.
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
  (Spanish) / `data-fr.js` (French), unique `i` per file.
- Stories: `stories.js` / `stories-sv.js` / `stories-es.js` /
  `stories-fr.js`, `m` = unlock milestone.
- Adding a fifth language: add a new `data-xx.js` / `stories-xx.js` pair,
  register a new entry in the `PROFILES` object and `PROFILE_ORDER` array
  in `index.html` (storage key, brand, flags, `ttsLang`, `uiLocale`,
  `icons`, `showNativeLine`), add its `<script src>` tags, and list the
  two new files in `sw.js`'s `ASSETS`. If the new language uses elided
  or unusual articles, check `stripArticle()` / `hintFor()` for whether
  they need extending, and check for collisions against existing
  vocabulary first — same pattern used for Spanish and French. No other
  code changes needed.
- After any edit, bump the cache name in `sw.js` (v10 → v11).
