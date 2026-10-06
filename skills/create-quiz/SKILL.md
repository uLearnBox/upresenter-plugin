---
name: create-quiz
description: "Add graded quiz questions, knowledge checks, scoring, feedback and results to a uPresenter presentation, using the template's own question-* layouts so the quiz matches the deck. Use when the user wants a quiz, test, assessment, survey, review questions or a score-based lesson in uPresenter, in any of its 14 question types, in any language (for example tạo câu hỏi trắc nghiệm, crear un cuestionario, 做一份测验, créer un quiz)."
---

# Add quizzes and assessment in uPresenter

Questions are native objects of the presentation, graded by the player and tracked for LMS
reporting.

## Ground rules

- A presentation must be **open in an editor tab** and stay open (ask the user not to click around
  in it). If none is open: `listOpenEditorContexts`, then `listPresentations` or
  `getNewPresentationLink` (see `create-presentation`, step 4). With several editors open pass the
  `sessionId`.
- Call tools **one at a time**, use the ids they return, and pass `slideId` with every `shapeId`
  (shape ids repeat on every slide).
- **Template first:** every question slide starts from a `question-*` layout of the deck's
  template (`listTemplates`, `getTemplateLayouts`, `insertTemplateSlides`). A blank slide is only
  for a type the template lacks. Slots with `sample` text must be replaced or deleted.
- Check with `reviewSlides` and `getSlideImage` before reporting; the tools cannot play the quiz.
- Not possible with the tools: animations, transitions, narration/TTS, exports. Tell the user to
  use the editor.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Question workflow

### In a deck built from a template (preferred)

Templates carry designed question slides tagged `question-mc`, `question-mr`, `question-tf`,
`question-fte`, `question-sq`, `question-ma`, `question-fb`, `question-sl`, `question-dd` and
`question-dw`, and some also `question-es`, `question-hs`, `question-lb`, `question-mdd` (the full
set is the 14 tags in the table below). Using them keeps the quiz in the same visual style as the
rest of the deck. `getTemplateLayouts {templateId, tag: "question-mc"}` shows which exist and the
variants of each; `listTemplates` `layouts` counts them per tag, so when choosing a template for a
quiz check that it has the tags you need (`category: "Quiz"` lists quiz-oriented ones; filter by
`language` and `layoutType` too).

1. Find the template the deck's style comes from: `getPresentationInfo` gives `themeId`; the
   template is the one whose `themeId` matches in `listTemplates` (usually the deck's own
   `templateId`, but not after the style was switched). Pass that `templateId` on every insert:
   without it the tool uses the template the deck was created from, which may be another style.
2. `insertTemplateSlides {templateId, slides: [{tag: "question-mc"}, ...], index}` places the
   question slides at exact positions. The result gives each slide's `slideId`, its `slots`, the
   `questionShapeId` and `questionType`.
3. `setShapeText` the question into the `title` slot, and a short instruction for the type
   ("Select one answer", "Put the steps in order") into the other slot, or `deleteShape {slideId,
   shapeId}` it.
4. `setQuestionAnswers {slideId, shapeId: questionShapeId, ...}` with the answer model of that
   type (below). The answers keep the layout's designed answer cards; extra answers copy the last
   card. More answers than the layout shows can overflow: check the slide picture.
5. `setQuestionSettings` for grading, feedback and navigation.

A type the template has no layout for falls back to the blank-slide route below. Follow the
`create-presentation` skill for choosing the template.

### On blank slides

For every question slide:

1. `addSlide {slideType: "blank", index}` (always pass `index`).
2. In a deck made from a template (freeform), first the positioned content group of
   `create-presentation` `references/composed-slides.md`, section 2, and `selectShape` it before
   each insert; otherwise the title and the question land on top of each other.
3. `addText` with `textType: "title"` holding the question text.
4. `addQuestion {questionType, answers}` returns the question `shapeId`.
5. `setQuestionAnswers` with the answer model of that type.
6. `setQuestionSettings` for grading, feedback and navigation.

```json
{"tool": "addQuestion", "args": {"questionType": "multiple-choice", "answers": ["Jupiter", "Saturn", "Earth"]}}
{"tool": "setQuestionAnswers", "args": {"slideId": 3, "shapeId": 7, "answers": [{"text": "Jupiter", "correct": true}, {"text": "Saturn", "correct": false}, {"text": "Earth", "correct": false}]}}
{"tool": "setQuestionSettings", "args": {"slideId": 3, "shapeId": 7, "mode": "graded", "points": 10, "maxAttempts": 2, "feedback": "correct-incorrect", "correctFeedback": "Exactly!", "incorrectFeedback": "Not quite. Jupiter is the largest.", "answerExplanation": "Jupiter's diameter is about 140,000 km.", "showConfetti": true}}
```

## Math in questions and answers

Write equations as formulas, `[[katex:LATEX]]` inside the text (rules and syntax in the
`create-presentation` skill, `references/math-formulas.md`). They work in the question text (the
slide's title via `setShapeText`), choice answers, sequence items, matching pairs and the
`sentence` of fill-blanks, drag-words and multiple-dropdown questions:

```json
{"tool": "setShapeText", "args": {"slideId": 6, "shapeId": 3, "text": "Which expression equals [[katex:(x+3)^2]]?"}}
{"tool": "setQuestionAnswers", "args": {"slideId": 6, "shapeId": 8, "answers": [{"text": "[[katex:x^2 + 6x + 9]]", "correct": true}, {"text": "[[katex:x^2 + 9]]", "correct": false}, {"text": "[[katex:x^2 + 3x + 9]]", "correct": false}]}}
```

Dropdown options and the accepted answers of fill-text-entry questions are plain text only: for
math there, ask for a number or a short word answer, or use a choice or fill-blanks question.

## The 14 question types

`questionType` is the name `addQuestion` takes; the tag is the template layout to insert first.

| questionType | Template tag | `setQuestionAnswers` payload |
|---|---|---|
| multiple-choice | `question-mc` | `answers: [{text, correct}]`, at least 2, exactly one `correct` |
| multiple-response | `question-mr` | same, several `correct` |
| true-false | `question-tf` | exactly 2 `answers: [{text, correct}]`, in the deck's language |
| fill-text-entry | `question-fte` | `answers`: every accepted spelling, all `correct: true`; case-sensitive until `caseSensitive: false` |
| essay | `question-es` | cannot be auto-graded: use `mode: "survey"` or `feedback: "submitted"` |
| dropdown | `question-dd` | `answers: [{text, correct}]` (plain text) |
| slider | `question-sl` | `correctValue, min, max, step` |
| sequence | `question-sq` | `items: [...]` in the correct order, at least 2 (the player shuffles them) |
| matching | `question-ma` | `pairs: [{source, target}]`, 2 to 6 pairs |
| hotspot | `question-hs` | `hotspots: [{shape: "point" \| "rectangle", x, y, width, height}]` in percent; needs an image |
| label | `question-lb` | `labels: [{text, x, y}]` plus `distractors` (needs an image) |
| fill-blanks | `question-fb` | `sentence` with `___` per blank, `blanks: [...]` |
| drag-words | `question-dw` | as fill-blanks plus `distractors: [...]` |
| multiple-dropdown | `question-mdd` | `sentence` plus `dropdowns: [{correct, alternatives: [...]}]` |

`getSupportedQuestionTypes` lists the names the app accepts. Read one question back with
`getQuestionInfo {slideId, shapeId}` (settings plus the authored answer model) and the whole deck
with `getQuestionsInfo {scope: "all-slides", includeAnswers: true}` (without `scope` only the
current slide is read).

Vary the types across a quiz: for example two multiple-choice, one true-false, one matching,
one fill-blanks. Check `getSupportedQuestionTypes` if unsure which names exist.

## Settings that matter

**Write feedback in the deck's language.** New questions carry English feedback texts ("Correct.",
"Not quite..."). For every question set `correctFeedback`, `incorrectFeedback` (and
`submittedFeedback` and `answerExplanation` where used) in the language of the deck, whatever it
was before.

- `mode`: `graded` (counts toward the score) or `survey` (opinion checks, never graded).
- `points` (1-1000), `maxAttempts` (0 = unlimited), `allowResubmission`.
- `feedback`: `none`, `correct-incorrect` or `submitted`, with `correctFeedback`,
  `incorrectFeedback`, `submittedFeedback` and `answerExplanation`.
- `navigation`: questions advance to the next slide after submit by default. If the slide has
  its own Next button, set `navigation: "stay"` or one click will skip a slide.
- `showConfetti` with `confettiEffect`: `basic, fireworks, stars, school-pride, snow, fountain,
  explosion, rain`; `shuffleAnswers` for multiple-choice and multiple-response;
  `caseSensitive` for fill-text-entry: new questions are case-sensitive, so set it to `false`
  unless the case matters ("hà nội" should match "Hà Nội").

Read the result back with `getQuestionsInfo {scope: "all-slides", includeAnswers: true}`.

## Score tracking

1. `createVariable {name: "score", type: "number", defaultValue: 0}` returns the variable id (a UUID such as
   `3f6c1a9e-8d2b-4c57-9a41-b7e0d5c2f813`, used in the examples below; always use the one returned).
2. On every graded question add an interaction that adds its points on a correct answer:

```json
{"tool": "addInteraction", "args": {"slideId": 3, "shapeId": 7, "eventType": "question-correct", "actionType": "adjust-variable", "actionData": {"variableId": "3f6c1a9e-8d2b-4c57-9a41-b7e0d5c2f813", "operation": "add", "operand": {"kind": "literal", "value": 10}}}}
```

Operands are typed objects (`{"kind": "literal", "value": 10}`), never a bare number.
`question-correct` and `question-incorrect` attach only to graded question shapes.

## Branching on the score

Build the target slides first so their ids exist. Then add two conditional `go-to-slide`
actions to the same button; each runs only when its condition holds:

```json
{"tool": "addInteraction", "args": {"slideId": 9, "shapeId": 21, "eventType": "click", "actionType": "go-to-slide", "actionData": {"slideId": 12}, "condition": {"match": "all", "rules": [{"variableId": "3f6c1a9e-8d2b-4c57-9a41-b7e0d5c2f813", "operator": "gte", "operand": {"kind": "literal", "value": 20}}]}}}
{"tool": "addInteraction", "args": {"slideId": 9, "shapeId": 21, "eventType": "click", "actionType": "go-to-slide", "actionData": {"slideId": 11}, "condition": {"match": "all", "rules": [{"variableId": "3f6c1a9e-8d2b-4c57-9a41-b7e0d5c2f813", "operator": "lt", "operand": {"kind": "literal", "value": 20}}]}}}
```

A review slide should end with a button back to the questions.

## Results slide

Templates rarely have a results layout: compose it (in a template deck inside the positioned
group of `create-presentation` `references/composed-slides.md`). Games with lives, timers and
boards: `create-presentation` `references/games.md`.

- Title text, then `addProgress {variant: "circle", min: 0, max: <total points>, variableId,
  progressColor: "rgb(var(--aie-accent-0-rgb))", trackColor: "rgba(var(--aie-body-rgb), 0.2)"}`.
  The circle shows no number: add a text such as `Score: {{score}}`.
- A "Try again" button with two interactions in this order: `adjust-variable` with
  `operation: "reset"`, then `go-to-slide` to the first question. A retry only reopens questions
  that allow more than one attempt.
- A slide-level `slide-enter` interaction with `actionType: "play-confetti"` celebrates the
  result (`actionData: {"confettiType": "fireworks"}`).
- `finish-presentation` on a final button closes the attempt for LMS reporting.

## Tracking settings

For graded e-learning: `setCompletionCriteria {condition: "sv", value: 100, unit: "p"}` (all
slides viewed) or `condition: "sc"` (score), `setSuccessCriteria {condition: "sc", value: 70,
unit: "p"}`, `setMaxAttempts`, `setResumeOption`. `setPlayerMode {mode: "quiz"}` presents the
deck as a quiz.

## Check

Run `reviewSlides` on the question slides: long questions or answers can spill out of their card. `reviewSlides` also reports unfilled placeholders; sample text that ships in a layout (`sample` slots, answer labels like "Option A") is not marked, so read each slide's texts and replace or delete it.
Then run `getQuestionsInfo {scope: "all-slides", includeAnswers: true}` (without `scope` it reads
only the current slide): every graded question has a correct answer, points add up to the
progress `max`, and feedback texts are in the deck's language. Check the score and branching
wiring with `getInteractions {slideId}`; the tools cannot play the quiz, so ask the user to try
it in the player. Report the quiz structure
to the user (question types, points, pass mark).
