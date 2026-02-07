# FADELE

A Wordle-like daily word game where previous guess feedback fades away, forcing you to rely on memory.

## Architecture

- **Single file**: `index.html` — all CSS, HTML, and JS inline
- **Word lists**: EN (~1713 answers + ~3690 valid) and ES (~500 answers + ~800 valid), stored as comma-separated strings, split at init
- **Epoch**: Jan 1, 2026 — daily word is derived from day offset + seeded PRNG (mulberry32)
- **localStorage keys** are language-suffixed: `fadele-state-en`, `fadele-stats-es`, etc.
- **Settings**: stored in `fadele-settings` as `{ language: 'en'|'es', difficulty: 'easy'|'hard' }`

## Game Modes

### Easy Mode (default)
- Rows 0 and 1 fade at guess 3; row N fades at guess N+2
- Fade transition: 2.5s CSS ease-out
- Keyboard colors persist as a memory aid (green/yellow/gray)

### Hard Mode
- **Every row fades 3 seconds after being revealed** (2s delay + 1s CSS transition), starting from guess 1
- **Keyboard gives NO color hints** — keys stay neutral gray regardless of guesses
- All previously submitted rows appear instantly faded on page restore
- Share text appends a fire emoji (🔥) to indicate hard mode

## Features

### Language (EN / ES)
- Toggled in settings modal (gear icon in header)
- Language change reloads the page (different word list = different daily word)
- Ñ key appears in keyboard middle row when Spanish is active (`body.lang-es`)
- Input regex: `/^[a-zA-ZñÑ]$/`
- All UI text translated via `I18N` object and `t(key)` helper
- Language badge (EN/ES) shown in header-left

### Challenge Mode
- URL param: `?challenge=BASE64WORD&lang=en|es`
- The `lang` param ensures the recipient plays in the correct language
- Challenge games don't affect daily stats

### i18n
- `I18N` object with `en` and `es` sub-objects for all user-facing strings
- `applyLanguage()` updates all DOM text including how-to-play modal
- How-to-play modal content is rebuilt dynamically per language

## Key Functions

| Function | Purpose |
|---|---|
| `initWordLists(lang)` | Loads answer + valid word sets for the given language |
| `applyFading(animate)` | Easy mode threshold-based fading |
| `handleSubmit()` | Core game loop — evaluate, reveal, fade, check win/lose |
| `updateKeyboard()` | Updates key colors (skipped in hard mode) |
| `revealRow(index, eval)` | Flip animation for a submitted row |
| `evaluateGuess(guess, sol)` | Two-pass evaluation (exact match first, then present) |
| `loadSettings()` / `saveSettings()` | Persist language + difficulty |
| `applyLanguage()` | Apply i18n strings to all DOM elements |
| `t(key)` | Returns translated string for current language |

## Conventions

- All word lists: 5-letter, uppercase internally, no accents, Ñ allowed in Spanish
- CSS custom properties in `:root` for theming (dark theme only)
- Animations: flip, bounce, shake, pop — all CSS keyframes
- Modals: `.modal-overlay.open` toggled via JS
