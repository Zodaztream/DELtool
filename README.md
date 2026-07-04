# Duizend — jouw eerste 1000 woorden Nederlands

Offline-first Dutch learning app: spaced repetition, sentence typing, graded
reading, free writing with AI/partner correction, shadowing, Dutch audio,
Swedish cognate boosting, and a canal-house streak tracker.

## Update an existing deployment

Replace `index.html`, `stories.js` (new file), `data.js` and `sw.js` in your
repo. The cache name is bumped to `duizend-v4` so phones pick up the update
automatically. Your progress (localStorage) is untouched.

## First deployment (3 steps)

1. Create a public repo on github.com (e.g. `duizend`).
2. Upload: `index.html`, `data.js`, `stories.js`, `manifest.json`, `sw.js`,
   `icon-180.png`, `icon-512.png`.
3. Settings → Pages → Deploy from branch `main`, folder `/ (root)`.
Then on iPhone: open the URL in Safari → Share → **Add to Home Screen**.

## The learning system (research notes)

- **Sessions (SRS)**: due reviews + new words. New words now use *learning
  steps*: you see each one twice in its first session before it graduates —
  the biggest early-retention win known from the Anki/FSRS community.
  Lapses on mature words *halve* the interval instead of resetting it,
  avoiding "low-interval hell".
- **New words/day**: 5 / 8 / 15 / 20. Research (e.g. Nakata 2015) shows
  diminishing returns above ~20/session; at 20/day expect ~30-40 min of
  daily reviews at steady state, and the full 1000 in ~7 weeks.
- **Schrijven**: the app picks your 7 weakest learned words; you write a
  mini-text using them. Composition with target words tops the retention
  charts (Involvement Load Hypothesis — Hulstijn & Laufer 2001; Keating
  2008). Used words get an SRS boost; entries are saved to a journal your
  partner can correct. A weekly "tijd!" badge nudges you.
- **AI-controle**: optional. Add an Anthropic API key in Instellingen
  (stored only on your device) for one-tap correction, or use "Kopieer
  controle-prompt" to paste your text + a teacher prompt into any AI app.
  Your partner remains the final judge — that's the pedagogy, not a bug.
- **Lezen**: 20 graded mini-stories unlock at every 50 words started. Each
  is written (almost) entirely with vocabulary you've already met, plus a
  few glossed extras — comprehensible input, "i+1". Tap any word for its
  meaning; play audio at normal or slow speed for listening practice.
- **Shadowing**: after each card reveal, say the sentence aloud and replay
  it slowly to compare. Self-assessed by design — the articulation is the
  training.

## Your data

Progress lives in localStorage. **Exporteer** regularly; **Importeer**
restores. Clearing Safari website data wipes progress — keep backups.

## Editing content

- Words: `data.js`, one item per line, unique `i`, before the final `];`.
- Stories: `stories.js` — `m` is the unlock milestone (words started).
- After any edit, bump the cache name in `sw.js` (v4 → v5).
