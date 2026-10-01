# Building a deck from blank slides

The fallback when no template fits (`listTemplates` returned nothing suitable) or when the user
asks for a deck with no template. A deck built from a template looks more designed and is
faster, so try `listTemplates` first (see `SKILL.md`). The interactivity and player settings in
sections 4 and 5 also apply to decks built from templates.

## 1. Plan the deck

Before any editing call, write a short slide-by-slide plan: purpose of each slide, the native
component that carries it, and the interaction if any. Pick components from this palette:

| Content need | Tool |
|---|---|
| Agenda, tips, key ideas | `addList` (a row of cards; markers can be `"icon: <material symbol>"`) |
| Process, cycle, funnel, hierarchy, comparison | `addInfographic` (see `references/tools.md`) |
| History, milestones | `addTimeline` |
| Numbers, trends, shares | `addChart` |
| Structured facts, specs | `addTable` with `csv` (never a markdown table) |
| Video | `addEmbeddedVideo` (YouTube, Vimeo, Loom) |
| Knowledge check | `addQuestion` (use the `create-quiz` skill) |
| Reveal on click | `addButton` + `setShapeInitiallyHidden` + `addInteraction` |

A strong default for a ~12 slide lesson: title, objectives, 2-4 concept slides (text + image,
infographic, timeline or chart as the content demands), an exploration slide, 3-5 questions of
different types, results, summary. For a live lecture lean on visuals; for an assessment lean
on questions.

## 2. Set up the deck

In order: `setPresentationName`, `setPresentationLanguage` (BCP-47, for example `vi-VN`),
`setSlideSize` if not 16:9, `setPresentationPositioningMode` with `auto` (new blank decks start
in freeform; flow layout keeps content aligned), then `getPresentationInfo` to read the theme.
Read `references/design.md` before building: it explains how to make the deck look designed
rather than "text on a background".

## 3. Build slides

`addSlide` with `slideType: "blank"`, then `add*` tools, using `addGroupLayout` and
`setContainerLayout` for structure. Rules that prevent the common failures:

- **Always pass `index` to `addSlide`.** Without it the slide can land before the current one.
- **Inserts go into the current slide and into the selected group.** `addSlide` focuses the new
  slide; use `goToSlide` to return to a slide. After `addGroupLayout` the group is selected, so
  the next insert lands inside it. To fill a specific column call `selectShape <column id>`
  right before each insert and check `parentShapeId` in the result; `deselectAll` returns to the
  slide root. `addTable` always goes to the slide root.
- **Ids are numbers unique only within one slide.** Always carry `slideId` and `shapeId`
  together and take them from tool results; never guess.
- **Tools without a `slideId` act on the current slide** (`setTextStyle`, `addChart`,
  `setChartText`, every insert). Call `goToSlide` right before them.
- **Use theme variables for colors and fonts**: `rgb(var(--aie-accent-0-rgb))` (primary),
  `rgb(var(--aie-accent-2-rgb))`, `rgb(var(--aie-title-rgb))`, `rgb(var(--aie-body-rgb))`,
  `var(--aie-title-font-family)`, so a later theme change restyles the whole deck.
- **Keep text slide-sized**: titles up to 8 words, bullets up to about 15 words, one idea per
  slide. Put depth into a follow-up slide, a tooltip or dialog.
- A new blank deck's first slide already holds a title shape (id 1) and an empty body
  placeholder (id 2). Fill them with `setShapeText` instead of adding duplicates.
- Charts: size them afterwards with `setShapeCSSStyle` (`width`, `height`); `addChart` stores
  640 x 360 regardless of the parameters. Tables take `csv` or `data`.
- Images: use `addImage` with a public HTTPS `source`, or `addImagePlaceholder` when the user
  will add the picture. Do not invent image URLs; if you do not have a real, relevant one, use a
  placeholder, an icon or an `addSvg` drawing and tell the user.
- `addList` lays its items out in a row; for a vertical bullet list use a text shape with
  several paragraphs.

After each slide, glance at `getPresentationState` with `scope: "current_slide"` before
building on it. Layout mistakes compound.

## 4. Interactivity (optional, when it helps learning)

- Reveal: create the shape, `setShapeInitiallyHidden`, then a button with
  `addInteraction` (`eventType: "click"`, `actionType: "show"`, `actionData.targetShapeId`).
  A show/hide action cannot target the shape it is attached to; put the button in a group and
  target the group.
- Score and branching: `createVariable`, then `addInteraction` with `adjust-variable` and
  `go-to-slide` plus a `condition`. Operands are typed objects: `{"kind":"literal","value":10}`,
  never a bare number. Details in `references/tools.md`.
- Questions and variables: use the `create-quiz` skill.

## 5. Player and tracking settings

For graded e-learning set `setCompletionCriteria`, `setSuccessCriteria`, `setMaxAttempts` and
`setResumeOption`; show or hide player buttons with `setPlayerButton`. For a live lecture leave
navigation free.
