# Interactions: reveals, tabs, flip cards, tooltips, media

Recipes for interactive slides in the player. Each is a short list of tool calls; take every id
from tool results. In a deck made from a template, build the interactive slide inside a
positioned group first (`composed-slides.md`, section 2), or the shapes pile up. Scores,
branching on a score and results slides are in the `create-quiz` skill; game mechanics (lives,
timers, boards, keys, dice) in `games.md`.

Contents: 1 How interactions work, 2 Reveal on click, 3 Tabs, 4 Flip card, 5 Tooltip, dialog and
link, 6 Remembered reveals, 7 Hotspot and label on a picture, 8 Media slide, 9 Narrated slides

## 1. How interactions work

- `addInteraction {slideId, shapeId?, eventType, actionType, actionData?, condition?}`. Without
  `shapeId` the interaction belongs to the slide (`slide-enter`, `timer`).
- Events: `click`, `double-click`, `mouse-enter`, `mouse-leave` on any shape; `slide-enter` and
  `timer` on slides; `question-correct`, `question-incorrect`, `question-answered` on questions;
  `media-ended` on native audio and video.
- Actions on one event run in the order they were added. Navigation does not stop the chain, so
  never put a show or hide after a go-to in the same event.
- A shape cannot show, hide or toggle **itself**: put it in a group and target the group (a child
  may hide its parent).
- **`hide` collapses** the shape (`display: none`): its neighbours in a flow group move up.
  **Initially hidden** (`setShapeInitiallyHidden {slideId, shapeId, initiallyHidden: true}`) keeps
  its space until it is shown. For a stable layout reveal initially hidden shapes, or give the
  reveal area a fixed height.
- `slide-enter` fires on every visit, also when going back. The slide is rebuilt on each visit,
  so a revealed shape is hidden again (section 6 keeps it).
- Hover events do nothing on phones and tablets: never hide essential content behind hover.
- A click passes up to a parent's click only when the clicked shape has no events at all.
- `show-tooltip` is not allowed on `slide-enter`. `open-url` is blocked in public presentations.
- Check every interactive slide with `getInteractions {slideId}`, and tell the user you could not
  play it: the tools build interactions but cannot click through the player.

## 2. Reveal on click

Explore activities: a button per fact, the fact hidden until clicked.

1. A `col-2` group: buttons in the left column, the facts in the right (or a card per fact).
2. `addButton {label}` into the left column, `addText` (the fact) into the right column.
3. `setShapeInitiallyHidden {slideId, shapeId: fact, initiallyHidden: true}`.
4. `addInteraction {slideId, shapeId: button, eventType: "click", actionType: "show", actionData:
   {targetShapeId: fact}}`.

Variations: `toggle-visibility` to show and hide again; a picture plus a hidden label for "what
is this?"; one `play-confetti` action after the last reveal.

## 3. Tabs

Several buttons, each showing its own panel at the same place.

1. A row of buttons, then one panel group per tab. Make the panels absolute at the same
   left/top/width/height (`setShapePositioningMode` + `setShapeCSSStyle`) so they overlap.
2. Every panel except the first starts initially hidden.
3. On each button's click: `show` its panel, then `hide` each other panel (one action per panel).

## 4. Flip card

Front and back at the same spot; a click turns the card.

1. Two groups, front and back, absolute at the same left/top/width/height; the back initially
   hidden.
2. Inside each group a transparent hit area (a group or shape covering it, `background:
   transparent`) carries the click, because a group cannot hide itself but a child can hide its
   parent.
3. Front hit area click: `hide` the front group, `show` the back group. Back hit area click: the
   reverse.

A grid of flip cards makes a vocabulary or definitions activity; count "I knew it" clicks in a
variable if the user wants a score.

## 5. Tooltip, dialog and link

- `addIcon {iconName: "info"}` with `mouse-enter` → `show-tooltip {content}` for a side note
  (desktop only; give phone users the same text another way).
- A button with `click` → `open-dialog {title, message}` for a longer explanation ("Did you
  know?"); the dialog shows plain text.
- `open-url {url}` for "read more" links, only with real URLs the user or the source gave.

## 6. Remembered reveals

The player rebuilds a slide on every visit. To keep a reveal after the learner leaves and comes
back: a boolean variable per reveal (`createVariable {name, type: "boolean", defaultValue:
false}`); the click also sets it true (`adjust-variable` `set`); and the slide's `slide-enter`
shows the shape when the variable `isTrue`.

## 7. Hotspot and label on a picture

Graded questions about a picture (geography, anatomy, machine parts):

1. The picture first (`addImage` with a real HTTPS URL or a picture the user gave; on a template
   slide its sample picture only if it shows the right thing).
2. `addQuestion {questionType: "hotspot"}` (or `"label"`).
3. **Look at the picture before choosing coordinates**: `getSlideImage` and estimate the spots in
   percent of the question area.
4. `setQuestionAnswers {slideId, shapeId, hotspots: [{shape: "rectangle", x, y, width, height}]}`,
   or `labels: [{text, x, y}]` plus `distractors` for a label question.
5. Look again after setting them; adjust if a spot is off.

## 8. Media slide

- `addEmbeddedVideo {provider: youtube | vimeo | dailymotion | loom, url, controls: true,
  autoplay: false}` with a URL you know exists (from the user or the source); otherwise
  `addVideoPlaceholder` and tell the user where to drop their video.
- `addAudio {source, controls: true, autoplay: false}` with a real audio URL.
- `play-media` and `pause-media` actions work on native and embedded video; `media-ended` only on
  native audio and video, and never when the clip loops.

## 9. Narrated slides

The tools cannot record or synthesize narration. When the user has audio files (an HTTPS URL per
slide), each slide gets `addAudio {source, autoplay: true, controls: false}` and a `media-ended`
interaction on it with `next-slide`. Browsers block autoplay until the learner clicks once, so
keep a visible Start or Next button on the first slide. Without audio files, tell the user that
narration can be added in the editor.
