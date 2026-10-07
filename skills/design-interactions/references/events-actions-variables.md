# Events, actions and variables reference

Look things up here when you wire `addInteraction` / `updateInteraction` by hand. Every name is the
exact string the tools take. A call that breaks a rule below is refused with a message, so read the
message, fix the arguments and retry rather than working around it.

## Events (`eventType`)

| Event | Target | Fires when | Notes |
| --- | --- | --- | --- |
| `click`, `double-click` | shape | the learner clicks or double-clicks | a child that has any event swallows the click for its parent group |
| `mouse-enter`, `mouse-leave` | shape | the pointer enters or leaves | no hover on phones and tablets: never hide essential content behind it |
| `slide-enter` | slide only | the slide is shown | runs on every visit; a shape cannot use it |
| `timer` | slide only | `timerSeconds` (1 to 3600, default 10) after the slide is shown | one timer per slide: setting it again changes the delay for all its actions; paused with the presentation; show the countdown with `{{$timeLeft}}` |
| `question-correct`, `question-incorrect` | graded question shape | after a submission | refused on survey questions |
| `question-answered` | any question shape | after any saved submission | surveys included |
| `media-ended` | audio or video shape | playback finishes | not for embedded YouTube, Vimeo, Dailymotion or Loom, and not while Loop is on |

A slide interaction (no `shapeId`) accepts only `slide-enter` and `timer`; a shape interaction
accepts everything else. Actions on one event run in the order added, and navigation does not stop
the chain, so put `show`, `hide` and `adjust-variable` before any go-to.

## Actions (`actionType`) and their `actionData`

| Action | `actionData` | Rules |
| --- | --- | --- |
| `next-slide`, `previous-slide`, `go-to-first-slide`, `go-to-last-slide` | none | |
| `go-to-slide` | `slideId` | the slide must exist; re-point it with `updateInteraction` after slides are deleted or copied |
| `go-to-slide-at-index` | `slideIndex` (zero-based) | breaks when slides are reordered; prefer `go-to-slide` |
| `go-to-random-slide` | `slideIds` (list) | empty list = any slide except the current one |
| `pause-presentation`, `resume-presentation` | none | pauses and resumes the presentation in the player |
| `finish-presentation` | none | the same as the player's finish button |
| `show`, `hide`, `toggle-visibility` | `targetShapeId` | a shape cannot target itself: target its group (a child may target its parent); `hide` collapses the space, initially hidden (`setShapeInitiallyHidden`) keeps it until shown |
| `play-media`, `pause-media` | `targetShapeId` | audio, video or an embedded video |
| `toggle-play-pause-media` | `targetShapeId` | audio or video only |
| `mute-media`, `unmute-media`, `toggle-media-mute` | none | apply to the whole presentation, not one shape; the first button should run `unmute-media` when there is background music |
| `show-tooltip` | `content` (required), `backgroundColor?` | shape interactions only; a short note, use `open-dialog` for more |
| `open-dialog` | `title` and/or `message` | at least one of the two |
| `open-url` | `url` | `http`, `https`, `mailto` or `tel` only; blocked in public presentations |
| `play-confetti` | `confettiType`: `basic`, `fireworks`, `stars`, `school-pride`, `snow`, `fountain`, `explosion`, `rain` | default `basic` |
| `submit` | none | the same as the player's submit button; test it in the player |
| `set-text` | `targetShapeId`, `text` | target must be a title, subtitle, content, button or text-box shape; the change lasts for this visit; `{{variable name}}` and `{{$score}}` stay live |
| `adjust-variable` | `variableId`, `operation`, plus `operand` or `min` and `max` | see below |

## Variables

`createVariable {name, type, defaultValue}` makes a lesson-wide variable; each learner attempt
starts from the defaults. `type` is `number`, `text` or `boolean`; the name must be unique
(case-insensitive). `updateVariable` changes the definition only (a type change is refused while
the variable is used); it never changes a learner's current value, an `adjust-variable` action
does. `deleteVariable` is refused while any action, condition or progress meter uses it.

`adjust-variable` operations (only on your own variables; system variables are read-only):

| `operation` | Variable type | Needs |
| --- | --- | --- |
| `set` | any | `operand` of the same type |
| `add`, `subtract` | number | numeric `operand` |
| `toggle` | boolean | nothing |
| `reset` | any | nothing (back to the default) |
| `random` | number | `min` and `max`, whole numbers, `min` <= `max` (both included); no operand |

An operand is typed: `{"kind": "literal", "value": 10}` or `{"kind": "variable", "variableId":
"..."}`, never a bare value, and its type must equal the variable's type.

## System variables (read-only)

Use them in conditions, in `{{ }}` text and in `addProgress`. They cannot be set or adjusted. Get
the live list, with ids, from `getVariables`.

| Id | Type | Meaning |
| --- | --- | --- |
| `$score` | number | points earned in this attempt |
| `$maxScore` | number | maximum graded score |
| `$scorePercentage` | number | score as 0 to 100 (0 when there are no points) |
| `$slideNumber` | number | current slide position, from 1 |
| `$slideTitle` | text | name of the current slide |
| `$totalSlides` | number | slides in the lesson |
| `$viewedSlides` | number | distinct slides viewed in this attempt |
| `$progressPercentage` | number | percentage of distinct slides viewed |
| `$questionCount` | number | graded questions (surveys excluded) |
| `$answeredQuestionCount` | number | graded questions with a submitted answer |
| `$unansweredQuestionCount` | number | graded questions without a submitted answer |
| `$correctAnswerCount` | number | graded questions whose latest answer is correct |
| `$incorrectAnswerCount` | number | graded questions whose latest answer is incorrect |
| `$passed` | boolean | the success requirement is currently met |
| `$completed` | boolean | the completion requirement is currently met |
| `$slideElapsed` | number | whole seconds since the slide was shown (paused time excluded) |
| `$timeLeft` | number | whole seconds before the slide timer fires (0 without a timer) |

`$passed` and `$completed` follow the criteria from `setSuccessCriteria` and
`setCompletionCriteria`: set those first, then branch with `isTrue` / `isFalse`.

## Conditions

`condition: {match: "all" | "any", rules: [{variableId, operator, operand}]}` is checked just
before the action runs; a false or invalid condition skips that action. There is no else branch:
add a second action with the opposite condition. `removeCondition: true` on `updateInteraction`
makes an action unconditional.

| Variable type | Operators | Operand |
| --- | --- | --- |
| number | `eq`, `ne`, `gt`, `gte`, `lt`, `lte` | typed operand of the same type |
| text | `eq`, `ne`, `contains`, `empty`, `notEmpty` | typed operand (none for `empty`, `notEmpty`) |
| boolean | `isTrue`, `isFalse` | none |

## Check after wiring

`getInteractions {slideId}` returns `eventIndex` and `actionIndex` for `updateInteraction` and
`removeInteraction`; `getVariables` shows each variable with where it is used. Report which paths
the user should try in the player (the tools cannot click through it).
