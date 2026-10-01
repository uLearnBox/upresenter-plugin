---
name: edit-presentation
description: "Review, restyle or change an existing uPresenter presentation: fix wording, reorder or add slides, adjust layout and styling, summarize the deck, or undo a change. Use when the user asks to edit, improve, restyle, translate, shorten, summarize or fix a presentation that already exists in their uPresenter account."
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
- `getPresentationInfo`, `getPresentationSettings` and `getQuestionsInfo` for settings and quizzes.

For a "summarize" or "review" request, read everything and answer in chat; change nothing.

## 3. Make the change

| Request | Tools |
|---|---|
| Change wording | `setShapeText {slideId, shapeId, text}` (a line break starts a new paragraph) |
| Restyle text | `setTextStyle` (font, size, weight, color, alignment, line height) |
| Restyle a shape or slide | `setShapeCSSStyle`, `setSlideCSSStyle` with theme variables such as `rgb(var(--aie-accent-0-rgb))` |
| Rearrange content | `setContainerLayout`, `moveShapeForward` / `Backward` / `ToFront` / `ToBack`, `duplicateShape`, `deleteShape` |
| Add a designed slide | `listTemplates` / `getTemplateLayouts` for the deck's template (`templateId` in `getPresentationInfo`), then `insertTemplateSlides {slides: [{tag}], index}` and fill the returned `slots` with `setShapeText` |
| Update a chart's numbers | `setChartData {slideId, shapeId, labels, datasets}` |
| Restyle the whole deck | Pick a template with `listTemplates`, then `insertTemplateSlides {templateId, slides: [{tag}], applyThemeFromTemplate: true}` switches the theme (colors and fonts) of every slide; remove the helper slide afterwards with `deleteSlide` if you only wanted the theme |
| Add a blank slide | `addSlide {slideType: "blank", index}` (always pass `index`), then `add*` tools |
| Reorder or remove slides | `duplicateSlide`, `deleteSlide`, `goToSlide` |
| Rename the deck or a slide | `setPresentationName`, `setSlideName`, `setSlideTitle` |
| Change language | `setPresentationLanguage`, then `setShapeText` for translated texts |
| Slide size or layout type | `setSlideSize`, `setLayoutType` (fixed slides or fluid page) |

Layouts inserted from a template follow the deck's theme; the filling rules (every slot, within
`maxChars`, no sample text left) are in `create-presentation` `references/template-layouts.md`.
The design principles are in the `create-presentation` skill (`references/design.md`): one
palette with one accent, background rhythm, large titles, generous margins. Colors and fonts
come from the theme variables so the whole deck stays consistent.

Rules that prevent mistakes:

- Tools without a `slideId` act on the **current slide**: call `goToSlide` right before them.
- Inserts go into the current slide and into the selected group. `deselectAll` returns to the
  slide root; `selectShape` a container before inserting into it, and check `parentShapeId`.
- Do not delete or rewrite content the user did not ask you to touch. For a bulk change (for
  example translating every slide) go slide by slide and report progress.
- `undo` reverts the last change if something went wrong; `redo` reapplies it.

## 4. Verify and report

After a change read `getPresentationState` for the affected slide and confirm the result. Tell
the user exactly what changed (slide numbers and what was edited) and suggest previewing the
deck in the player. If part of the request could not be done with the available tools, say so.
