---
name: create-presentation
description: "Build a complete, professionally designed interactive presentation or e-learning lesson in uPresenter from a topic, outline, or source material, starting from the app's gallery of designed templates (listTemplates, getTemplateLayouts, insertTemplateSlides) or, on request, designing it from scratch on a blank deck (own theme, colors and fonts, composed layouts). Builds both kinds of presentation: fixed slides, and fluid ones (a scrolling, responsive document of cards). Use when the user wants to create or draft slides, a lesson, a lecture, a pitch, a report or a training module in uPresenter, or asks Claude/ChatGPT to \"make a presentation about ...\" in their uPresenter account, in any language (for example tạo bài giảng về ..., tạo presentation fluid về ..., crear una presentación sobre ..., 做一个关于...的演示文稿, créer une présentation sur ...)."
---

# Create a presentation in uPresenter

uPresenter is a web editor for interactive presentations and e-learning. It has a gallery of
**templates**: each one a design language (theme with colors and fonts, plus a library of layouts
in that style). This skill builds a deck the way a template is built, with real content instead of
placeholders: choose the design language, set the deck's style, plan only the slides the request
needs, insert the matching layouts, then replace every placeholder with real content. The result
should be a deck the user would be proud to present: accurate content, one visual identity, a clear
structure, and interactivity where it helps learning.

Two rules make the difference between a designed deck and "text on a background":

1. **The design comes from a template**, not from styling blank slides. Start every deck, and every
   extra slide, from `listTemplates` / `getTemplateLayouts` / `insertTemplateSlides`; compose by
   hand only what no layout carries. Never restyle what a layout already does.
2. **The content comes from the request**, not from the template. The user's prompt decides which
   slides exist and what they say. Insert only the layouts the outline needs, and leave no sample
   text behind.

## Ground rules

- **Ask with the host's choice picker, not a paragraph.** When the user must decide (a template, a
  style or theme, a game, a language, a cost), use the host's structured question tool if it has one
  (for example `AskUserQuestion` in Claude Code, `request_user_input` in Codex, or the question picker of
  the Claude and ChatGPT apps): one question per decision, 2 to 4 options each with a one-line
  description (put a preview link in it), the recommended option first and marked "(Recommended)", and
  always room for the user's own answer. Batch several decisions into one call. Only when the host has no
  such tool, give a short numbered list and wait for the reply.
- Execute uPresenter operations through the `upresenter` MCP tools. Do not substitute Computer Use,
  browser automation, DOM scripts or direct HTTP calls unless the user explicitly requests that method.
- If a required tool is missing, use `searchTools`, `describeTool` and `callTool` when available.
  If those gateways are also missing, stop and report incomplete MCP tool discovery with the missing
  tool names. A successful sign-in does not prove that tools loaded; do not ask the user to sign in
  again without an authentication error. An absent editor requires the user to open the editor link,
  not a switch to browser automation.
- Editing tools act on a presentation **open in an editor tab** of the user's browser. The tab runs
  every call: keep it open for the whole build and ask the user not to click around in it (a click
  changes the current slide). If a call says "No presentation is open in an editor tab", ask the
  user to open it and retry once; no loops.
- Call tools **one at a time**, never in parallel, using ids from earlier results. Shape ids repeat
  on every slide: pass `slideId` and `shapeId` together, both taken from tool results. `add*`
  tools insert into the current slide (`goToSlide` first).
- Not possible with the tools: music generation and exports (SCORM, PDF, video). Narration only
  when the connection lists `addNarration` (AI voice, uses credits; see `references/tools.md`).
  Animations and slide transitions are possible (`setShapeAnimation`, `setSlideTransition`; see
  `references/tools.md`), and templates bring their own. Build the rest and tell the user to add the
  others in the editor.
- Fluid is a different kind of deck: never turn a slide deck into fluid with `setLayoutType`
  (`references/fluid.md`).
- Review with `reviewSlides` and `getSlideImage` before reporting (step 9).
- Tool names and arguments: `references/tools.md`. Problems: `references/troubleshooting.md`; the
  rules behind refused calls: `references/pitfalls.md`.
- Motion (slide transitions, shape animations, looping media): only when asked or when the genre
  needs it; follow the `add-motion` skill. Phones: prefer a fluid deck, or a portrait
  `setSlideSize`, and know that `getSlideImage` crops anything beyond 1920 x 1080.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

### 1. Brief

Work out: topic and source material (a document the user pasted, or your own knowledge), audience
and level, language (the language the user writes in, unless they say otherwise), purpose (live
lecture, self-paced lesson, assessment, pitch, report), the length asked for, and the mood. Infer
sensible defaults and ask only what would really change the deck. Content accuracy matters most:
stay faithful to a given source.

**Decide the kind from the words of the request.** uPresenter has **slides** (fixed 16:9 pages, the
default) and **fluid** (a scrolling, responsive document of cards, for phones and reading). If the
user asks for fluid, scrolling, responsive, mobile-first or document-like ("dạng cuộn", "kiểu
fluid", in any language), **read `references/fluid.md` now and follow it where this file differs**:
`listTemplates {layoutType: "fluid"}`, a link that creates a fluid deck, cards instead of slides,
no `maxChars`. When the request does not say, build slides.

For a full game build (lives, timer, boards, escape room) use the `build-game` skill if installed,
otherwise `references/games.md`. For a quiz-heavy deck follow `create-quiz`.

### 2. Choose the design language: pick a template

Every template is one **design language**: a theme (colors and fonts) plus a library of layouts in
that style. Choosing it comes first, before any slide exists.

1. Pick the style the request calls for (`references/styles.md`: topic to style), then
   `listTemplates` with a `query` for the style, genre or topic ("Swiss Editorial", "pitch",
   "lesson", "report"). Filters: `query` searches name, description and categories; `category`
   takes only a gallery category (Business, Education, Game, Quiz, Healthcare, Nonprofit, Design,
   Marketing, Travel); `language` matches the deck's language; `layoutType` is `slide` (default) or
   `fluid`. The gallery has about forty templates and a call shows 20 at most: compare `total` with
   what came back and narrow the search rather than judging the first page. Read each result's
   `description`, `categories`, `layouts` (tag counts), `language`, `palette` and `fonts`. The tags
   the plan needs must exist: a `timeline`, a `chart`, `list-5`, the `question-*` types of a quiz. A
   template with `variableCount` above zero is a game template: its ordinary layouts serve any deck
   in that look, its game screens are for games.
2. **Suggest, do not just pick.** Shortlist two or three templates in one short message: name, what
   it looks like (from `description`, `palette`, `fonts`), why it suits this request, which planned
   layouts it has, and its `previewUrl`. As the **last option** offer to **design it from scratch on
   a blank deck**: name the design language you would use (`references/styles.md`,
   `references/design-systems.md`) with palette and fonts, say the user may give their own brand
   colors and fonts, and that it takes longer. Say which you recommend and ask with the **choice picker** (Ground rules): one
   question "Which design should I use?", the shortlisted templates as options (name and look in the
   description, `previewUrl` included) and "Design from scratch" as the last option, the
   recommendation first. In the same call you may ask the deck length or language when the prompt
   leaves them open. **Wait for the answer** before any editing call. Skip the question only when the user already named a template or
   style, asked for a blank or custom design, or asked you to choose: then pick one and say which and
   why in one line.
3. **One template for the whole deck.** No template in the wanted style: offer the nearest or borrow
   a theme and compose (`references/styles.md`, section 4). If nothing fits, relax the filters
   (legacy packs: `includeLegacy: true`, no theme of their own). Last resort: blank slides
   (`references/build-from-blank.md`).
4. **Design from scratch** (the user's choice, a brand kit, or a style with no template):
   `references/design-from-scratch.md` replaces steps 2 and 4 to 7 (a design language turned into a
   theme with `createTheme`, a planned layout suite, every slide composed and checked by eye); then
   continue at step 8.

Reading templates and tags: `references/template-layouts.md`.

### 3. Plan the deck: only the slides the prompt needs

Write the outline before any editing call. One line per slide: the **message**, the **layout tag**
that carries it, and the **real content** in short form (numbers, names, steps, sentences).

- Length: the user's number, else the genre default in `references/deck-genres.md`, trimmed to the
  content (a lesson 12-16 slides, a pitch 10-12). Start from the genre's running order, drop what
  the brief does not need, add what it does. No slides just to show off layouts.
- Choose each tag by the **shape of the content** (`template-layouts.md`, section 2): three parallel
  points `list-3`, a sequence `timeline`, figures `chart` or `statistic`, two options `comparison`, a
  question `question-*`. Charts, timelines, statistics and quotes need real data; with none, use a
  text layout.
- Rhythm: a cover first and an `end` last; no two slides with the same tag in a row; a `section`
  divider only in decks of ten or more slides.
- Math, physics, chemistry: plan each slide's formulas as LaTeX (`references/math-formulas.md`).
- Be specific: concrete numbers, names and examples; headlines that state a claim ("Revenue grows
  4.5x in three years"), not labels ("Revenue").

### 4. Open the deck and set its style

A deck made with "Create Blank" is not empty of design: it starts from a template of its own with
that template's theme and a starter cover. So set the style first, then build.

1. `listOpenEditorContexts`. If the user has a deck open, use it. If none, `getNewPresentationLink`
   with the chosen `templateId`: opening it **creates a presentation from the template** (a fluid
   one for a fluid template). Ask the user to keep that tab open, then call
   `listOpenEditorContexts` again. With several editors open, pass the right `sessionId` to every
   editing tool.
2. `getPresentationInfo` shows `layoutType` (must match the request), `templateId` (what the deck was
   created from) and `themeId` (the style it wears now). If `themeId` is **not** the chosen
   template's `themeId` (from `listTemplates`), call `insertTemplateSlides {templateId: <chosen>,
   applyThemeFromTemplate: true}` with no `slides`: it changes only the colors and fonts of every
   slide. Do it before building. Pass the chosen `templateId` in **every** `insertTemplateSlides`
   call; without it the tool uses the deck's own starter template.
3. `getPresentationState {scope: "all_slides"}` shows the starter slide. If the deck's template is the
   chosen one, keep its cover and fill it; otherwise insert the chosen `title` layout at index 0 and
   `deleteSlide` the starter.
4. `setPresentationName` and `setPresentationLanguage` with the deck's language code
   (`references/language-codes.md`, for example `vi-VN`, `en-US`). The language sets the player's
   buttons, date format and, for Arabic, Hebrew, Persian and Urdu, the text direction.

### 5. Read the layouts and pick slides

`getTemplateLayouts {templateId}` once, then choose a concrete `slideId` for every planned slide:
among variants with the same tag take the one whose `maxChars` budgets hold your text, and vary the
variants when a tag repeats (`template-layouts.md`, section 5).

### 6. Insert the planned layouts

`insertTemplateSlides {templateId, slides: [{slideId}, ...], index}` inserts all layouts in one call,
in the planned order, at an exact position (`index` = position of the first). A `tag` instead of a
`slideId` takes the first slide with that tag. Insert at index 0 when the starter cover is replaced,
otherwise at index 1 after the kept cover, then delete the starter if needed. Each result lists the
new slides with `slideId`, `slideIndex` and `slots` (`shapeId`, `role`, `maxChars`, `prompt` or
`sample`) plus `chartShapeId`, `tableShapeId`, `questionShapeId`, `entryCount` and `imageSlots`. A
cover you kept was not inserted by you: `goToSlide`, then `getPresentationState {scope:
"current_slide"}` lists its text shapes.

### 7. Fill every slot with real content

Slide by slide, for every slot of every inserted slide:

- `setShapeText {slideId, shapeId, text}` with real text **within `maxChars`**, following the slot's
  `role` and `prompt` (`template-layouts.md`, section 3): a `label` is 1-3 words, a `title` a claim,
  an `item-title` a few words, an `item-content` one or two sentences.
- **Sample text is not a placeholder.** Slots with a `sample` (figures, labels, `entry-*` timeline
  and process entries, chart titles, table cells) look like real copy; every one must be replaced
  or deleted, or the template's figures stay in the deck.
- `chart` layout: `setChartData` with real labels and numbers, then the insight text. Tables:
  `editTableStructure` to the shape of the data, then `setTableCellsText`. Questions:
  `setQuestionAnswers` and `setQuestionSettings` (`create-quiz`).
- Images: keep the template's sample picture, or `replaceImage` with a real public HTTPS URL the
  user gave; never invent URLs. A sample picture of the wrong subject misleads: ask for pictures up
  front, prefer a variant without picture slots, or use an icon. When the connection offers
  `generateShapeImage`, see `template-layouts.md`, section 4 (cost agreement, model, prompts).
- A slot the content has no use for: `deleteShape {slideId, shapeId}`.
- A slide no layout carries (results, reveal activity, media, game screen): compose it with
  `references/composed-slides.md`; shapes added in a template deck without its skeleton land on top
  of each other.
- **Math is a formula, never plain characters**: `[[katex:(a+b)^2]]`, never `x^2`, `x²` or `1/2`
  (`references/math-formulas.md`; read it before any math slide).
- **Language:** all text, including quiz feedback (default English), in the deck's language.
  Vietnamese, French, German, Spanish and Russian run 10-30% longer than English: shorten rather
  than overflow; Chinese, Japanese and Korean use about half the characters. For non-Latin scripts
  or Vietnamese accents, check in the slide picture that the template's fonts have the letters
  (`localize-presentation` skill, if installed).
- Do not touch colors, fonts or sizes. If the layout cannot hold the content, change the layout
  (another variant or tag), not the styling.

After a slide or a few slides, run `reviewSlides {slideIds: [...]}` and fix what it reports before
moving on. If a call fails, fix the call; do not retry the same arguments.

### 8. Interactivity and quizzes (when it helps learning)

- Reveals, tabs, flip cards, tooltips, hotspots, media and narrated slides:
  `references/interactions.md` (or the `design-interactions` skill if installed).
- Quiz questions use the template's `question-*` layouts: follow `create-quiz` (score, branching,
  results). For graded e-learning also `setCompletionCriteria`, `setSuccessCriteria`,
  `setMaxAttempts`, `setResumeOption`.
- Games (lives, Jeopardy boards, escape rooms, dice, branching stories): the `build-game` skill if
  installed, else `references/games.md`.

Interactive slides need room for buttons and panels: use the skeleton in
`references/composed-slides.md`.

### 9. Review the result and fix it

Run the loop in `references/review-loop.md`: `reviewSlides {}` until clean (up to 40 slides per call,
follow `remaining`), `getSlideImage {presentationId, slideId}` for each slide (up to about 15, else
one per layout plus every changed slide), then the content checks (no leftover sample text, texts
within `maxChars`, order matches the outline, every graded question has a correct answer,
interactions wired). Fix and look again.

### 10. Report

One line per slide with the template used; that the deck was reviewed and what you fixed; features
used; and what needs the user: unfixed problems, facts to confirm, pictures to replace, generated
pictures and credits left, accents to check, interactions and games to test in the player, and what
was asked but is not possible (exports, and narration when `addNarration` is not listed). Details:
`references/review-loop.md`. Suggest next steps: preview in the player, share, export for an LMS
from the editor, or restyle with another template.
