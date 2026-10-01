---
name: localize-presentation
description: "Translate a uPresenter presentation or lesson into another language, or build one in a language other than English, and adapt it to that locale: every text, quiz question, answer and feedback, chart label and table, plus the presentation language that drives the player's buttons, dates and text direction. Use when the user asks to translate, localize or convert a presentation to another language (for example dịch bài giảng sang tiếng Anh, traducir la presentación, 翻译成中文), or to make a presentation for a non-English audience."
---

# Translate or localize a presentation in uPresenter

A translated deck is only done when the words, the player around them and the layout all fit the new
language. Words are the easy part: text lengths change, fonts may lack the letters, quiz feedback
defaults to English, and right-to-left languages mirror the slide.

Editing happens in the user's open editor tab, like every other uPresenter edit: follow step 1 of
`edit-presentation` to get the presentation open, call tools one at a time, and always pass
`slideId` and `shapeId` together.

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
a text holds math), `getQuestionsInfo {includeAnswers: true}`, `getChartInfo` for each chart and
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
- Slide names and titles: `setSlideName`, `setSlideTitle`; the deck: `setPresentationName`.
- Leave variables, interactions and `{{score}}`-style placeholders as they are.

### 5. Translate the quiz

- The question text is the slide's title shape: `setShapeText`.
- `setQuestionAnswers` with the translated answers in the same order, and the same `correct` flags.
  Sequence items keep their correct order, matching pairs keep their pairing, and blanks keep their
  positions.
- Text-entry questions: list every accepted spelling, and in languages with accents also the form
  without accents (a learner may type `Ha Noi` for `Hà Nội`); `caseSensitive` stays off unless the
  case matters.
- **Feedback defaults to English.** `setQuestionSettings` with `correctFeedback`, `incorrectFeedback`,
  `submittedFeedback` and `answerExplanation` written in the target language for **every** question,
  even if the deck was English before.

### 6. What cannot be translated

Say so in the report instead of leaving it silently: text drawn inside an image, audio and video
narration, embedded videos and documents. List the slides where they are.

### 7. Review and fix

Translated text is the usual cause of overflow.

1. `reviewSlides {}`: fix every spill by shortening the translation, not by shrinking the font.
2. `getSlideImage {presentationId, slideId}` for each slide: check that every letter and accent
   renders in the intended font (a fallback font or empty boxes mean the font lacks the script),
   that nothing is cut, and for right-to-left decks that the layout reads naturally mirrored.
3. `getQuestionsInfo {includeAnswers: true}`: every graded question still has its correct answer and
   the feedback is in the new language.

If a font lacks the script, tell the user and propose another template whose fonts cover it
(`listTemplates` shows each template's `fonts` and `language`).

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
