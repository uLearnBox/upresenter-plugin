# Game mechanics: engine facts and recipes

Every mechanic is built with `addInteraction`, `updateInteraction`, `createVariable`,
`setShapeInitiallyHidden` and the player tools. Behaviour here was checked against the engine
source (`event-action.js`, `interaction-variables.js`) and the tool definitions.

Contents: 1 Engine facts, 2 Variables and text, 3 Setting up a game deck, 4 Mechanics M1-M10,
5 Pitfalls, 6 What to tell the user

## 1. Engine facts

**Events** (`eventType`): `click`, `double-click`, `mouse-enter`, `mouse-leave` on any shape;
`slide-enter` and `timer` on a slide; `question-correct`, `question-incorrect`,
`question-answered` on a question; `media-ended` on native audio and video.

- `slide-enter` fires on every visit, also when going back.
- `question-correct` and `question-incorrect` fire on every wrong or right attempt of a graded
  question. `question-answered` fires on any saved submission, surveys too, **as well as** the
  other two: put navigation on one of them only.
- `timer {timerSeconds: 1-3600}` fires once, N seconds after the slide appears (default 10),
  pauses with the player, and stops when the slide changes. One timer per slide: setting it again
  changes the delay for all its actions.
- Hover events do nothing on touch screens.

**Actions** (`actionType`): `next-slide`, `previous-slide`, `go-to-first-slide`,
`go-to-last-slide`, `go-to-slide` (`actionData.slideId`), `go-to-slide-at-index`
(`slideIndex`, zero-based), `go-to-random-slide` (`slideIds`; never the current slide; empty means
any other slide), `show`, `hide`, `toggle-visibility` (`targetShapeId`), `set-text`
(`targetShapeId`, `text`), `adjust-variable`, `show-tooltip`, `open-dialog` (`title`, `message`),
`play-confetti` (`confettiType`: basic, fireworks, stars, school-pride, snow, fountain, explosion,
rain), `play-media`, `pause-media`, `toggle-play-pause-media`, `mute-media`, `unmute-media`,
`toggle-media-mute` (sound actions apply to the whole presentation), `submit`,
`finish-presentation`, `pause-presentation`, `resume-presentation`, `open-url` (blocked in public
presentations).

- Actions of one event run in the order added. Navigation does not stop the chain: never put a
  show or hide after a go-to in the same event.
- A shape cannot show, hide or toggle itself: put it in a group and target the group (a child may
  hide its parent).
- `hide` collapses the shape (neighbours in a flow group move up). `setShapeInitiallyHidden
  {slideId, shapeId, initiallyHidden: true}` keeps the space until the shape is shown, and is the
  right way to start hidden (not a `slide-enter` hide).
- `adjust-variable {variableId, operation, operand}`: operations `set`, `add`, `subtract`
  (numbers), `toggle` (booleans), `reset`, `random` (whole number from `min` to `max`, both
  included, no operand). The operand is a literal or another variable.

**Conditions**: one per action, `{match: "all" | "any", rules: [{variableId, operator, operand}]}`.
Operators: numbers `eq ne gt gte lt lte`; text `eq ne contains empty notEmpty`; booleans `isTrue
isFalse`. Unary operators take no operand. The condition is read just before its action runs, so
an earlier `adjust-variable` in the same event changes what later conditions see. There is no
else: add a second action with the opposite condition. An invalid condition skips the action.

## 2. Variables and text

- `createVariable {name, type: number | text | boolean, defaultValue}`; `getVariables` returns
  ids, defaults and the read-only system variables. Values live across slides, survive resume and
  reset on Try again. `updateVariable` changes the definition only; `deleteVariable` fails while
  something still uses it.
- System variables: `$score`, `$maxScore`, `$scorePercentage` (earned score as a percentage from 0 to 100, 0 when
  there are no points; unrounded, so compare with `gte` and `lt`, never `eq`; 80 means 80 percent), `$correctAnswerCount`, `$incorrectAnswerCount`, `$viewedSlides`, `$progressPercentage` (0-100), `$slideElapsed`, `$answeredQuestionCount`,
  `$slideNumber`, `$totalSlides`, `$timeLeft` (countdown of the slide timer), `$passed`,
  `$completed` (need the pass criteria, see `configure-player-tracking`).
- Live text: write `{{lives}}`, `{{score}}` or `{{$timeLeft}}` (a variable name, case-insensitive,
  an id, or a system variable) in any text with one `setShapeText`; the player keeps it current and
  rounds percentages. The editor shows the placeholder as typed. Do not split a placeholder across
  styles.
- Meter: `addProgress {variant: "bar" | "circle", variableId, min, max}` follows a number variable
  (system variables too). It shows no number: put a `{{score}}` text beside it.
- Chance: `random`, `go-to-random-slide`, and `setQuestionSettings {shuffleAnswers: true}` on
  multiple choice and multiple response.

## 3. Setting up a game deck

- Cover with a **Start** button: `unmute-media` first (the click is the gesture that lets the
  browser play sound), then `next-slide`. A **How to play** slide with three or four short rules.
- `setPlayerMode {mode: "game"}`: hides the slide list, previous and next, review, early finish,
  generic submit, autoplay, progress bar, share and slide number, and locks free navigation
  (arrow keys, swipe, slide list). `setNavigationLock {locked: true}` locks navigation alone;
  `setPlayerButton` can bring a single button back afterwards.
- Every game question: `setQuestionSettings {mode: "graded", navigation: "stay", maxAttempts: 0}`
  unless the game says otherwise. With the default `next-slide` navigation the player moves on by
  itself and fights your own go-to actions.
- A slide with its own button wired to `submit` hides the player's generic Submit button: give each
  question slide its own styled submit button (`addButton` plus a `submit` action).
- Prefer relative navigation (`next-slide`, `go-to-last-slide` with Game Over kept last,
  `go-to-first-slide` for Play again): it survives slides being moved or copied.
- **Play again**: one `adjust-variable reset` per game variable, then `go-to-first-slide`.
- **Music**: `setBackgroundMusic {source | base64 + mimeType, name, volume 0-100, loop}` plays one
  looping track over every slide; it ducks while a slide plays its own audio or video, pauses with
  the player and follows the player's sound button. Pass `remove: true` to clear it. It starts
  muted until the Start button's `unmute-media`. Use WAV for loops.

## 4. Mechanics

`LIVES`, `SCORE`, `FLAG` stand for variable ids from `createVariable`. Build the target slides
first so their ids exist. In a game template many of these already exist on its screens:
read them with `getInteractions` before adding anything.

**M1 Lives.** `createVariable {name: "lives", type: "number", defaultValue: 3}`; a HUD text
`Lives: {{lives}}`; on each question `question-incorrect`, in this order: `adjust-variable`
subtract 1, then `go-to-slide` Game Over with the condition `lives lte 0`.

**M2 Board with done tiles (Jeopardy).** A board slide with one button per question
(`go-to-slide` to its clue). One boolean per question (`q1Done`). On the question
`question-correct`: set `q1Done` true, add points to `score` (your own variable: clue values and
team scores are not the question `points` that the built-in `$score` adds up), `go-to-slide` back
to the board.
Board `slide-enter`: `hide` the tile when `q1Done isTrue` (and `show` a done mark). A Final score
button appears when every flag is true (`match: all`). Team play: one number variable per team
(`score1`...) as in the Jeopardy template.

**M3 Keys and a locked door (escape room).** One boolean per key. Each puzzle slide: the
question, a hint button (`show` a hidden hint), a back button to the hub, a hidden reward.
`question-correct`: `add` 1 to `keysFound` **with the condition `kN isFalse`** (no double count),
then `set kN true`, then `show` the reward. Hub text `Keys: {{keysFound}} / 3`. The door:
`go-to-slide` win when `keysFound gte 3`, `open-dialog` "Still locked" when `keysFound lt 3`.

**M4 Countdown.** Text `{{$timeLeft}} s`; slide `timer` (for example 10 seconds): subtract a
life, then `go-to-slide` Game Over when `lives lte 0`, else `next-slide`. A correct answer that
moves on first wins the race, because the timer stops when the slide changes. To keep the
remaining time: `question-correct` then `set` a variable to the operand variable `$timeLeft`.

**M5 Score meter and tiers.** `addProgress {variant: "bar", variableId: <score>, min: 0, max:
<total>}` plus a `{{score}}` text. On the result slide three panels (gold, silver, try again),
initially hidden, each shown on `slide-enter` with `gte` / `lt` conditions on `$scorePercentage`.

**M6 Branching story.** One slide per situation with two or three choice buttons that go to the
consequence; choices counted in variables (`empathy` add 1); the ending shows the panel that
matches the variables. Lock navigation so learners cannot skip ahead. The Workplace Safety
template has `scenario intro`, `decision point`, `consequence`, `ending`.

**M7 Flip cards**, **M8 hidden objects**: see `design-interactions`. For hidden objects, a
transparent hit area over each spot shows a check mark, adds 1 to `found` and hides its own group;
`play-confetti` and an "All found" panel when `found eq N`.

**M9 Guard against skipping.** On a gated slide's `slide-enter`: `go-to-slide` back to the gate
when `gatePassed isFalse`.

**M10 Dice.** A Roll button: `adjust-variable {operation: "random", min: 1, max: 6}` into `roll`,
text `You rolled {{roll}}`, then branch on `roll` with conditions (Dice Arena does this).

## 5. Pitfalls

- Conditions are read in order at run time: "if x = 0 set 1" followed by "if x = 1 set 0" runs both.
  For booleans use `toggle`; otherwise copy the value into a helper variable first.
- Deleting and re-creating a slide changes its id: update every `go-to-slide` that pointed at it.
- `$correctAnswerCount` keeps counting across replays: a replayable scoreboard needs its own
  variable, reset on Play again.
- Four multiple-choice options with a title and a button can overflow a 720 px slide: check with
  `reviewSlides` and the slide picture.
- `show-tooltip` is not allowed on `slide-enter`.

## 6. What to tell the user

The tools build the game but cannot play it. Say which paths to test in the player (winning,
losing, Play again, leaving and resuming), which links and variables you wired, whether the
background music is set, and that narration is added in the editor unless `addNarration` was used.
