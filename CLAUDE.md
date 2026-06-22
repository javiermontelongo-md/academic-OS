# Academic OS — Claude Code Rules

## Repo
`javiermontelongo-md/academic-OS` — public GitHub
Single `index.html` + `data.json`. No backend, no build step, no frameworks.

## Stack
Vanilla HTML/CSS/JS · Anthropic API · GitHub API

## Canonical State Rule
The ONLY canonical file is `index.html` on the `main` branch
of the GitHub remote. Local changes that are not pushed do not
exist from the user's perspective. Never report a feature as
"done" without confirming it is pushed to origin/main.

## Push Rule — MANDATORY
After EVERY commit, immediately run:
  git pull --rebase && git push

Confirm push succeeded before reporting any change as complete.
After pushing, always state:
  - The commit hash
  - Output of: git log --oneline origin/main -3
  - Output of: git status (must show "nothing to commit, working tree clean")

Never leave commits unpushed. Never use git push --force
unless the user explicitly instructs it after reviewing
what will be overwritten.

## Before Starting Any Session
Run these before touching any file:
  git pull --rebase
  git status
  git log --oneline -5

This ensures you are working from the current canonical state.

## Coding Rules
- Only ever edit `index.html`
- Never touch `data.json` directly
- No `!important` anywhere in CSS
- No dead code — audit before deleting any function
- Nav is `gT(name, el)` (desktop tab bar) and `gM(name, el)` (mobile bottom nav + More tray); both just toggle `panel-<name>`

## Zero Dead Code Policy
Before deleting any function, confirm it has zero call sites.
Before adding any function, confirm it is called from at least one place.

## Verification After Every Change
After every edit, run grep checks to confirm:
- New functions exist at expected hit counts
- Deleted code has 0 hits
- No orphaned references remain

## Canonical Verification Rule
Never use local file grep to verify features.
Always verify against the remote canonical file directly.

  git show origin/main:index.html | grep -c "search-term"

Never run: grep -c "string" index.html
Always run: git show origin/main:index.html | grep -c "string"

## Commit Messages
Use conventional commit format:
  feat: description
  fix: description
  chore: description
  refactor: description

---

## Current Tabs
Tab-bar order (`panel-<name>`): TODAY · SCHEDULE · UWORLD · READING · ROTATION · PROCEDURES · NARRATIVE · INTERVIEW · APPLICATION · MILESTONES · ROADMAP · ANKI · CURRICULUM · MIND · TASKS · SETTINGS
Mobile bottom nav pins: TODAY · SCHEDULE · UWORLD · MIND · TASKS (rest live in the More tray).
(ABX/CLINIC/DATA panels were removed — moved to the internguide app.)

## Tab Roles
- **TODAY**: Daily mode selector (NORMAL/CALL/POST-CALL/RECOVERY/REST) + energy/sleep/motivation sliders + burnout risk indicator + AI coach output. Also shows a current-rotation banner + schedule-aware study plan (`buildTodayRotation`).
- **SCHEDULE**: Full PGY-1 block calendar from the `SCHED` array — current-block banner, monthly calendar view, schedule-aware study plan. Canonical source of "what rotation am I on."
- **UWORLD**: Question bank session logger (date, questions, score %, topic, mode, notes) + domain performance breakdown + streak counter + progress toward 3,500 Q target
- **READING**: Pre-built reading list with 5 foundational articles, mark-read tracking, PubMed links
- **ROTATION**: Schedule-driven hub (`buildRotations`). Auto-detects the current block from `SCHED` (no manual rotation field): current-block banner with week count + next block, real per-day week strip, schedule-aware study plan (shared `ROT_STUDY` engine), clinical priorities + anesthesia-bridge objectives (`RI`, keyed by the 12 block types), optional preview selector to look ahead at other rotations, weekly progress tracker, rest-day toggles, DO/NEVER hidden-curriculum card, and cross-tab jump buttons. (Merged the former THIS WEEK panel into this tab.)
- **PROCEDURES**: Procedures & skills logbook
- **NARRATIVE**: Personal statement themes, language upgrade examples, phrases to retire, monthly journal prompts
- **INTERVIEW**: Pre-written answers to 3 core reapplication questions + AI interview answer evaluator
- **APPLICATION**: LOR status tracking, mentor cadence, personal statement draft progression, anesthesia exposure log, counters
- **MILESTONES**: Timeline from match (May 2026) through anesthesia match day (Mar 2028), Step 3 phases, open task checklist
- **ROADMAP**: 6-phase sequential checklist from Anki setup to Match Day 2028
- **ANKI**: Card generator, settings diagnosis, daily targets, FSRS advisor
- **CURRICULUM**: Hidden curriculum — unwritten rules, rotation tips, situation coach
- **MIND**: Burnout support, mood tracking, perspective anchors, AI coach with persistent history
- **TASKS**: Universal capture, AI smart-add, category filtering, persistent log
- **SETTINGS**: GitHub config, API key, field migration tool, data export

---

## Key data.json Fields
`uworldSessions[]` · `journalEntries[]` · `exposureLog[]` · `checks{}` ·
`restDays[]` · `rotation` · `shadowingHours` · `anesthesiaContacts` ·
`lorStatus{}` · `profile{}`

## data.json Profile Fields
```
step2:           237
step3Target:     'Jan-Feb 2027'
currentRotation: 'Pre-residency'
program:         'Kaiser Permanente SF -- IM Categorical'
matchGoal:       'Anesthesiology 2027-2028'
orientation:     'June 15 2026'
firstRotation:   'July 1 2026'
```

## uworldSessions[] Entry Shape
```
date:      'YYYY-MM-DD'
questions: integer
score:     integer (percent correct)
topic:     string (e.g. 'Mixed / Random', 'Cardiology', etc.)
mode:      'TIMED' | 'TUTOR' | 'REVIEW'
notes:     string
ts:        unix timestamp ms
```

---

## CSS Design Tokens

### Color Palette (`:root`)
```
/* Backgrounds */
--bg: #0d0f0e        base page background
--s1: #141614        card surface (level 1)
--s2: #1a1d1a        inner surface (level 2)
--s3: #212421        elevated surface (level 3)

/* Borders */
--b:  rgba(255,255,255,0.07)   card outlines
--b2: rgba(255,255,255,0.13)   inner dividers

/* Accent */
--g:  #7fff6e   green    (primary, UWorld, active nav)
--g2: #4fc3f7   blue     (info, reading)
--g3: #ffb74d   amber    (warning, rotations)
--g4: #ef9a9a   rose     (alert)
--red: #ef5350

/* Text */
--t:  #e8ede8   primary body text
--t2: #9aa89a   secondary labels
--t3: #5a665a   tertiary hints, timestamps
```

### Typography
```
--mono:  'DM Mono', monospace
--serif: 'Fraunces', serif
--sans:  'DM Sans', sans-serif

body font-size: 26px    (matches workout-OS)
line-height:    1.7

Font size scale (used throughout HTML + inline styles):
  Primary text     26px   body, .lt, .tli-t, .qbox-a, .abox p, .air p
  Secondary text   25px   .sl, .ldt, .ig label, .btn, .sbtn, .tli-s, .lbad, .lgood
  Mono labels      24px   .lbl, .ptag, .spill, .bdg, .ld, .dn, .dt, .chip, .tli-d, .spin, .air .al, .pbl, .smsg
  Headings         29px   .h1, .dd, serif rotation titles
  Stat values      32px   .sv
  Logo             28px   .logo, .mlogo
  Tabs             25px   .tab
  Inputs           23px   input, select, textarea, .ci label
  Mobile nav icon  20px   .nicon
  Mobile nav label 11px   .nlbl  (intentionally compact)
```

### Layout
```css
/* Desktop */
.hdr  { padding: 16px 32px 0; position: sticky; top: 0; z-index: 50; }
.main { flex: 1; padding: 24px 32px; max-width: 80%; width: 100%; margin: 0 auto; }

/* Mobile nav */
--nav:  64px
--safe: env(safe-area-inset-bottom, 0px)
```

### Grid & Card Utilities
```css
.g2  { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
.g3  { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 14px; }
.g4  { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; }
.card { border-radius: var(--rl); padding: 18px 20px; margin-bottom: 16px;
        transition: border-color .2s; }
.card:hover { border-color: var(--b2); }
--r:  12px   (standard border-radius)
--rl: 18px   (large border-radius, cards)
```

### Panel Animation
```css
.panel.on { animation: fadeUp .25s ease; }
@keyframes fadeUp { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; } }
```

### Inline Font Size Rule
**Never hardcode small values in HTML or JS-rendered strings.**
Forbidden in HTML/JS context: anything below 24px (except `.nlbl` at 11px).
Always use the font size scale from the Typography section above.

---

## Burnout Risk Calculation
Inputs: energy (1–10), sleep (hours 0–9), motivation (1–10)
Color-coded output:
- LOW      green
- MODERATE amber
- HIGH     red
- CRITICAL blinking red

## Daily Mode Behavior
| Mode       | UWorld Requirement   |
|------------|----------------------|
| NORMAL     | Full session         |
| CALL       | 10 Q minimum         |
| POST-CALL  | Zero required        |
| RECOVERY   | Podcast only         |
| REST       | No UWorld            |

## Rotation / Schedule Engine
The current rotation is **derived from the real schedule, not a manual field.**
- `SCHED[]` — the full PGY-1 block calendar: `['YYYY-MM-DD','CODE']` pairs (week starts).
- `getCurrentSched()` — returns the active block for today `{i,code,start,week,meta}` (or a pre-residency pseudo-block before `SCHED[0]`).
- `getRotMeta(code)` — maps a block code (e.g. `E-CARD`, `ICU-2A`) → `ROT_META` entry with a `type` and display colors. 12 types: boot · icu · cards · adm · nf · wards · clinic · ed · vac · pc · id · pom.
- `schedForDate(date)` — per-day block lookup used by the ROTATION tab's week strip.
- `ROT_STUDY[type]` — schedule-aware study plan per type (UWorld topics, landmark paper + PubMed, procedures). Shared by the TODAY, SCHEDULE, and ROTATION tabs.
- `RI[type]` — clinical priorities + anesthesia-bridge objectives per type (keyed by the same 12 types).
- `ROT_LABELS[type]` — human-readable rotation names for the ROTATION tab's preview selector and AI guidance.
- Removed: the old manual `RM` study map and `showRot()` (replaced by the schedule-driven `buildRotations()` / `buildWkGrid()`).
- The `rotation` field in `data.json` is now only passed as fallback context to two AI prompts (`genToday`, `genJnlP`); it no longer drives any UI.

## Step 3 Study Phases
- Phase 1: Jun–Dec 2026 — 15–20 Qs/day
- Phase 2: Jan–Jun 2027 — all systems, daily CCS
- Phase 3: Jul–Sep 2027 — 30 Qs/day, target 240+

## LOR Status Values
`not started` · `building` · `pending` · `optional`

---

## AI Architecture
- Shared API key: `jm_api_key` in localStorage (same key as workout-OS)
- All AI calls route through a shared `ai(prompt, maxTokens)` wrapper
- System prompt includes: program context, anesthesia track specificity, narrative themes, stylistic guidelines
- Features using AI:
  - Daily coach (TODAY tab) — rotation + mode + metrics aware
  - UWorld topic recommendation — analyzes full session history
  - Rotation guidance generation — clinical priorities per rotation
  - Reading list generation — rotation + focus area tailored
  - Narrative reflection prompts — surfaces clinical moments
  - Interview answer evaluator — flags clichés, suggests rewrites

## Field Migration Tool (SETTINGS tab)
Safe data field rename utility — moves `D[oldField]` → `D[newField]`.
Non-destructive: aborts if new field already has data.
Use whenever renaming a data.json key to avoid data loss.

---

## What NOT to Do
- Never hardcode clinical case data or patient info into the app
- Never store the Anthropic API key anywhere except localStorage
- Never touch `data.json` directly — all writes go through the GitHub API push flow
