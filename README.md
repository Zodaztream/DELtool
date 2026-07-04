# Duizend — jouw eerste 1000 woorden Nederlands

A tiny offline-first Dutch learning app built for micro-sessions (5–10 min/day).
Spaced repetition, typo-tolerant sentence typing, Dutch audio, Swedish cognate
boosting, and a canal-house streak tracker.

## Deploy to GitHub Pages (3 steps)

1. **Create a repo** on github.com (e.g. `duizend`), public.
2. **Upload these files** (drag & drop on the repo page → "uploading an existing file"):
   `index.html`, `data.js`, `manifest.json`, `sw.js`, `icon-180.png`, `icon-512.png`
3. **Enable Pages**: repo → Settings → Pages → Source: "Deploy from a branch" →
   Branch: `main`, folder `/ (root)` → Save.

After ~1 minute your app is live at `https://<your-username>.github.io/duizend/`

## Install on iPhone

Open the URL in **Safari** → tap the Share button → **Add to Home Screen**.
It launches full-screen like a native app and works offline after the first visit.

## How it works

- **Session** = due reviews + up to 5 new words (configurable: 3/5/8), capped at ~7 min.
- **New/young words**: flashcards (recognition). **Mature words**: you type the Dutch
  sentence from the English prompt — grading is fuzzy, so word order and small typos
  aren't punished. Grammar perfection is not the goal; communication is.
- **≈ Zweeds** tag = Swedish cognate. These get longer initial intervals since
  they're nearly free for you.
- **Streak** = one canal house per day on the home screen.
- **Audio**: tap 🔊 (uses the iPhone's built-in Dutch voice, offline).

## Your data

Progress lives in your browser's localStorage on the device. Use **Exporteer**
regularly to back up, and **Importeer** to restore or move to another device.
Note: if you delete Safari website data, progress is wiped — keep a backup.

## Editing the word list

`data.js` is plain text. Each line is one item:

```
{i:301, w:"dutch word", s:"Dutch sentence.", e:"english gloss",
 v:"swedish gloss", x:"English sentence.", t:"core|life|fam", c:0 or 1},
```

Add lines before the final `];` with unique `i` numbers. Your boyfriend is the
naturalness QA department. After editing, bump the cache name in `sw.js`
(`duizend-v1` → `duizend-v2`) so phones pick up the change.
