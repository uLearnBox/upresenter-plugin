---
name: create-quiz
description: "Add graded quiz questions, knowledge checks, scoring, feedback and results to a uPresenter presentation, using the template's own question-* layouts so the quiz matches the deck. Use when the user wants a quiz, test, assessment, survey, review questions or a score-based lesson in uPresenter, in any of its 14 question types, in any language (for example tạo câu hỏi trắc nghiệm, crear un cuestionario, 做一份测验, créer un quiz)."
---

# Add quizzes and assessment in uPresenter

Questions are native objects of the presentation, graded by the player and tracked for LMS
reporting.

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
- Not possible with the tools: exports (narration only via `addNarration` when listed). Tell the user to
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

The score is built in: every graded question adds its `points` when answered correctly, and the
read-only system variables follow it (`$score`, `$maxScore`, `$scorePercentage` 0 to 100,
`$correctAnswerCount`, `$answeredQuestionCount`, `$passed`; the full list is in `design-interactions`
`references/events-actions-variables.md`). Do not create a `score` variable that every question
adds to: it duplicates the built-in one and drifts when a question changes. Surveys never count.

- Show it in any text: `Score: {{$score}} of {{$maxScore}}` or `{{$scorePercentage}}%`.
- Bind a meter to it: `addProgress {variant, variableId: "$scorePercentage", min: 0, max: 100}`.
- Branch on it: conditions use the system ids directly, as in the next section.
- Create your own number variable only when the built-in score cannot express the rule: negative
  marking, weighted bonuses, team or round scores, a streak. Then add its `adjust-variable`
  interactions on `question-correct` / `question-incorrect`:

```json
{"tool": "addInteraction", "args": {"slideId": 3, "shapeId": 7, "eventType": "question-correct", "actionType": "adjust-variable", "actionData": {"variableId": "<id from createVariable>", "operation": "add", "operand": {"kind": "literal", "value": 10}}}}
```

Operands are typed objects (`{"kind": "literal", "value": 10}`), never a bare number.
`question-correct` and `question-incorrect` attach only to graded question shapes.
Pass slide and shape ids exactly as the tool results returned them.

## Branching on the score

Build the target slides first so their ids exist. Then add two conditional `go-to-slide`
actions to the same button; each runs only when its condition holds. Use `$passed` when the pass
mark is already set with `setSuccessCriteria` (the quiz and the report then agree on what passing
means), or compare `$scorePercentage` with a number:

```json
{"tool": "addInteraction", "args": {"slideId": 9, "shapeId": 21, "eventType": "click", "actionType": "go-to-slide", "actionData": {"slideId": 12}, "condition": {"match": "all", "rules": [{"variableId": "$passed", "operator": "isTrue"}]}}}
{"tool": "addInteraction", "args": {"slideId": 9, "shapeId": 21, "eventType": "click", "actionType": "go-to-slide", "actionData": {"slideId": 11}, "condition": {"match": "all", "rules": [{"variableId": "$passed", "operator": "isFalse"}]}}}
```

A review slide should end with a button back to the questions.

## Results slide

Templates rarely have a results layout: compose it (in a template deck inside the positioned
group of `create-presentation` `references/composed-slides.md`). Games with lives, timers and
boards: `create-presentation` `references/games.md`.

- Title text, then `addProgress {variant: "circle", min: 0, max: 100, variableId: "$scorePercentage",
  progressColor: "rgb(var(--aie-accent-0-rgb))", trackColor: "rgba(var(--aie-body-rgb), 0.2)"}`.
  The circle shows no number: add a text such as `Score: {{$score}} / {{$maxScore}}`.
- A "Try again" button: `go-to-first-slide` (or `go-to-slide` to the first question). A retry only
  reopens questions that allow more than one attempt; if you track your own variables, run
  `adjust-variable` with `operation: "reset"` for each one first.
- A slide-level `slide-enter` interaction with `actionType: "play-confetti"` celebrates the
  result (`actionData: {"confettiType": "fireworks"}`); give it a `$passed` condition to
  celebrate a pass only.
- `finish-presentation` on a final button closes the attempt for LMS reporting.

## Scoring policies

- **Weighting**: set `points` per question (integers, 1 to 1000): harder questions get more.
- **Pass mark**: `setSuccessCriteria {condition: "sc", value: 70, unit: "p"}`, then branch on
  `$passed`. There is no partial credit: a question is fully right or scores nothing.
- **Attempts**: `maxAttempts` on a question limits its retries; `setMaxAttempts` limits the whole
  deck.
- **Negative marking or penalties**: not built in; use your own number variable that
  `question-incorrect` subtracts from, and show it next to `{{$score}}`.

## Question banks, timed exams and surveys

- **There is no question pool.** To give each learner a different set, put the questions on
  separate slides and use `go-to-random-slide` with `slideIds` (the candidates) from a start
  button, with a counter variable to stop after N questions. Say that it can repeat a slide.
- **Timed exam**: a slide `timer` event (`timerSeconds`) with `finish-presentation` or a
  `go-to-slide` to the results; show the countdown with `{{$timeLeft}}`. A timer is per slide, not
  per exam.
- **Survey or poll**: `mode: "survey"` on each question (never graded, no points). `question-answered`
  fires after any submission, surveys included; use it for a "thank you" step.

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
