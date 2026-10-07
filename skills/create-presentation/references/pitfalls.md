# Errors the tools raise, and the rule behind each

A refused call returns a message; read it, fix the arguments and retry once. These are the rules
agents hit most often. Each line is checked by the editor, so there is no way around it.

## Building

- `insertTemplateSlides` takes at most 30 slides per call (a game flow goes in one call, a bigger
  one in two), and the template's kind (slide or fluid) must match the deck's.
- `duplicateShape` and `duplicateSlide` return only `{success}`. Read the deck again with
  `getPresentationState` to find the copy before you wire or edit it.
- `setSlideCSSStyle` and `setShapeCSSStyle` ignore keys they do not know instead of failing: read
  the style back (`getSlideCSSStyle`, `getShapeCSSStyle`) when a style seems to do nothing.
- `applyTheme` needs a theme id from `listThemes`; `createTheme` checks the name and the colors
  and says which one is wrong.
- `setBackgroundMusic` needs a `source` (or `base64`) the first time; later calls may change only
  `volume`. `setSlideAutoAdvance` takes 0 (off) to 3600 seconds.
- `getSlideImage` draws at most 1920 x 1080: tall or portrait presets come back cropped.

## Text, tables and charts

- `setTableCellsText` refuses two edits to the same cell in one call.
- `editTableStructure` cannot delete the only row or column; moving a row or column
  needs a `direction`.
- `setChartData` needs exactly one value per label and finite numbers. Scatter and bubble charts
  keep their sample points; the user edits those in the editor.

## Questions

- `setQuestionAnswers`: a choice question needs at least 2 answers, true-false exactly 2,
  matching 2 to 6 pairs. The count of `___` markers in `sentence` must equal the number of
  `blanks` (or `dropdowns`); a slider needs `min` < `max`, a positive `step` and a `correctValue`
  inside the range.
- `setQuestionSettings`: `points` is a whole number from 1 to 1000, `navigationDelay` 0 to 100
  seconds, and `caseSensitive` is accepted only on fill-text-entry questions, so do not send it
  in a batch to every question.

## Interactions and variables

- A slide takes only `slide-enter` and `timer`; a shape takes everything else. `show-tooltip`
  is for shapes. A shape cannot show, hide or toggle itself. All rules:
  `design-interactions/references/events-actions-variables.md`.
- `removeInteraction` deletes an event once its last action is gone, so every later `eventIndex`
  moves: call `getInteractions` again after each removal.
- A variable operand must have the variable's type, names are unique ignoring case, and a variable
  that is still used cannot be deleted or change type.

## Motion and media

- `setShapeAnimation` refuses an effect that does not match its `trigger` (`fade-in` as `exit`)
  and a direction that the effect does not offer. `setSlideTransition` ignores a direction on a
  transition that has none.
- `setMediaPlayback` works on audio, video and embedded videos only; `loop` is not available for
  embedded videos and `mode` only for audio.

## Grouping and AI media

- `groupShapes` needs at least two shapes with the same parent; questions and some special shapes
  cannot join a group. `ungroupShape` works only on a group with children. Ids of the children do
  not change; the group gets a new id.
- `removeImageBackground` and `addNarration` spend credits and appear only on connections that
  allow AI media. Background removal rejects SVG and image shapes without a picture; narration
  accepts only the nine listed voices and refuses empty text.
