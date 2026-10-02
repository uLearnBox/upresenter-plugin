---
name: edit-presentation
description: "Review, restyle or change an existing uPresenter presentation: fix wording, reorder or add slides, adjust layout and styling, summarize the deck, or undo a change. Use when the user asks to edit, improve, restyle, shorten, summarize or fix a presentation that already exists in their uPresenter account (for example sửa bài giảng, mejorar la presentación, 修改演示文稿). Translation has its own skill, localize-presentation, and a full audit is review-presentation."
---

# Edit an existing presentation in uPresenter

Edits happen in the user's open editor tab, so a presentation must be open there. Work in small,
checked steps and keep the user's content unless they ask for a change.

## 1. Find and open the presentation

1. `listOpenEditorContexts`: if the presentation is open, continue.
2. Otherwise `listPresentations` (use `nameContains` to narrow it down) and give the user the
   `editorUrl` of the right one. Ask them to open it and keep the tab open, then call
   `listOpenEditorContexts` again. If several editors are open, pass the `sessionId` of the right
   one to every tool.

Call tools one at a time. Tell the user not to click around in the editor while you edit; a
click changes the current slide and later edits can land on the wrong slide.

## 2. Read before changing

- `getPresentationState {scope: "presentation_basic"}` for the structure, then `"all_slides"` or
  `"current_slide"` for detail. Take slide and shape ids from the result; ids are unique only
  within a slide, so always pass `slideId` and `shapeId` together.
- `getPresentationInfo`, `getPresentationSettings` and `getQuestionsInfo {scope: "all-slides",
  includeAnswers: true}` for settings and quizzes (without `scope` only the current slide is read).
  `getPresentationInfo` also says whether the deck is `slide` or `fluid` (a scrolling document of
  cards, see `create-presentation`, `references/fluid.md`): in a fluid deck keep to flow layout, do
  not place shapes by x/y, and add cards from fluid templates only.

For a "summarize" request, read everything and answer in chat; change nothing. For a "review",
"check" or "is it ready" request use the `review-presentation` skill: it measures and looks at the
slides, and reports before changing anything.

## 3. Make the change

| Request | Tools |
|---|---|
| Change wording | `setShapeText {slideId, shapeId, text}` (a line break starts a new paragraph) |
| Restyle text | `setTextStyle {slideId, shapeId, style}` (font, size, weight, color, alignment, line height) |
| Restyle a shape or slide | `setShapeCSSStyle`, `setSlideCSSStyle` with theme variables such as `rgb(var(--aie-accent-0-rgb))` |
| Rearrange content | `setContainerLayout`, `moveShapeForward` / `Backward` / `ToFront` / `ToBack`, `duplicateShape`, `deleteShape`, all with `{slideId, shapeId}` |
| Add a designed slide | Find the template of the deck's style: the one in `listTemplates` whose `themeId` equals `getPresentationInfo`'s `themeId` (usually its `templateId`, but not after a style switch). Then `getTemplateLayouts {templateId}`, `insertTemplateSlides {templateId, slides: [{tag}], index}` and fill the returned `slots` with `setShapeText` |
| Add a slide no layout carries | Compose it (`create-presentation` `references/composed-slides.md`): in a deck made from a template, shapes added without that skeleton land on top of each other |
| Update a chart's numbers | `setChartData {slideId, shapeId, labels, datasets}` |
| Restyle the whole deck | Pick a template with `listTemplates`, then `insertTemplateSlides {templateId, applyThemeFromTemplate: true}` (no slides) switches the theme, the colors and fonts of every slide, like the Design tab. Slides keep their own layouts and decor |
| Add a blank slide | `addSlide {slideType: "blank", index}` (always pass `index`), then `add*` tools |
| Reorder slides | `moveSlide {slideId, toIndex}` (0-based); the slide keeps its id, so go-to-slide links still reach it |
| Copy or remove slides | `duplicateSlide` copies the current slide (`goToSlide` first), `deleteSlide {slideId}` |
| Rename the deck or a slide | `setPresentationName`, `setSlideName` (the outline label). To change a slide's title text use `setShapeText` on the title shape: `setSlideTitle` replaces it with plain text and loses its formatting and formulas |
| Interactions, games | `create-presentation` `references/interactions.md` and `references/games.md` |
| Change language | Use the `localize-presentation` skill: it translates every text, quiz answer and feedback, and sets the presentation language that drives the player and text direction |
| Slide size | `setSlideSize` (fixed slides only; a fluid presentation has no slide size, its card width is `setFluidMaxWidth`) |
| Slides to fluid, or fluid to slides | Not a setting: the layouts of the two kinds differ, and `setLayoutType` alone leaves a slide deck under another name. Build a new presentation of the other kind from a template of that kind (`create-presentation`, `references/fluid.md`), and move the content over |

Layouts inserted from a template follow the deck's theme; the filling rules (every slot, within
`maxChars`, no sample text left) are in `create-presentation` `references/template-layouts.md`.
The design principles are in the `create-presentation` skill (`references/design.md`): one
palette with one accent, background rhythm, large titles, generous margins. Colors and fonts
come from the theme variables so the whole deck stays consistent.

Math is stored as formulas. `getPresentationState` shows them inside `text.content` as LaTeX
source and lists them in `text.formulas`; `setShapeText` replaces the whole text, so wrap every
formula again as `[[katex:LATEX]]` when you rewrite it (`create-presentation`,
`references/math-formulas.md`), or the formula turns into plain text with raw LaTeX.

Rules that prevent mistakes:

- Shape ids repeat on every slide: pass `slideId` with every `shapeId`. Inserts (`add*`) go to the
  **current slide**: call `goToSlide` right before them.
- Inserts go into the current slide and into the selected group, and the new shape is selected
  afterwards. `selectShape {slideId, shapeId}` the container before **each** insert into it, and
  check `parentShapeId`; `deselectAll` returns to the slide root.
- Animations, transitions, narration and custom themes have no tools: say so and point the user to
  the editor.
- Do not delete or rewrite content the user did not ask you to touch. For a bulk change (for
  example translating every slide) go slide by slide and report progress.
- `undo` reverts the last change if something went wrong; `redo` reapplies it.

## 4. Verify and report

After a change run `reviewSlides {slideIds: [the changed slides]}`: new or longer text can spill out
of its card, and a rewritten text can lose its formulas. Fix what it reports, look at the slide with
`getSlideImage {presentationId, slideId}`, and read `getPresentationState` for the affected slide to
confirm the result. Tell
the user exactly what changed (slide numbers and what was edited) and suggest previewing the
deck in the player. If part of the request could not be done with the available tools, say so.
