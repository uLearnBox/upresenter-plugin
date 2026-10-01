---
name: create-quiz
description: "Add graded quiz questions, knowledge checks, scoring, feedback and results to a uPresenter presentation. Use when the user wants a quiz, test, assessment, survey, review questions or a score-based lesson in uPresenter, in any of its 14 question types."
---

# Add quizzes and assessment in uPresenter

Questions are native objects of the presentation, graded by the player and tracked for LMS
reporting. This skill assumes a presentation is open in an editor tab. If not, follow step 2 of
the `create-presentation` skill first (`listOpenEditorContexts`, then `listPresentations` or
`getNewPresentationLink`). Call tools one at a time and use the ids they return.

## Question workflow

### In a deck built from a template (preferred)

Every template has designed question slides tagged `question-mc`, `question-mr`, `question-tf`,
`question-fte`, `question-sq`, `question-ma`, `question-fb`, `question-sl`, `question-dd` and
`question-dw` (some also `question-es`, `question-hs`, `question-lb`, `question-mdd`). Using them
keeps the quiz in the same visual style as the rest of the deck. `getTemplateLayouts {templateId,
tag: "question-mc"}` shows which exist and the variants of each.

1. `insertTemplateSlides {slides: [{tag: "question-mc"}, ...], index}` places the question slides
   at exact positions. The result gives each slide's `slideId`, its `slots` and the
   `questionShapeId`.
2. `setShapeText` the question into the `title` slot, and a short instruction for the type
   ("Select one answer", "Put the steps in order") into the other slot, or `deleteShape` it.
3. `setQuestionAnswers {slideId, shapeId: questionShapeId, ...}` with the answer model of that
   type (below); the layout adapts to the number of answers.
4. `setQuestionSettings` for grading, feedback and navigation.

A type the template has no layout for falls back to the blank-slide route below, or to another
template question style. Follow the `create-presentation` skill for choosing the template.

### On blank slides

For every question slide:

1. `addSlide {slideType: "blank", index}` (always pass `index`).
2. `addText` with `textType: "title"` holding the question text.
3. `addQuestion {questionType, answers}` returns the question `shapeId`.
4. `setQuestionAnswers` with the answer model of that type.
5. `setQuestionSettings` for grading, feedback and navigation.

```json
{"tool": "addQuestion", "args": {"questionType": "multiple-choice", "answers": ["Jupiter", "Saturn", "Earth"]}}
{"tool": "setQuestionAnswers", "args": {"slideId": 3, "shapeId": 7, "answers": [{"text": "Jupiter", "correct": true}, {"text": "Saturn", "correct": false}, {"text": "Earth", "correct": false}]}}
{"tool": "setQuestionSettings", "args": {"slideId": 3, "shapeId": 7, "mode": "graded", "points": 10, "maxAttempts": 2, "feedback": "correct-incorrect", "correctFeedback": "Exactly!", "incorrectFeedback": "Not quite. Jupiter is the largest.", "answerExplanation": "Jupiter's diameter is about 140,000 km.", "showConfetti": true}}
```

## Answer models by type

| questionType | `setQuestionAnswers` payload |
|---|---|
| multiple-choice, multiple-response, true-false, dropdown | `answers: [{text, correct}]` (one `correct` for multiple-choice, several for multiple-response) |
| fill-text-entry (short answer) | `answers`: every accepted spelling, all `correct: true` |
| essay | cannot be auto-graded: use `mode: "survey"` or `feedback: "submitted"` |
| slider | `correctValue, min, max, step` |
| sequence | `items: [...]` in the correct order (the player shuffles them) |
| matching | `pairs: [{source, target}]` |
| fill-blanks | `sentence` with `___` per blank, `blanks: [...]` |
| drag-words | as fill-blanks plus `distractors: [...]` |
| multiple-dropdown | `sentence` plus `dropdowns: [{correct, alternatives: [...]}]` |
| hotspot | `hotspots: [{shape: "point" \| "rectangle", x, y, width, height}]` in percent; the slide needs an image first |
| label | `labels: [{text, x, y}]` plus `distractors` (also needs an image) |

Vary the types across a quiz: for example two multiple-choice, one true-false, one matching,
one fill-blanks. Check `getSupportedQuestionTypes` if unsure which names exist.

## Settings that matter

- `mode`: `graded` (counts toward the score) or `survey` (opinion checks, never graded).
- `points` (1-1000), `maxAttempts` (0 = unlimited), `allowResubmission`.
- `feedback`: `none`, `correct-incorrect` or `submitted`, with `correctFeedback`,
  `incorrectFeedback`, `submittedFeedback` and `answerExplanation`.
- `navigation`: questions advance to the next slide after submit by default. If the slide has
  its own Next button, set `navigation: "stay"` or one click will skip a slide.
- `showConfetti` with `confettiEffect`: `basic, fireworks, stars, school-pride, snow, fountain,
  explosion, rain`; `shuffleAnswers` for multiple-choice and multiple-response; `caseSensitive`
  for fill-text-entry.

Read the result back with `getQuestionsInfo {scope: "all-slides", includeAnswers: true}`.

## Score tracking

1. `createVariable {name: "score", type: "number", defaultValue: 0}` returns the variable id.
2. On every graded question add an interaction that adds its points on a correct answer:

```json
{"tool": "addInteraction", "args": {"slideId": 3, "shapeId": 7, "eventType": "question-correct", "actionType": "adjust-variable", "actionData": {"variableId": "VAR", "operation": "add", "operand": {"kind": "literal", "value": 10}}}}
```

Operands are typed objects (`{"kind": "literal", "value": 10}`), never a bare number.
`question-correct` and `question-incorrect` attach only to graded question shapes.

## Branching on the score

Build the target slides first so their ids exist. Then add two conditional `go-to-slide`
actions to the same button; each runs only when its condition holds:

```json
{"tool": "addInteraction", "args": {"slideId": 9, "shapeId": 21, "eventType": "click", "actionType": "go-to-slide", "actionData": {"slideId": 12}, "condition": {"match": "all", "rules": [{"variableId": "VAR", "operator": "gte", "operand": {"kind": "literal", "value": 20}}]}}}
{"tool": "addInteraction", "args": {"slideId": 9, "shapeId": 21, "eventType": "click", "actionType": "go-to-slide", "actionData": {"slideId": 11}, "condition": {"match": "all", "rules": [{"variableId": "VAR", "operator": "lt", "operand": {"kind": "literal", "value": 20}}]}}}
```

A review slide should end with a button back to the questions.

## Results slide

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

Run `getQuestionsInfo` with `includeAnswers: true`: every graded question has a correct answer,
points add up to the progress `max`, and feedback texts are filled in. Report the quiz structure
to the user (question types, points, pass mark).
