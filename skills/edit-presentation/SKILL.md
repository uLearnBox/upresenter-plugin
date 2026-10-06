---
name: edit-presentation
description: "Review, restyle or change an existing uPresenter presentation: fix wording, reorder or add slides from the deck's own template layouts, adjust layout and styling, swap images, set slide timing, background music and player buttons, summarize the deck, or undo a change. Use when the user asks to edit, improve, restyle, shorten, summarize or fix a presentation that already exists in their uPresenter account (for example sửa bài giảng, mejorar la presentación, 修改演示文稿, modifier la présentation). Translation has its own skill, localize-presentation, and a full audit is review-presentation."
---

# Edit an existing presentation in uPresenter

Edits happen in the user's open editor tab. Work in small, checked steps and keep the user's content
unless they ask for a change.

## Ground rules

- A presentation must be **open in an editor tab** and stay open; ask the user not to click around
  in it (a click changes the current slide and later edits can land elsewhere). "No presentation is
  open in an editor tab" means: ask them to open it, then retry once.
- Call tools **one at a time**. Shape ids repeat on every slide: always pass `slideId` and `shapeId`
  together, both from tool results. `add*` inserts go to the **current slide** (and the selected
  group): `goToSlide` right before them, and `selectShape` the container before each insert into it.
- A new slide starts from a **template layout** (`listTemplates`, `getTemplateLayouts`,
  `insertTemplateSlides`), not from a blank slide; compose by hand only what no layout carries.
- After every change run `reviewSlides {slideIds: [...]}` and look with `getSlideImage`.
- Not possible with the tools: animations, slide transitions, narration/TTS, exports. Say so and
  point the user to the editor.
- Fluid decks: never convert with `setLayoutType`; see `create-presentation` `references/fluid.md`.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## 1. Find and open the presentation

1. `listOpenEditorContexts`: if the presentation is open, continue.
2. Otherwise `listPresentations` (`nameContains` narrows it) and give the user the `editorUrl`. Ask
   them to open it and keep the tab open, then call `listOpenEditorContexts` again. With several
   editors open, pass the `sessionId` of the right one to every tool.

## 2. Read before changing

- `getPresentationState {scope: "presentation_basic"}` for the structure, then `"all_slides"` or
  `"current_slide"` for detail.
- `getPresentationInfo`, `getPresentationSettings`, and `getQuestionsInfo {scope: "all-slides",
  includeAnswers: true}` (without `scope` only the current slide is read; `getQuestionInfo
  {slideId, shapeId}` reads one question). `getPresentationInfo` says whether the deck is `slide`
  or `fluid`: in a fluid deck keep to flow layout, do not place shapes by x/y, and add cards from
  fluid templates only.

For "summarize", read everything and answer in chat; change nothing. For "review", "check" or "is it
ready" use the `review-presentation` skill.

## 3. Make the change

| Request | Tools |
|---|---|
| Change wording | `setShapeText {slideId, shapeId, text}` (a line break starts a paragraph) |
| Restyle text | `setTextStyle {slideId, shapeId, style}` (font, size, weight, color, alignment, line height) |
| Restyle a shape or slide | `setShapeCSSStyle`, `setSlideCSSStyle` with theme variables such as `rgb(var(--aie-accent-0-rgb))` |
| Rearrange content | `setContainerLayout`, `moveShapeForward` / `Backward` / `ToFront` / `ToBack`, `duplicateShape`, `deleteShape`, all with `{slideId, shapeId}` |
| **Add a designed slide (template first)** | The template of the deck's style is the one in `listTemplates` whose `themeId` equals `getPresentationInfo`'s `themeId` (usually its `templateId`, not after a style switch; filter with `query`, `category`, `language`, `layoutType`). `getTemplateLayouts {templateId}` (optionally `tag`), `insertTemplateSlides {templateId, slides: [{tag} or {slideId}], index}`, then fill every returned `slot` with `setShapeText` (within `maxChars`). Slots with a `sample` hold the template's sample copy: replace or `deleteShape` each one. Pass `templateId` on every insert |
| Add a slide no layout carries | Compose it (`create-presentation` `references/composed-slides.md`): in a template deck, shapes added without that skeleton land on top of each other |
| Add a blank slide | Last resort: `addSlide {slideType: "blank", index}` (always pass `index`), then `add*` tools |
| Update a chart's numbers | `setChartData {slideId, shapeId, labels, datasets}` (bar, column, line, pie, doughnut, polarArea, radar only) |
| Update a table | `setTableCellText` (one cell), `setTableCellsText` (many), `editTableStructure` |
| Restyle the whole deck | A template's look: `listTemplates`, then `insertTemplateSlides {templateId, applyThemeFromTemplate: true}` (no slides). A built-in or own theme: `listThemes`, then `applyTheme {themeId}`. The user's brand: `createTheme` with their colors and fonts (`create-presentation` `references/design-systems.md`), which also applies it. Each switches colors and fonts of every slide, like the Design tab; layouts and decor stay |
| Page behind the slides | `setPresentationBackground {color}` (CSS color, gradient or theme variable) |
| Swap or fix a picture | `replaceImage {slideId, shapeId, source}` (public HTTPS URL or resource; never invent URLs); `convertImageFormat {slideId, shapeId, format: png \| jpg \| webp}` to shrink or convert a stored image; `upscaleImage` only when offered (AI media, credits) |
| Slide timing | `setSlideAutoAdvance {slideId, seconds}` moves on by itself after N seconds (0 = off); for any other action on a timer use a `timer` interaction |
| Background music | `setBackgroundMusic {source, volume?, name?}` loops one track across the deck; it starts muted until an `unmute-media` action (first button), `remove: true` clears it. WAV loops cleanly, MP3 can gap |
| Player buttons and mode | `setPlayerButton {buttonId, visible}` (`ssb` slides panel, `spb` progress bar, `spsb` previous, `snsb` next, `srb` review, `strb` try again, `sfnb` finish early, `ssmb` submit, `sfsb` fullscreen, `sab` autoplay, `sshb` share, `ssn` slide number), `setPlayerToolbarVisibility`, `setPlayerMode`, `setNavigationLock` |
| Reorder slides | `moveSlide {slideId, toIndex}` (0-based); the slide keeps its id, so go-to-slide links still reach it |
| Copy or remove slides | `duplicateSlide` copies the current slide (`goToSlide` first), `deleteSlide {slideId}` |
| Rename the deck or a slide | `setPresentationName`, `setSlideName` (outline label). Change a slide's title text with `setShapeText` on the title shape: `setSlideTitle` replaces it with plain text and loses formatting and formulas |
| Interactions, games | `getInteractions`, `addInteraction`, `updateInteraction`, `removeInteraction` (`create-presentation` `references/interactions.md`, `references/games.md`, or the `design-interactions` / `build-game` skills if installed) |
| Change language | The `localize-presentation` skill |
| Slide size | `setSlideSize` (fixed slides only; fluid card width is `setFluidMaxWidth`) |
| Slides to fluid or back | Not a setting. Build a new presentation of the other kind from a template of that kind (`create-presentation` `references/fluid.md`) and move the content over |

Layouts inserted from a template follow the deck's theme; the filling rules (every slot, within
`maxChars`, no sample text left) are in `create-presentation` `references/template-layouts.md`. The
design principles (one palette with one accent, background rhythm, large titles, generous margins)
are in `create-presentation` `references/design.md`. Colors and fonts come from the theme variables
so the whole deck stays consistent.

Math is stored as formulas. `getPresentationState` shows them inside `text.content` as LaTeX and
lists them in `text.formulas`; `setShapeText` replaces the whole text, so wrap every formula again
as `[[katex:LATEX]]` when you rewrite it (`references/math-formulas.md` of `create-presentation`),
or it turns into plain text with raw LaTeX.

Do not delete or rewrite content the user did not ask you to touch. For a bulk change go slide by
slide and report progress.

### Undo scope

`undo` and `redo` step through the editor's history **one action per call**, and that history is
shared with whatever the user does in the tab. A multi-call change (a template insert, then ten
`setShapeText` calls) needs as many undos, so prefer to fix forward when only part is wrong. Before
undoing, read `getPresentationState` for the slide so you know what the last step was, and again
afterwards to confirm. `convertImageFormat` and `upscaleImage` keep the original image, so they can
be undone or restored. Undo does not bring back anything outside the open editor.

## 4. Verify and report

After a change run `reviewSlides {slideIds: [the changed slides]}` (new or longer text can spill out
of its card, and a rewritten text can lose its formulas, and inserted template layouts can still
carry sample text). Fix what it reports, look at the slide with `getSlideImage {presentationId,
slideId}`, and read `getPresentationState` for the affected slide. Tell the user exactly what
changed (slide numbers and what was edited) and suggest previewing in the player. If part of the
request could not be done with the available tools, say so.
