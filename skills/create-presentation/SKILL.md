---
name: create-presentation
description: "Build a complete, professionally designed interactive presentation or e-learning lesson in uPresenter from a topic, outline, or source material, using the gallery of designed templates. Use when the user wants to create or draft slides, a lesson, a lecture, a pitch, a report or a training module in uPresenter, or asks Claude/ChatGPT to \"make a presentation about ...\" in their uPresenter account."
---

# Create a presentation in uPresenter

uPresenter is a web editor for interactive presentations and e-learning. It has a gallery of
**templates**: each one a design language (theme with colors and fonts, plus a library of layouts
in that style). This skill builds a deck the way a template is built, with real content instead of
placeholders: choose the design language, set the deck's style, plan only the slides the request
needs, insert the matching layouts, then replace every placeholder with real content. The
result should be a deck the user would be proud to present: accurate content, one visual identity,
a clear structure, and interactivity where it helps learning.

The two rules that make the difference between a designed deck and "text on a background":

1. **The design comes from a template**, not from styling blank slides. Never restyle what a
   layout already does.
2. **The content comes from the request**, not from the template. The user's prompt decides which
   slides exist and what they say. Insert only the layouts the outline needs, never the whole
   library, and leave no sample text behind.

## How the tools work (read once)

- Editing tools act on a presentation **open in an editor tab of the user's browser**. The tab runs
  every call, so it must stay open for the whole build, and the user should not click around in it
  meanwhile (a click changes the current slide and later edits land elsewhere).
- Tools that read or list (`listPresentations`, `listTemplates`, `getTemplateLayouts`,
  `getNewPresentationLink`, `listOpenEditorContexts`) work without an editor.
- Call tools **one at a time**, never in parallel, and use the ids returned by earlier calls.
  Slide ids and shape ids are numbers that are unique only within a slide: always pass `slideId`
  and `shapeId` together, taken from tool results, never guessed.
- If a call fails with "No presentation is open in an editor tab", ask the user to open the
  presentation and retry. Do not try again in a loop.

## Workflow

### 1. Brief

Work out: topic and source material (a document the user pasted, or your own knowledge), audience
and level, language, purpose (live lecture, self-paced lesson, assessment, pitch, report), the
length the user asked for, and the mood if they gave one. Infer sensible defaults and ask only what
would really change the deck. Content accuracy matters more than anything else: if the user gave a
source, stay faithful to it.

### 2. Choose the design language: pick a template

Every template is one **design language**: a theme (colors and fonts, what the Design tab
holds) plus a library of layouts drawn in that style ("Swiss Editorial", "Duotone Mono",
"Warm Classroom"...). Choosing the language comes first, before any slide exists.

1. `listTemplates` with a `query` or `category` that matches the genre or mood ("pitch", "lesson",
   "report", "quiz", "dark") and the `language` of the deck. Read each result's `description`,
   `categories`, `layouts` (tag counts), `language`, `palette` and `fonts`. The tags the plan needs
   must exist in the template: a `timeline`, a `chart`, `list-5`, the `question-*` types of a quiz.
2. Pick **one** template for the whole deck and tell the user which and why in one line ("Swiss
   Editorial: a clean grid that suits a business plan"). If nothing fits, relax the filters or pass
   `includeLegacy: true`; if still nothing, build from blank slides with
   `references/build-from-blank.md`.

Details on reading templates and tags: `references/template-layouts.md`.

### 3. Plan the deck: only the slides the prompt needs

Write the outline before any editing call. One line per slide: the **message** (what the viewer
should take away), the **layout tag** that carries it, and the **real content** in short form
(the numbers, names, steps and sentences you will use).

- Length: the user's number if they gave one; otherwise the genre default in
  `references/deck-genres.md`, trimmed to what the content needs. A lesson defaults to 10-14
  slides, a pitch to 10-12.
- Start from the genre's running order, **drop what the brief does not need, add what it does**.
  Do not add slides to show off layouts.
- Choose each tag by the **shape of the content** (`template-layouts.md`, section 2): three parallel
  points are a `list-3`, a sequence in time a `timeline`, figures a `chart` or `statistic`, two
  options a `comparison`, a question a `question-*`. A chart, timeline, statistic or quote needs
  real data: if you have none, use a text layout.
- Rhythm: a cover first and an `end` last; no two slides with the same tag in a row; a `section`
  divider only in decks of ten or more slides.
- Make the content real and specific: concrete numbers, names and examples from the source or
  your knowledge, headlines that state a claim ("Revenue grows 4.5x in three years"), not topic
  labels ("Revenue").

### 4. Open the deck and set its style

A deck made with "Create Blank" is not empty of design: it starts from a template of its own
(the dialog preselects one) with that template's theme and a starter cover. So whatever deck the
user has, set the style first, then build.

1. `listOpenEditorContexts`. If the user has a deck open, use it. If none is open,
   `getNewPresentationLink` with the `templateId` of the chosen template gives the user a link:
   opening it **creates a presentation from the template** and opens it in the editor. Ask them to
   keep that tab open, then call `listOpenEditorContexts` again. If several editors are open, pass
   the `sessionId` of the right one to every editing tool.
2. `getPresentationInfo` shows the deck's `templateId`. If it is **not** the template you chose,
   switch the deck's style: `insertTemplateSlides {templateId: <chosen>, applyThemeFromTemplate:
   true}` with no `slides` changes only the theme (colors and fonts of every slide), like picking a
   theme in the Design tab. Do it before building, so everything that follows already has the
   chosen look. Pass the chosen `templateId` in **every** `insertTemplateSlides` call: without it
   the tool uses the deck's own starter template.
3. `getPresentationState` with `scope: "all_slides"` shows the starter slide. If the deck's own
   template is the chosen one, keep its cover and fill it. Otherwise it belongs to the other
   template: you will insert the chosen template's `title` layout at index 0 and delete the
   starter slide with `deleteSlide`.
4. Call `setPresentationName` and `setPresentationLanguage` (BCP-47, for example `vi-VN`).

### 5. Read the layouts and pick slides

`getTemplateLayouts {templateId}` once, then choose for every planned slide a concrete `slideId`:
among variants with the same tag take the one whose `maxChars` budgets hold your text, and vary
the variants when a tag repeats (`template-layouts.md`, section 5).

### 6. Insert the planned layouts

`insertTemplateSlides {templateId, slides: [{slideId}, ...], index}` inserts the layouts in one
call, in the planned order, at an exact position (`index` is the position of the first one). A
`tag` instead of a `slideId` takes the first slide with that tag. Insert at index 0 when the
starter cover is replaced, otherwise after the kept cover (index 1), then delete the starter slide
if there is one. Each result lists the new slides with their `slideId`, `slideIndex` and `slots`
(the shapes to fill: `shapeId`, `role`, `maxChars`, `prompt` or `sample`), plus `chartShapeId`,
`tableShapeId`, `questionShapeId`, `entryCount` and `imageSlots` where the layout has them. A cover
you kept was not inserted by you: `goToSlide`, then `getPresentationState` with `scope:
"current_slide"` lists its text shapes (`placeholder`, `maxChars`).

### 7. Fill every slot with real content

Slide by slide, in order. For every slot of every inserted slide:

- `setShapeText {slideId, shapeId, text}` with the real text, **within `maxChars`**. Follow the
  slot's `role` and `prompt` (`template-layouts.md`, section 3): a `label` is 1-3 words, a `title`
  is a claim, an `item-title` a few words, an `item-content` one or two sentences.
- A `chart` layout: `setChartData` with the real labels and numbers, then the insight text.
- Slots with a `sample` (figures, labels, timeline and process entries `entry-*`): overwrite every
  one. Sample copy looks real and is not marked as a placeholder, so skipping it leaves the
  template's figures in your deck.
- Tables: `editTableStructure` to the shape of your data, then `setTableCellsText`. Questions: `setQuestionAnswers` and
  `setQuestionSettings` (the `create-quiz` skill). Images: keep the template's sample picture, or
  `replaceImage` with a real public HTTPS URL the user gave you; never invent URLs.
- A slot the content has no use for: `deleteShape`, rather than leaving the prompt visible.
- Do not touch colors, fonts or sizes. If the layout cannot hold the content, change the layout
  (another variant or tag), not the styling.

If a call fails, read the error and fix the call. Do not retry the same arguments.

### 8. Interactivity and quizzes (when it helps learning)

Reveal buttons, score variables and branching are in `references/build-from-blank.md` (sections 4
and 5), and they work on template slides too. Quiz questions use the template's `question-*`
layouts: follow the `create-quiz` skill. For graded e-learning also set `setCompletionCriteria`,
`setSuccessCriteria`, `setMaxAttempts` and `setResumeOption`.

### 9. Audit

`getPresentationState` with `scope: "all_slides"` and `getQuestionsInfo`, and check:

- no shape has a `placeholder` field, and no figure, label, table cell or timeline entry still holds
  the template's sample (read the texts of each slide: dates, percentages and numbers from the
  sample must match your content);
- every text fits its `maxChars`; the headline of each slide states its message;
- the order matches the outline; every graded question has a correct answer;
- no two neighbouring slides repeat the same layout without a reason.

Fix what the audit finds before reporting.

### 10. Report

Tell the user, one line per slide, what was created and which template was used; which features
were used (charts, questions, interactions); and anything that needs their input: facts to confirm,
pictures to replace (list the slides whose sample picture stays), accents to check in a non-English
deck. Suggest next steps: preview in the player, share, export for an LMS, or restyle with another
template.

## Troubleshooting

| Symptom | Fix |
|---|---|
| "No presentation is open in an editor tab" | Ask the user to open the presentation and keep the tab open. |
| "More than one editor tab is open" | Call `listOpenEditorContexts` and pass the `sessionId`. |
| `listTemplates` returns nothing | Remove the `query` or `category`, widen `language`, or pass `includeLegacy: true`. |
| `insertTemplateSlides` says a slide was not found | Take the `slideId` or `tag` from `getTemplateLayouts` of the same template. |
| The inserted slides look different from the rest | The deck's style and the layouts come from different templates. Switch the style with `insertTemplateSlides {templateId, applyThemeFromTemplate: true}` (no slides) and pass the chosen `templateId` on every insert. |
| A slide still shows "Click to add title" | A slot was not filled: fill it or `deleteShape` it, then audit again. |
| Text spills out of its card | Shorten it to the slot's `maxChars`, or move the slide to a variant with a larger budget. |
| Edit landed on the wrong slide | A tool without `slideId` ran after the current slide changed. Pass `slideId` and `shapeId` every time. |
| "Editor tool execution timed out" | Check the state before retrying so nothing is inserted twice. |
| Bad argument errors | Read the tool's schema in the tool list and fix the arguments. |
