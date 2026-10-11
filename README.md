# PomoWrite

A responsive, local-first web app for practising academic writing in English. Built with vanilla HTML, CSS, and JavaScript — no build step, framework, database, or API keys required.

## Run locally

Requirements: Python 3 (or any static file server).

```bash
cd pomowrite
npm start
```

Then open http://localhost:4173 in your browser. Alternatively, run `python3 -m http.server 4173` from this directory.

## Features

- Three writing stages: Brainstorming, Drafting, and Polishing.
- Brainstorming topic, removable idea chips, and a four-part essay outline.
- Draft editor, live word/character count, estimated reading time, copy action, B2–C1 vocabulary examples, and insertable linking phrases.
- Polishing checklist and rule-based feedback (word count, paragraph structure, linking phrases, punctuation, outline, and checklist status). This is not AI feedback or a full grammar checker.
- Pomodoro timer: 25-minute focus and 5-minute break, pause/resume, reset, and mode switch.
- Editable profile, daily word goal, current/best streak, weekly activity, completed stages, and focus session counts.
- Browser `localStorage` persistence; no Supabase, AI API, server-side account, or cloud sync.
- Responsive layout for desktop and mobile.

## Data and limitations

All saved state lives in the current browser on the current device. Clearing browser site data or using the reset button removes it. The daily word goal is stored in the profile; the draft progress bar currently uses a 500-word reference scale. Vocabulary and review feedback are local static/rule-based features.
