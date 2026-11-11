# Mustang Fold Rush: notes for Claude Code

Review-games web app for Little Chute High School Biology, Essential Standard 1.4: Protein Structure and Function [HS-LS1-1]. Built for 9th–10th graders, mostly on school Chromebooks. Owner: Mr. Rankin.

## How it's built

- The whole app is ONE file: `index.html` (HTML + CSS + vanilla JavaScript, no frameworks, no build step, no npm). Keep it that way.
- Hosted on Vercel from this GitHub repo. Every push to `main` redeploys automatically in about a minute.
- Other files: `favicon.svg`, `apple-touch-icon.png` (Mustang badge icons), `README.md`.
- Only outside resource: Google Fonts (Atkinson Hyperlegible for body, Bricolage Grotesque for display, Press Start 2P for the Dogma Invaders arcade look, JetBrains Mono).

## Finding your way around index.html

CSS is in the `<style>` block at the top; JavaScript is one IIFE in the `<script>` at the bottom. Sections are marked with banner comments, so search for these:

- CSS: `HOME v3` (home page), `DOGMA INVADERS`, `MY EVIDENCE`, `sticky game bar`, `Pin the Tail: animated blindfold intro`
- JS core: `SCHOOL LOGOS` (Mustang SVG symbol `#mustangArt`, tail mask), `CORE`, `genetic code`, `vocabulary`, `storage`, `registry, router, shell`, `drag + tap-to-place`, `label-the-diagram component`
- Level 1: `DON'T FRY THE EGG`, `CROSSWORD`, `ACROSTIC`, `DEFINE IT`
- Level 2: `TRANSCRIPTION (inside the nucleus)`, `TRANSLATION`, `PIN THE TAIL ON THE MUSTANG`, `TRANSCRIPTION or TRANSLATION sort`
- Level 3: `codon wheel` / `Codon Wheel Protein Builder`, `STRUCTURE CHECK`, `ENIGMA DECODER`, `DOGMA INVADERS`
- Home and evidence: `HOME + EVIDENCE`, `FOLD RUSH animated title`, `MY EVIDENCE: portfolio artifact`

Each game is registered with `reg({ id, level, icon, title, blurb, how, run(arena, g) { ... } })`. Game ids: egg, crossword, acrostic, define, transcribe, translate, pin, sort, builder, structure, enigma, dash (Dogma Invaders keeps the old id `dash` so saved scores carry over).

Navigation uses `go(id)` and buttons with `data-go="id"`, `data-home` or `data-restart`, not `href="#..."` links. Cleanup when leaving a game goes through `onLeave(fn)`.

Scores: `record(gameId, score, total)` saves the best percentage. Everything is saved in localStorage under the key `codonRodeo.v1` (name, games, reflection, Dogma Invaders settings). Never rename this key, or students lose their progress.

My Evidence draws the downloadable PNG/PDF itself on a canvas (`evidenceCanvas`) and builds the PDF by hand (`pdfFromJpegs`). There are no PDF libraries. If you add a game, it appears in the evidence table automatically from `GAMES`.

## Mr. Rankin's design rules (please follow)

- Proficiency wording: Level 1 starts with "I can…"; Levels 2 and 3 start with "Additionally, I can…".
- No emoji anywhere in the app (they render differently across devices). Use inline SVG icons instead.
- Make everything interactive and animated. Visuals over text-heavy screens.
- Students scan instead of reading, so buttons and labels must be obvious on their own. No jargon.
- Activities check themselves and play their success animation automatically. Don't add "Check" buttons.
- Practice questions: mix formats (drag, label, sort, match). For multiple choice, the correct answer must never be the longest option.
- School colors: Carolina blue `#4B9CD3`, deep Carolina `#24679F`, navy `#13294B`, sky blue, white, with gold `#F2C744` accents. The mascot is Marty the Mustang.
- Keep light and dark mode working (color tokens on `:root`, dark overrides under `prefers-color-scheme` and `[data-theme="dark"]`).
- Must work at phone width (390px) with no sideways scrolling, and on Chromebook touchscreens (tap as well as drag).
- The top game bar stays pinned so the Menu button is always reachable. Keep it that way in any new screen.

## Before you push

1. Open `index.html` in a browser (`open index.html` on Mac) and click through whatever you changed, on a narrow window too.
2. Check the browser console for errors.
3. Commit with a clear message and push to `main`. Vercel handles the rest.
