# Games: quiz shows, escape rooms, boards and other learning games

What the player can do for games, how to use the game templates, and recipes for the mechanics.
Every mechanic is built with the interaction tools (`interactions.md`, section 1 for the rules).
Questions follow the `create-quiz` skill.

Contents: 1 Game templates, 2 What the engine offers, 3 Setting up a game deck, 4 Mechanics,
5 Game catalog, 6 Pitfalls, 7 What to tell the user

## 1. Game templates

The Game and Quiz templates (`variableCount` above zero: Quiz Show with Lives, Escape Room, Dice
Arena, Jeopardy Scoreboard, Last One Standing, Workplace Safety...) are **layout libraries in a
game look**, not ready-made games:

- their **ordinary layouts** (`title`, `title and content`, `list-N`, `chart`, `timeline`...) have
  no game logic and serve any deck in that style;
- their **game screens** carry genre tags (`game-board`, `clue slide`, `team scoreboard`,
  `round`, `game over`, `win`...) and slide-local mechanics: hearts that react to wrong answers, a
  score shown with `{{score}}`, buttons that add or take points, reveal cards. Variables the
  screens use come along when they are inserted;
- the links between screens of a whole game, its music and its player mode are not part of what
  a template gives a new deck: you wire and set them (sections 3 and 4).

So, for a game:

1. Pick the game template whose mechanics fit (section 5) and read its screens with
   `getTemplateLayouts {templateId}`: tags, `actionCount` (how wired a screen is) and slots.
2. Insert the screens the game needs **in one call**, in play order. Jumps between screens by
   slide id or number are re-pointed among the slides inserted together; jumps to screens not
   inserted are removed and the result warns about them (the warning counts them).
3. After the insert, `getInteractions {slideId}` on each screen and `getVariables` show what is
   wired. Fill the texts, then **add the links that are missing** (a board button to its
   question, a door to the win screen) with the recipes in section 4.
4. Set up the deck (section 3).

## 2. What the engine offers

- **Variables** (`createVariable`): number, text or boolean; they live across slides and reset on
  "Try again". System values for conditions and text: `$score`, `$maxScore`, `$scorePercentage`
  (a fraction, so compare with `gte` and `lt`, never `eq`), `$correctAnswerCount`,
  `$answeredQuestionCount`, `$slideNumber`, `$timeLeft`...
- **Live text**: any text can show `{{lives}}`, `{{score}}` or `{{$timeLeft}}`; the player keeps it
  current. Write the placeholder in one go (`setShapeText`), not split across styles. The editor
  shows it as written.
- **Timer**: `addInteraction {eventType: "timer", timerSeconds: 1-3600, ...}` on a slide fires once,
  N seconds after the slide appears, and pauses with the player; `{{$timeLeft}}` counts down. One
  timer per slide; it stops when the slide changes.
- **Chance**: `adjust-variable` with `operation: "random"` (whole numbers, both ends included);
  `go-to-random-slide {slideIds}` (never the current slide); `shuffleAnswers` on choice questions.
- **Conditions**: one per action, `{match: all | any, rules}`; read just before the action runs,
  so an earlier `adjust-variable` in the same event changes what later conditions see. There is
  no else: add a second action with the opposite condition.
- **set-text**: `{targetShapeId, text}` replaces a text for this visit (placeholders stay live).
- **Meters**: `addProgress {variableId, min, max}` draws a bar or circle; it shows no number, so
  put a `{{score}}` text beside it.
- **Player**: `setPlayerMode {mode: "game"}` hides the slide list, prev/next, progress and generic
  submit and locks free navigation; `setNavigationLock {locked: true}` locks navigation alone.
  A slide with its own button wired to `submit` hides the player's submit button.
- **Background music**: `setBackgroundMusic {source, name, volume}` loops one track across the
  deck. Like all player media it starts muted: the first action of the Start button must be
  `unmute-media`. The tools cannot make music: use an audio file the user gives you (an HTTPS
  URL), or tell them to add a track in Settings, Background music. A game without sound feels
  unfinished, so always mention it.

## 3. Setting up a game deck

- A cover with a **Start** button (`unmute-media` first, then `next-slide`) and a **How to play**
  slide with the rules in three or four short lines.
- `setPlayerMode {mode: "game"}`.
- Every question in a game: `setQuestionSettings {mode: "graded", navigation: "stay",
  maxAttempts: 0}` unless the game says otherwise. With the default navigation the player moves
  on by itself and fights your own go-to actions.
- Give each question slide its own styled submit button (`addButton` + `submit`).
- Prefer relative navigation where possible (`next-slide`, `go-to-last-slide` with Game Over kept
  last, `go-to-first-slide` for Play again): it survives slides being moved or copied.
- A **Play again** button: `reset` every game variable (one action each), then
  `go-to-first-slide`.

## 4. Mechanics

`LIVES`, `SCORE`, `FLAG` stand for variable ids from `createVariable`. Build the target slides
first so their ids exist.

**M1 Lives.** `createVariable {name: "lives", type: "number", defaultValue: 3}`; a HUD text
`Lives: {{lives}}` (or hearts); on each question `question-incorrect`, in this order:
`adjust-variable` subtract 1, then `go-to-slide` Game Over with the condition `lives lte 0`.
`question-incorrect` fires on every wrong attempt, so each attempt costs a life.

**M2 Board with done tiles (Jeopardy).** A board slide with one button per question
(`go-to-slide` to its question). One boolean per question (`q1Done`). On the question:
`question-correct` → set `q1Done` true, add the points to `score`, `go-to-slide` back to the
board. Board `slide-enter`: `hide` the tile when `q1Done isTrue` (and `show` a "done" mark).
A Final score button shown when every flag is true (`match: all`).

**M3 Keys and a locked door (escape room).** One boolean per key. Each puzzle slide: the
question, a hint button (`show` a hidden hint), a back button to the hub, a hidden reward card.
`question-correct` → `add` 1 to `keysFound` **with the condition `kN isFalse`** (no double count),
then `set kN true`, then `show` the reward. The hub shows `Keys: {{keysFound}} / 3`. The door:
`go-to-slide` win when `keysFound gte 3`, `open-dialog` "Still locked" when `lt 3`.

**M4 Countdown.** A text `{{$timeLeft}} s`; slide `timer` (10 s) → subtract a life, then go to
Game Over when `lives lte 0`, else `next-slide`. A correct answer that moves on first wins the
race, because the timer stops when the slide changes. To keep the remaining time:
`question-correct` → `set` a variable to the operand variable `$timeLeft`.

**M5 Score meter and tiers.** `addProgress {variant: "bar", variableId: <score>, min: 0, max:
<total>}` with a `{{score}}` text. On the result slide three initially hidden panels (gold,
silver, try again), each shown on `slide-enter` with `gte` / `lt` conditions.

**M6 Branching story.** One slide per situation with two or three choice buttons that go to the
consequence; choices counted in variables (`empathy` add 1); the ending shows the panel that
matches the variables. Lock navigation so learners cannot skip ahead.

**M7 Flip cards** and **M8 hidden objects**: `interactions.md` section 4, and for hidden objects a
transparent hit area over each spot that shows a check mark, adds 1 to `found` and hides its
own group; `play-confetti` and an "All found" panel when `found eq N`.

**M9 Guard against skipping.** On a gated slide's `slide-enter`: `go-to-slide` back to the gate
when `gatePassed isFalse` (useful when navigation is not locked).

**M10 Dice.** A Roll button: `adjust-variable {operation: "random", min: 1, max: 6}` into `dice`, a
text `You rolled {{dice}}`, then branch on `dice` with conditions.

## 5. Game catalog

| Game | Mechanics | Questions | Template to start from |
|---|---|---|---|
| Quiz show with lives | M1, M4, M5 | multiple-choice, true-false, fill-blanks | Quiz Show with Lives, Last One Standing |
| Jeopardy board | M2, M5 | any graded type | Jeopardy Scoreboard |
| Escape room | M3, M4 | fill-text-entry codes, matching, sequence | Escape Room |
| Dice or board game | M10, M2 | any graded type | Dice Arena |
| Millionaire ladder | M1 with one life, M5 | multiple-choice, one attempt | Quiz Show with Lives |
| Speed round | M4 | true-false, multiple-choice | any game template |
| Branching scenario | M6, M9 | survey questions for reflection | Workplace Safety, or any template |
| Flashcards | M7 | survey self-check | any template |
| Sort it, put in order | none | matching, drag-words, label, sequence | any template |
| Map or diagram quest | none | label, hotspot (`interactions.md`, section 7) | any template |

Games that need real-time action, many players at once, free dragging anywhere or a large grid
(word search, maze) cannot be built: say so and offer the nearest game above.

## 6. Pitfalls

- `question-answered` fires as well as `question-correct` / `question-incorrect`: put navigation on
  one of them only, or the player moves twice.
- Conditions are read in order at run time: "if x = 0 set 1" followed by "if x = 1 set 0" runs
  both. For booleans use `toggle`; otherwise copy the value into a helper variable first.
- A shape cannot hide itself: hide its group.
- Deleting and re-creating a slide changes its id: update every `go-to-slide` that pointed at it
  (`getInteractions`, then `updateInteraction`).
- `$correctAnswerCount` keeps counting across replays: a replayable scoreboard needs its own
  variable, reset on Play again.
- Four multiple-choice options with a title and a button can overflow a 720 px slide: check with
  `reviewSlides` and the slide picture.

## 7. What to tell the user

The tools build the game but cannot play it. Say which paths to test in the player (winning,
losing, Play again, leaving and resuming), which links and variables you wired, and that the game
needs a music track if none was given.
