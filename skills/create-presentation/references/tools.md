# uPresenter tool guide

A quick map of the uPresenter tools and their main arguments. The live tool list always wins:
if a call is rejected for a bad argument, read that tool's schema and fix the call.

Common geometry on every `add*` tool: `x, y, width, height, name` (CSS px; x and y only matter
inside freeform containers). Every insert goes into the **current slide**, and into the
**selected group** if one is selected; after an insert the new shape is selected. In a deck made
from a template (freeform) inserts land on top of each other: see `composed-slides.md`.

**Ids.** Slide and shape ids come from tool results; pass them back as given. Shape ids repeat on
every slide, so every tool that names a shape takes `slideId` and `shapeId` together.

Formulas written as `[[katex:LATEX]]` work in `setShapeText`, `addText`, table cells, the answers
of multiple-choice, multiple-response and true-false questions, sequence items, matching pairs and
the sentences of fill-blanks, drag-words and multiple-dropdown questions; not in dropdown options,
fill-text-entry answers, chart labels or names. See `math-formulas.md`. `getPresentationState`
lists them as `text.formulas`.

Editing tools take an optional `sessionId` (from `listOpenEditorContexts`); it is only needed
when more than one editor tab is open.

## Context

- `listPresentations {nameContains?, limit?}` returns `{presentationId, name, editorUrl}` for
  the user's presentations.
- `getNewPresentationLink {templateId?}` returns a link. Without a `templateId` it opens the
  "Create blank presentation" dialog; with one, opening it **creates** a presentation from that
  template and opens it in the editor. A fluid template gives a link that creates a **fluid**
  presentation (the result says `layoutType`).
- `listTemplates {query?, category?, language?, layoutType?, includeLegacy?, limit?}` lists the
  gallery templates (up to `limit`, default 20, max 50, sorted by name) with the `total` that
  matched. `layoutType` is `slide` (default) or `fluid`: a fluid presentation is built from a fluid
  template, never by converting a slide one (`fluid.md`). Each result has:
  `templateId`, `name`, `description`, `categories`, `language`, `themeId`, `palette`, `fonts`,
  `slideCount`, `layouts` (slides per layout tag), `previewUrl` (a page that shows every slide)
  and `variableCount` (above zero: a game or quiz-show flow).
- `query` searches name, description and categories; `category` must equal a gallery category
  (Business, Education, Game, Quiz, Healthcare, Nonprofit, Design, Marketing, Travel).
- `getTemplateLayouts {templateId, tag?}` lists a template's slides: `slideId`, `index`, `tag`,
  `name`, `actionCount`, `itemCount`, `hasChart` with `chartShapeId`, `tableShapeId` with `tableRows` and
  `tableColumns`, `hasInfographic` with `entryCount`, `imageSlots`, `questionShapeId` with
  `questionType` (the `addQuestion` name), and `slots` (`shapeId`, `role`, `maxChars`, `minChars`,
  and either `prompt` for an empty placeholder or `sample` for text that ships in the layout and
  must be replaced).
- `listOpenEditorContexts` lists the presentations open in an editor tab.
- `attachToPresentation {presentationId}` finds the open editor for a presentation.
- `getPresentationState {scope}` with scope `selected_shapes | selected_slides | current_slide |
  all_slides | presentation_basic | presentation_full`.
- `getSlideImage {presentationId, slideId | slideIndex}` returns a picture of one slide as the
  player draws it (a JPEG of the slide, followed by its id, index and size; in a fluid
  presentation one whole card, as tall as its content). It shows the saved
  state and takes a few seconds. `presentationId` comes from `listOpenEditorContexts` or
  `listPresentations`.
- `reviewSlides {slideIds?}` shows each slide in the editor, measures how it really renders and
  returns the problems per slide: `placeholder`, `spillsOutOfBox`, `outsideCard`, `outsideSlide`
  (with `spillPx`), `overlapsText` (with `otherShapeId`) and `plainMath`; plus `problemSlides` and
  `remaining`, the slides still to review (a deck of more than 40 slides, or the time ran out):
  pass them as `slideIds` in the next call. It moves the editor's current slide while it works
  and restores it.
- `getPresentationInfo` returns name, slide count, theme colors and fonts, `layoutType` (`slide`
  or `fluid`), `positioningMode`, size, `templateId` (the template the deck was created from) and
  `themeId` (the theme it wears now; `listTemplates` gives each template's `themeId`).

## Presentation

- `setPresentationName {name}`, `setPresentationLanguage {languageCode}` (BCP-47, `vi-VN`)
- `setPresentationBackground {color}`: the page behind the slides (CSS color, theme variable or
  gradient)
- `setLayoutType {layoutType: slide | fluid}`: switches how an existing deck is sized and flowed. It
  does **not** turn a slide deck into a designed fluid one, because slide layouts are fixed
  compositions: to make a fluid presentation start from a fluid template (`fluid.md`)
- `setPresentationPositioningMode {positioningMode: auto | freeform}`
- `setSlideSize {preset}` or `{width, height}`: presets `16:9` (1280x720), `16:9-hd`, `4:3`,
  `16:10`, `a4-landscape`, `a4-portrait`, `social-post` (1080x1080), `social-story` (1080x1920)
- `setFluidMaxWidth {maxWidth}`: the width of the card column of a fluid presentation (templates use 1040px)

## Slides and layout

- `insertTemplateSlides {slides: [{slideId} | {tag}], index?, templateId?, applyThemeFromTemplate?}`
  clones template layouts into the open deck, in order, starting at `index` (default: after the
  current slide). `templateId` defaults to the deck's own template (pass the chosen one). With
  `applyThemeFromTemplate: true` the deck also switches to the template's theme (restyles every
  slide; no credits); with that flag and no `slides` only the theme changes. Returns each
  new slide's `slideId`, `slideIndex`, `tag` and `slots`, plus `chartShapeId`, `tableShapeId`,
  `questionShapeId`, `entryCount` and `imageSlots` where present, and `warnings` (slide size
  differs from the deck, or go-to-slide actions had to be removed). Slots still show their prompt until `setShapeText`
  fills them; `getPresentationState` marks them with `placeholder` and gives `maxChars`.
- `generateShapeImage {slideId, shapeId, prompt, model, ratio, style?}` (offered only on some
  connections) generates a picture into an image slot and spends credits; the result has the
  stored image name and `creditsRemaining`. Always pass `model` and `ratio`. Rules for its use:
  `template-layouts.md`, section 4.
- `addSlide {slideType: "blank", index}` returns `{slideId, slideIndex}` and focuses the slide
  (without `index`: right after the current slide)
- `moveSlide {slideId, toIndex}` moves a slide to a position (0-based); its id and links stay
- `duplicateSlide {}` copies the **current** slide (`goToSlide` first), `deleteSlide {slideId}`,
  `goToSlide {slideId}`
- `setSlideName {slideId, name}` (outline label). `setSlideTitle {slideId, title}` replaces the
  slide's title text with plain text (losing its formatting and formulas) and renames the slide:
  to change a title use `setShapeText` on its shape instead
- `setContainerLayout {slideId, shapeId?, layout, gap, alignItems, justifyContent}`
  - layout: `row, column, col-2 ... col-6, ratio-2-1-1, ratio-1-1-2, ratio-1-2, ratio-2-1,
    tiles-2-1, tiles-1-2, tiles-2-1-col, tiles-1-2-col, none`
  - alignItems: `stretch start center end`; justifyContent also `space-between space-around
    space-evenly`
- `setSlideCSSStyle {slideId, style}`: `background, background-image, background-size,
  background-position, border-*, border-radius, box-shadow, padding, width, height, overflow*,
  align-items, justify-content, gap, filter:*, backdrop-filter:*`
- `addGroupLayout {layout, gap, padding}` returns `{shapeId, childShapeIds}`; a flex container
  with the same layout presets (except `none`)

## Content objects

- `addText {text, textType: title | subtitle | body, fontSize, fontWeight, textAlign, color}`
- `addList {items: [{marker?, title?, text?}], columns, startAt}` returns `itemShapeIds`; a
  marker is a number, a glyph or `"icon: <material symbol name>"`; `addListItem {slideId,
  shapeId, marker, title, text}` appends one item
- `addInfographic {infographicType, itemCount (1-20), items[], showImages, showConnectors,
  numbering}`; types: `timeline-horizontal, timeline-vertical, process, comparison, key-points,
  hierarchy, cycle, matrix, funnel, flowchart, modular-cards, journey, relationship, anatomy,
  decision, educational-explanation`. The `cycle` type is tall: prefer `process`, `funnel` or
  `key-points` on 16:9 slides.
- `addTimeline {orientation, itemCount, items[], showImages, showConnectors, numbering}`
- `addButton {label, fontSize, fontWeight, textColor}`; give it behavior with `addInteraction`
- `addIcon {iconName (Material Symbols), color}`, `addSvg {svg}`
- `addDivider {orientation, thickness, color}`: use it for every separator
- `addProgress {variant: bar | circle, min, max, value, variableId, fillColor, progressColor,
  trackColor}`: bind it to a variable for a live meter; pass colors from the theme at creation
- `addShape {shapeType, subTypeName, ...}`: geometric shapes for diagrams and decoration; prefer
  the semantic `add*` tools for content

## Styling

- `setShapeText {slideId, shapeId, text}`: each line break starts a new paragraph
- `setTextStyle {slideId, shapeId, targetType: shape | table | row | column | cell, style}`: keys
  `font-family, font-size, font-weight, font-style, color, text-align, line-height,
  vertical-align, text-decoration-line`
- `setShapeCSSStyle {slideId, shapeId, style}`: background, border, radius, shadow, padding, flex,
  `filter:*`, `backdrop-filter:*`, `transform:*`. Unsupported keys (for example per-side margins
  and borders, `max-width`) are ignored without an error; use gaps, padding and
  `border-width: "0 0 0 4px"` instead.
- `setShapePositioningMode {slideId, shapeId, positioningMode: inherit | flow | absolute}`
- `setShapeInitiallyHidden {slideId, shapeId, initiallyHidden}`
- `duplicateShape`, `deleteShape`, `moveShapeForward`, `moveShapeBackward`, `moveShapeToFront`,
  `moveShapeToBack`, `selectShape`: all `{slideId, shapeId}` (the slide is shown first);
  `selectShapes`, `deselectAll`, `undo`, `redo`

Theme variables: `rgb(var(--aie-accent-0-rgb))` (primary) through `--aie-accent-7-rgb`,
`rgb(var(--aie-title-rgb))`, `rgb(var(--aie-subtitle-rgb))`, `rgb(var(--aie-body-rgb))`,
`rgb(var(--aie-slide-bg-rgb))`; lightness steps `rgb(var(--aie-accent-2-rgb-l90))` (steps 90, 80,
70, 60, 40, 30, 20, 10; absolute lightness, and there is no l50); alpha as `rgba(var(--aie-accent-0-rgb), 0.5)` (never `rgb(var(--x) /
0.5)`); fonts `var(--aie-title-font-family)`, `var(--aie-body-font-family)`.

## Media

Image sources: `source` (a resource filename or an http(s) URL), `dataUrl`, or `base64` plus
`mimeType`; `objectFit: contain | cover | fill | scale-down | none`.

- `addImage`, `replaceImage {slideId, shapeId, ...}`, `addImagePlaceholder`
- `setContainerBackgroundImage {slideId, shapeId?, source, backgroundSize, backgroundPosition}`
- `addAudio {source, controls, autoplay}`, `addVideo {source, controls, autoplay}`,
  `addVideoPlaceholder`
- `addEmbeddedVideo {provider: youtube | vimeo | dailymotion | loom, url, controls, autoplay}`
- `addModel3D {source, fileName (.glb/.gltf)}`

## Questions

`addQuestion {questionType, answers[], name}` returns the shape id. Put the question text in its
own `addText` above it.

questionType: `multiple-choice, multiple-response, true-false, fill-text-entry, essay,
dropdown, slider, sequence, matching, hotspot, label, fill-blanks, multiple-dropdown,
drag-words`.

`setQuestionAnswers {slideId, shapeId, ...}` by type:

- multiple-choice, multiple-response: `answers: [{text, correct}]` (at least 2); true-false:
  exactly 2 answers
- dropdown: `answers: [{text, correct}]` (plain text options)
- fill-text-entry: `answers` = every accepted spelling, all `correct: true` (plain text; matching
  is case-sensitive unless `caseSensitive: false`); essay: cannot be graded automatically
- slider: `correctValue, min, max, step`; sequence: `items[]` in the correct order (at least 2)
- matching: `pairs: [{source, target}]` (2 to 6 pairs)
- fill-blanks and drag-words: `sentence` with `___` per blank, plus `blanks[]`; drag-words also
  `distractors[]`
- multiple-dropdown: `sentence` plus `dropdowns: [{correct, alternatives[]}]`
- hotspot: `hotspots: [{shape: point | rectangle, x, y, width, height}]` (0-100 %), needs an image
- label: `labels: [{text, x, y}]` plus `distractors`

`setQuestionSettings {slideId, shapeId, ...}`: `mode: graded | survey`, `points` (1-1000),
`maxAttempts` (0 = unlimited), `allowResubmission`, `feedback: none | correct-incorrect |
submitted`, `correctFeedback`, `incorrectFeedback`, `submittedFeedback`, `showAnswerKey`,
`answerExplanation`, `navigation: next-slide | stay`, `navigationDelay` (seconds),
`showConfetti`, `confettiEffect: basic | fireworks | stars | school-pride | snow | fountain |
explosion | rain`, `shuffleAnswers`, `caseSensitive` (new fill-text-entry questions are
case-sensitive). Read back with `getQuestionsInfo {scope: "all-slides", includeAnswers: true}`
(without `scope` only the current slide is read; the value has a hyphen) and `getQuestionInfo`.

## Charts and tables

- `addChart {chartType: bar | column | line | pie | doughnut | polarArea | radar | scatter |
  bubble, title, labels[], datasets: [{label, data[]}]}`; `setChartText {title, labels,
  datasetLabels, xAxisName, yAxisName}`; `getChartInfo`
- `setChartData {slideId, shapeId, title?, labels[], datasets: [{label, data[]}]}` replaces the
  numbers of an existing category chart (a template's sample figures) and keeps its type and
  style; one number per label per series; pie and doughnut take one series; scatter and bubble
  are not supported
- `addTable {rows, columns, preset: basic | header | sideHeader, headerRow, headerColumn, csv |
  data[][]}`; `setTableStyle`, `setTableCellsText {cells: [{rowIndex, columnIndex, text}]}`,
  `editTableStructure {action, direction}`, `getTableInfo`. Text styles for tables go through
  `setTextStyle` with `targetType: row | column | cell`.

## Variables and interactions

- `createVariable {name, type: number | text | boolean, defaultValue}` returns the variable id;
  `getVariables`, `updateVariable`, `deleteVariable`
- `addInteraction {slideId, shapeId?, eventType, actionType, actionData?, condition?}`; no
  `shapeId` means a slide-level interaction such as `slide-enter`
  - eventType: `click, mouse-enter, mouse-leave, double-click, slide-enter, timer (slide;
    timerSeconds 1-3600), question-correct, question-incorrect, question-answered, media-ended`
  - actionType: `next-slide, previous-slide, go-to-slide, go-to-slide-at-index,
    go-to-first-slide, go-to-last-slide, pause-presentation, resume-presentation,
    finish-presentation, play-media, pause-media, toggle-play-pause-media, mute-media,
    unmute-media, toggle-media-mute, show, hide, toggle-visibility, show-tooltip, submit,
    open-dialog, open-url, play-confetti, adjust-variable, go-to-random-slide, set-text`
  - actionData: `slideId` or `slideIndex`; `targetShapeId` (media and visibility); `url`;
    `title` and `message` (dialog); `content` and `backgroundColor` (tooltip); `confettiType`;
    `variableId, operation: set | add | subtract | toggle | reset | random, operand`;
    `slideIds` (random slide); `targetShapeId, text` (`set-text`; `{{variable}}` placeholders
    stay live). Operands are typed: `{"kind":"literal","value":10}` or
    `{"kind":"variable","variableId":"..."}`.
  - condition: `{match: all | any, rules: [{variableId, operator: eq | ne | gt | gte | lt | lte |
    contains | empty | notEmpty | isTrue | isFalse, operand}]}`
- `getInteractions {slideId, shapeId?}`, `updateInteraction`, `removeInteraction`
- A show, hide, toggle or media action cannot target the shape it is attached to.
  `question-correct` and `question-incorrect` attach only to graded question shapes,
  `media-ended` only to audio or video shapes, and `slide-enter` only to slides (omit `shapeId`).
- `go-to-slide` stores a slide id: after deleting and re-creating the target, update the link.

## Player and tracking settings

- `setCompletionCriteria {condition: sc | sv, value, unit: p | a}` (score or slides viewed;
  percent or absolute), `setSuccessCriteria` (same arguments), `setMaxAttempts {attempts}`,
  `setResumeOption {option: ask_resume | always_resume | always_restart}`
- `setPlayerToolbarVisibility {visible}`, `setPlayerButton {buttonId, visible}`; button ids:
  `ssb` slides panel, `spb` progress bar, `spsb` previous, `snsb` next, `srb` review, `strb` try
  again, `sfnb` finish early, `ssmb` submit, `sfsb` fullscreen, `sab` autoplay, `sshb` share,
  `ssn` slide number
- `setPlayerMode {mode: presentation | quiz | game}`, `setNavigationLock {locked}`,
  `setSlideAutoAdvance {slideId, seconds}` (0 turns it off)
- `setBackgroundMusic {source, name?, volume?}` (an audio resource or HTTPS URL) loops one track
  across the deck; it starts muted until an `unmute-media` action (put it first on the Start
  button); `remove: true` clears it
- Text can show `{{variable name}}`, `{{$score}}` or `{{$timeLeft}}` in the player.

## Read-only

`getPresentationInfo`, `getPresentationSettings`, `getPresentationBackgrounds`, `getSlideInfo
{slideIndex}`, `getSlideCSSStyle`, `getShapeCSSStyle`, `getSupportedQuestionTypes`,
`getSupportedChartTypes`, `getQuestionsInfo`, `getChartInfo`, `getTablesInfo`.

## Not available through the tools

Entrance and exit animations, slide transitions, narration or text-to-speech, music generation,
custom themes (own colors and fonts) and exporting (SCORM, PDF, video). Templates bring their own
animations and transitions with their layouts. Tell the user when a request needs one of these:
they can do it in the editor.
