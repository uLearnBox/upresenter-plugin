---
name: localize-presentation
description: "Translate a uPresenter presentation or lesson into another language, or build one in a language other than English, and adapt it to that locale: every text, quiz question, answer and feedback, chart label and table, plus the presentation language that drives the player's buttons, dates and text direction. Use when the user asks to translate, localize or convert a presentation to another language (for example dịch bài giảng sang tiếng Anh, traducir la presentación, 翻译成中文, traduire la présentation), or to make a presentation for a non-English audience."
---

# Translate or localize a presentation in uPresenter

A translated deck is only done when the words, the player around them and the layout all fit the new
language. Words are the easy part: text lengths change, fonts may lack the letters, quiz feedback
defaults to English, and right-to-left languages mirror the slide.

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
- Editing happens in the user's **open editor tab**, which must stay open (ask the user not to click
  around in it). Not open: `listOpenEditorContexts`, else `listPresentations` and give the user the
  `editorUrl`; with several editors pass the `sessionId`.
- Call tools **one at a time**; pass `slideId` and `shapeId` together (shape ids repeat on every
  slide), both from tool results.
- **Translating in place replaces the original and the tools cannot copy a deck.** Before the first
  edit remind the user to duplicate the presentation in their dashboard and open the copy if they
  want to keep the source language.
- Template first: any slide you must add (a missing results or summary slide) comes from the deck's
  template layouts (`listTemplates`, `getTemplateLayouts`, `insertTemplateSlides`), not a blank
  slide. Template sample text that was never replaced is untranslated content too: translate or
  delete it.
- Check with `reviewSlides` and `getSlideImage`. Not possible with the tools: exports; narration
  only through `addNarration` when listed.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## What the presentation language does (read once)

`setPresentationLanguage {languageCode}` does **not** translate anything. It sets what the viewer
sees around the content:

- the **player interface** language: buttons, prompts and messages such as Next, Submit, Retry;
- date and time formats;
- the **text direction**: Arabic, Hebrew, Persian and Urdu decks are right-to-left, which mirrors
  text alignment and the order of answer choices.

Use one of the codes in `references/locales.md` (for example `vi-VN`, `en-US`, `ja-JP`). For a
language that is not on the list, pick the closest one and tell the user the player interface will
fall back to English.

## Workflow

### 1. Scope

Find out the target language and variant (`pt-BR` or `es-ES`), the register (formal or friendly, for
students or professionals) and whether the original must stay. Translating in place **replaces**
the original text, and the tools cannot copy a presentation: if the user wants to keep the source
version, ask them to duplicate the presentation in their dashboard first and open the copy.

### 2. Read everything

`getPresentationState {scope: "all_slides"}` for every text (with its shape id, and `formulas` where
a text holds math), `getQuestionsInfo {scope: "all-slides", includeAnswers: true}` (without
`scope` only the current slide is read), `getChartInfo` for each chart and
`getTablesInfo` for tables. Make a list of what needs translating: texts, list items, buttons,
table cells, chart labels, slide names, the presentation name, quiz questions, answers, feedback and
answer explanations.

### 3. Prepare

- A short glossary of the key terms, and a note of what stays as it is: product and brand names,
  people, code, units, formulas.
- The conventions of the target locale: decimal and thousands separators, date order, currency
  position, units, examples that need local equivalents.
- Text length: the new text must fit the same boxes. English to Vietnamese, French, Spanish,
  Portuguese, Italian, Russian or German usually runs 10-30% longer: shorten sentences and keep
  headings short. Chinese, Japanese and Korean use about half the characters but each is wider:
  keep the translation to about half of the slot's `maxChars`.
- Fonts: check that the deck's fonts have the letters of the target script (`references/locales.md`).
- `setPresentationLanguage` with the target code **first**, so direction and player follow while you
  work.

### 4. Translate slide by slide

For each slide, in order:

- `setShapeText {slideId, shapeId, text}` for every text shape, keeping its structure: one shape
  stays one shape, a line break stays a paragraph, list items stay separate shapes. Stay within
  `maxChars` where the state gives it.
- **Formulas stay formulas.** Keep every `[[katex:...]]` marker and its LaTeX; translate only the
  words inside `\text{...}`. For a decimal comma write `3{,}14` inside a formula. Rewriting a text
  without its markers turns its formulas into plain text (`create-presentation`,
  `references/math-formulas.md`).
- Charts: `setChartText {slideId, shapeId, title, labels, datasetLabels, xAxisName, yAxisName}` keeps
  the numbers and changes only the words, in the same order and count.
- Tables: `setTableCellsText`.
- Slide titles are text shapes: translate them with `setShapeText` like any text. Slide names
  (the outline labels): `setSlideName`; the deck: `setPresentationName`. Do not use
  `setSlideTitle`: it replaces the title with plain text, losing its formatting and formulas.
- Leave variables, interactions and `{{score}}`-style placeholders as they are.

### 5. Translate the quiz

- The question text is the slide's title shape: `setShapeText`.
- `setQuestionAnswers` with the translated answers in the same order, and the same `correct` flags.
  Sequence items keep their correct order, matching pairs keep their pairing, and blanks keep their
  positions.
- Text-entry questions: list every accepted spelling, and in languages with accents also the form
  without accents (a learner may type `Ha Noi` for `Hà Nội`). New text-entry questions are
  case-sensitive: set `setQuestionSettings {caseSensitive: false}` unless the case matters.
- **Feedback defaults to English.** `setQuestionSettings` with `correctFeedback`, `incorrectFeedback`,
  `submittedFeedback` and `answerExplanation` written in the target language for **every** question,
  even if the deck was English before.

### 6. What cannot be translated

Say so in the report instead of leaving it silently, and list the slides where each occurs:

- text drawn inside an image or SVG, and text baked into a chart picture;
- audio and video narration, subtitles inside a video, embedded videos and documents (`addNarration`, when
  listed, can voice new text; the tools cannot edit media);
- image alt text and speaker notes: the tools do not expose
  them, so the user checks them in the editor;
- the player's own buttons come from `setPresentationLanguage`, not from your translation, and only
  for the languages in `references/locales.md`;
- a font that lacks the script (see step 7).

### 6b. Right-to-left languages (Arabic, Hebrew, Persian, Urdu)

1. `setPresentationLanguage` first (`ar-EG`, `he-IL`, `fa-IR`, `ur-PK`): the slide mirrors, text aligns
   right and answer order starts from the right.
2. Translate as usual. Keep formulas, numbers, Latin brand names and code left to right inside the
   text (`[[katex:...]]` markers stay as they are). Do not add manual alignment or reorder shapes to
   imitate RTL: the mirroring is automatic.
3. Look at every slide with `getSlideImage`: decor on the wrong side, arrows pointing the wrong
   way, a timeline or progress that reads backwards. Report each to the user; a template whose
   layouts mirror well may be a better start (`listTemplates` with the `language` filter).
4. Check the font covers the script (Noto Sans Arabic, Noto Naskh Arabic, Vazirmatn, Noto Sans
   Hebrew); an Urdu deck reads best in a Nastaliq font.

### 7. Review and fix

Translated text is the usual cause of overflow.

1. `reviewSlides {}` (up to 40 slides per call; call again with `remaining`): fix every spill by
   shortening the translation, not by shrinking the font.
2. `getSlideImage {presentationId, slideId}` for each slide: check that every letter and accent
   renders in the intended font (a fallback font or empty boxes mean the font lacks the script),
   that nothing is cut, and for right-to-left decks that the layout reads naturally mirrored.
3. `getQuestionsInfo {scope: "all-slides", includeAnswers: true}`: every graded question still has
   its correct answer and
   the feedback is in the new language.

If a font lacks the script, tell the user and propose another template whose fonts cover it
(`listTemplates` shows each template's `fonts` and `language`, and takes a `language` filter).
Also read every text once more for sample copy left in the template's own language.

### 8. Report

Tell the user the language and variant used, how many slides, questions and charts were translated,
the glossary choices they may want to confirm, and everything that could not be translated or that
looks off (fonts, mirrored layouts, text baked into images).

## Troubleshooting

| Symptom | Fix |
|---|---|
| The player still shows English buttons | `setPresentationLanguage` was not set, or the language is not in `references/locales.md`. |
| Quiz feedback is in English | `setQuestionSettings` was not called for that question: set all four feedback texts. |
| Formulas show as plain text with raw LaTeX | A rewrite dropped the `[[katex:...]]` markers: write the text again with them. |
| Text spills out of its card | Shorten the translation; check the slot's `maxChars` and the language factor in step 3. |
| Accents or letters look wrong or missing | The font lacks the script: another template, or ask the user to change the theme font in the Design tab. |
