# Duizend / Tusen — jouw eerste 1000 woorden

Offline-first language app for a bilingual couple: **Duizend** teaches Dutch
(for the Swedish partner), **Tusen** teaches Swedish (for the Dutch partner)
— same app, same device, switchable with one tap, fully independent progress.

## Update an existing deployment

Add two new files (`data-sv.js`, `stories-sv.js`) alongside the existing
ones, and replace `index.html` and `sw.js`. Cache bumps to `duizend-v5` so
phones auto-update. **Your existing Dutch progress is untouched** — it still
lives under its original storage key; the Swedish track uses a separate key.

## First deployment (3 steps)

1. Create a public repo on github.com (e.g. `duizend`).
2. Upload: `index.html`, `data.js`, `data-sv.js`, `stories.js`,
   `stories-sv.js`, `manifest.json`, `sw.js`, `icon-180.png`, `icon-512.png`.
3. Settings → Pages → Deploy from branch `main`, folder `/ (root)`.
Then on iPhone: open the URL in Safari → Share → **Add to Home Screen**.

## Two profiles, one device

Tabs in the header (🇳🇱 Nederlands / 🇸🇪 Zweeds) switch the active learner.
Each profile has its own: word progress, streak, journal, and settings —
stored under separate keys so borrowing the phone never mixes progress.
The UI chrome stays in Dutch for both (native language for the Dutch
partner, light immersion for the Swedish-learning partner).

## Content design decisions

- **Not a blind mirror.** Word frequency is language-specific — Swedish's
  real top-1000 isn't the direct translation of Dutch's. The Swedish track
  keeps the same curated real-life themes (relationship, Amsterdam, family)
  but each entry was written/checked for natural Swedish, not templated.
- **Genuine Swedish culture in the stories**, not reskinned Dutch ones:
  fika, midsommar, Lucia, kanelbulle, dalahäst — the 20 Tusen stories are
  Sweden-first, unlocking at the same 50-word milestones as Duizend.
- **Cognates are symmetric.** A Dutch–Swedish cognate flagged in one track
  is flagged in the other; both tracks get the scheduling boost.

## The learning system (unchanged mechanics, research notes)

- SRS with learning steps (each new word seen twice before graduating);
  mature lapses halve the interval instead of resetting.
- 5/8/15/20 new words per day (research: diminishing returns above ~20).
- **Schrijven**: app picks your 7 weakest learned words; write a mini-text
  using them; AI-check via API key (Instellingen) or copy-paste prompt;
  partner gives final correction. Journal saved per profile.
- **Lezen**: 20 graded stories per language, unlocking every 50 words,
  built from vocabulary already learned. Tap any word for its gloss;
  audio at normal or slow speed.
- **Shadowing**: reveal → say it aloud → 🐢 replay slow to compare.

## Your data

Progress lives in localStorage, split by profile key. **Exporteer**
exports the *currently active* profile only — export each profile
separately if you want full backups of both. **Importeer** restores into
whichever profile is active when you import.

## Editing content

- Words: `data.js` (Dutch) / `data-sv.js` (Swedish), unique `i` per file.
- Stories: `stories.js` (Dutch) / `stories-sv.js` (Swedish), `m` = unlock
  milestone.
- After any edit, bump the cache name in `sw.js` (v5 → v6).
