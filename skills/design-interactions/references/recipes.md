# Interaction recipes

Each recipe is a short list of tool calls; take every id from tool results. In a template deck,
build an interactive slide inside a positioned or flow group first, or shapes pile up at the
default spot: `addSlide {slideType: "blank", index}`, `addGroupLayout {layout: "column", gap: 24}`,
and `selectShape {slideId, shapeId: group}` before every insert (after an insert the new shape is
selected, so without this the next one lands outside the group). In a freeform slide deck pin the
group with `setShapePositioningMode` absolute and `setShapeCSSStyle` left/top/width/height; in a
flow ("auto") deck leave it in flow.

Contents: 1 Reveal on click, 2 Flip card, 3 Tabs, 4 Tooltip, dialog and link, 5 Remembered
reveals, 6 Variables and conditions, 7 Results by score tier, 8 Progress bound to a variable,
9 Hotspot and label on a picture, 10 Media

## 1. Reveal on click

1. A `col-2` group: buttons in the left column, the facts in the right (or a card per fact).
2. `addButton {label}` into the left column, `addText` (the fact) into the right.
3. `setShapeInitiallyHidden {slideId, shapeId: fact, initiallyHidden: true}`.
4. `addInteraction {slideId, shapeId: button, eventType: "click", actionType: "show",
   actionData: {targetShapeId: fact}}`.

Variations: `toggle-visibility` to show and hide again; a picture plus a hidden label for "what is
this?"; one `play-confetti` after the last reveal.

## 2. Flip card

Slide decks only: fluid cards do not allow overlap, so use stacked reveals there.

1. Two groups, front and back, absolute at the same left/top/width/height (needs a freeform
   deck or an absolute exception: `setShapePositioningMode` absolute plus `setShapeCSSStyle`); the
   back initially hidden.
2. Inside each group a transparent hit area (a shape covering it, `background: transparent`)
   carries the click, because a group cannot hide itself but a child can hide its parent.
3. Front hit area `click`: `hide` the front group, `show` the back group. Back hit area `click`:
   the reverse.

A grid of flip cards makes a vocabulary or definitions activity. Count "I knew it" clicks in a
variable if the user wants a score. Templates with a `flip cards` layout already have this wired.

## 3. Tabs

Slide decks only (same reason as flip cards).

1. A row of buttons, then one panel group per tab. Make the panels absolute at the same
   left/top/width/height so they overlap.
2. Every panel except the first is initially hidden.
3. On each button's `click`: `show` its panel, then `hide` each other panel (one action per panel).

## 4. Tooltip, dialog and link

- `addIcon {iconName: "info"}` with `mouse-enter` and `show-tooltip {content}` for a side note
  (desktop only; give phone users the same text another way). Not allowed on `slide-enter`.
- A button with `click` and `open-dialog {title, message}` for a longer explanation; the dialog
  shows plain text.
- `open-url {url}` for "read more" links with real URLs the user or a source gave; it is blocked
  in public presentations.

## 5. Remembered reveals

The player rebuilds a slide on every visit. To keep a reveal after the learner leaves and comes
back: a boolean variable per reveal (`createVariable {name, type: "boolean", defaultValue:
false}`); the click also runs `adjust-variable` `set` true; and the slide's `slide-enter` runs
`show` on the shape with the condition `isTrue`.

## 6. Variables and conditions

- `createVariable {name, type: "number" | "text" | "boolean", defaultValue}`; `getVariables` for
  ids and the read-only system variables (`$score`, `$maxScore`, `$scorePercentage` 0 to 100,
  `$correctAnswerCount`, `$answeredQuestionCount`, `$slideNumber`...).
- Count points: on each graded question `question-correct` runs `adjust-variable {variableId,
  operation: "add", operand: {kind: "literal", value: 10}}`.
- Show a value: write `{{score}}` in a text (a variable name, case-insensitive) in one
  `setShapeText`; the player keeps it live. For the points of graded questions the built-in
  `{{$score}}` already does it: create your own number variable only for clue values, penalties or
  team scores.
- Condition: pass `condition: {match: "all", rules: [{variableId, operator: "gte", operand:
  {kind: "literal", value: 20}}]}` on `addInteraction`; two actions on one event with opposite
  conditions make an if/else. `updateInteraction` with `removeCondition: true` makes it
  unconditional.
- `set-text {targetShapeId, text}` replaces a text for this visit (placeholders stay live).
- Edit and remove with the zero-based `eventIndex` and `actionIndex` from `getInteractions`.

## 7. Results by score tier

1. Build the target panels or slides first so their ids exist: for example gold, silver and try
   again.
2. On one results slide: three groups, initially hidden (`setShapeInitiallyHidden`); on the slide
   `slide-enter`, three `show` actions with conditions on `$scorePercentage`: `gte 80` for gold,
   `gte 50` and `lt 80` for silver (`match: all`), `lt 50` for try again.
3. Or on a button, two conditional `go-to-slide` actions to separate result slides.
4. Add a `Try again` button with `adjust-variable reset` per variable, then `go-to-first-slide`
   (a retry only reopens questions that allow more than one attempt), and a final button with
   `finish-presentation` to close the attempt for LMS tracking (see `configure-player-tracking`).
5. `play-confetti` on the gold panel's slide `slide-enter` for a celebration.

A results layout from a template (tag `results`) saves the visuals; read its wiring with
`getInteractions` and add the tier conditions.

## 8. Progress bound to a variable

`addProgress {variant: "bar" | "circle", variableId, min, max}` draws a meter that follows a
number variable, including system variables (`$score`, `$progressPercentage`). The meter shows no
number: put a `{{score}}` text beside it. `value` is only the authoring fallback. Style with
`fillColor`, `progressColor`, `trackColor`, `borderColor`, `borderWidth`, preferably as theme
variables such as `rgb(var(--aie-accent-0-rgb))`.

## 9. Hotspot and label on a picture

1. The picture first (`addImage` with a real HTTPS URL or a picture the user gave).
2. `addQuestion {questionType: "hotspot"}` (or `"label"`).
3. Look at the picture before choosing coordinates: `getSlideImage` and estimate the spots in
   percent of the question area.
4. `setQuestionAnswers {slideId, shapeId, hotspots: [{shape: "rectangle", x, y, width, height}]}`,
   or `labels: [{text, x, y}]` plus `distractors` for a label question.
5. Look again after setting them and adjust if a spot is off.

For a non-graded explore picture: a transparent shape over each spot with `click` and `show` on a
hidden label card.

## 10. Media

- `addEmbeddedVideo {provider: youtube | vimeo | dailymotion | loom, url, controls: true,
  autoplay: false}` with a URL you know exists; otherwise `addVideoPlaceholder` and tell the user
  where to drop their video.
- `addAudio {source, controls: true, autoplay: false}` with a real audio URL.
- `play-media`, `pause-media` work on native and embedded video; `media-ended` only on native
  audio and video, and never when the clip loops. Narration is generated only by `addNarration`
  (when listed); with audio files the user supplies, `addAudio {autoplay: true, controls: false}`
  and `media-ended` to `next-slide`; browsers block autoplay until a first click, so keep a
  visible Start button first. `setMediaPlayback {slideId, shapeId, loop: true}` makes ambient audio
  repeat (a looping clip never fires `media-ended`), and changes autoplay or controls of media that
  already exists.
