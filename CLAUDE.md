# Mustang Fold Rush: notes for Claude Code

Review-games web app for Little Chute High School Biology, Essential Standard 1.4: Protein Structure and Function [HS-LS1-1]. Built for 9th–10th graders, mostly on school Chromebooks. Owner: Mr. Rankin.

## How it's built

- The whole app is ONE file: `index.html` (HTML + CSS + vanilla JavaScript, no frameworks, no build step, no npm). Keep it that way.
- Hosted on Vercel from this GitHub repo. Every push to `main` redeploys automatically in about a minute.
- Other files: `favicon.svg`, `apple-touch-icon.png` (Mustang badge icons), `README.md`.
- Only outside resource: Google Fonts (Atkinson Hyperlegible for body, Bricolage Grotesque for display, Press Start 2P for the Dogma Invaders arcade look, JetBrains Mono, Lexend for the dyslexia-friendly setting).

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

Settings (the three-line menu button in every top bar, `data-settings`, `openSettings`) are saved separately under `mustangFoldRush.settings` so erasing scores never resets a student's accommodations: text size, light/dark, high contrast, Animations (Full/Calm), read aloud + speed, dyslexia-friendly text (Lexend), reading ruler, color-blind friendly. Settings also holds "Start over from the beginning" (with a confirm step), which erases `codonRodeo.v1` (name, scores, reflection, Invaders high score) but keeps these settings. The My Evidence "Reset my scores" button erases scores only. A small script in `<head>` applies them before the page paints. Search `SETTINGS MENU` (JS) and `SETTINGS (menu button)` (CSS).

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
- Label-the-diagram activities (`labelDiagram`): on screens 1080px and wider the label bank sits to the right of the diagram, and the diagram's height is capped so the whole activity fits on a Chromebook screen without scrolling. A leader line's dot (`px, py`) must never sit on the letter or part being labeled (touch its edge, or the backbone between bases), and the line itself must not cross other letters or beads. Zoom in on a screenshot to check.
- Leader targets that are small, wide, or moving get a `mark` on the zone (a bracket or an outline box drawn with the leaders) and the dot touches the mark: the bracket around both DNA strands, the box around one free RNA nucleotide (which holds still, `TX_LABELED`). Never leave a dot floating in empty space beside a part.
- Label: Transcription and Label: Translation open with a short animation (`intro` option of `labelDiagram`, `TX_INTRO` / `TL_INTRO`) that must end exactly on the frozen labeling picture (`TX_STATIC` / `RB_STATIC`). The intro and the finale share the same opening code (`txOpening`, `tlStart`, `tlCycle`). The intro's step captions describe the action without naming the parts students are about to label. Always keep the "Skip to the labels" and "Watch the start again" buttons.
- Label: Transcription has seven labels (no Uracil: students confused it with mRNA). When the labels are right, its finale carries on from the frozen picture instead of starting over ("Replay animation" plays the whole story), then `cheer(TX_PUNS)` pops up a silly horse-pun card with Marty. Keep the puns cheesy and kid-friendly.
- The top game bar stays pinned so the Menu button is always reachable. Keep it that way in any new screen. New top bars also get `${setBtn()}` so Settings is always reachable.

## Accessibility and smooth animation (please follow)

- Motion: never read the device's `prefers-reduced-motion`. Many school computers have it on, and following it froze half the animations (the cause of the "choppy" reports). Calm motion comes only from the Settings menu: use `.rm` in CSS and `reducedMotion()` in JS for any new animation.
- Color-blind friendly (`.cb` on `<html>`): anything that shows right/wrong or groups by color needs a non-color cue too (the check/X badges under `COLOR-BLIND FRIENDLY` in the CSS cover `.ok/.no/.hit/.miss` on chips, options, boxes, and cells). Use the `--good`, `--bad`, and base tokens (`--bA`...), not raw hex, so the color-blind palette applies.
- Read aloud: `sayBtn(text, label)` makes a speaker button (shown only when Read aloud is on). Empty text reads the button's parent. Game directions and multiple-choice questions already have one.
- Size new text in rem so the Text size setting scales it.
- Animate with `transform` and `opacity` only. Don't animate `left`, `top`, `width`, `height`, `box-shadow`, or `background-position` in anything that runs continuously, and don't add `backdrop-filter`.
- Pointer-move handlers (drag) should store the latest position and do the work once per `requestAnimationFrame`.
- JS animation loops: book the next frame first, use real elapsed time (not frame counts), and stop when off screen (`pauseOffscreen`).
- For an SVG scene redrawn every frame from a markup string, `morph(el, html)` patches only what changed. It helped the transcription and translation finales about 30%, but was slower for the protein-folding scenes (most attributes change every frame), so measure before switching.

## Before you push

1. Open `index.html` in a browser (`open index.html` on Mac) and click through whatever you changed, on a narrow window too.
2. Check the browser console for errors.
3. Commit with a clear message and push to `main`. Vercel handles the rest.
