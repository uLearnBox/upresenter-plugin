---
name: review-presentation
description: "Audit an existing uPresenter presentation or lesson and report what to improve, without changing it: text that spills out of cards, leftover template sample text and unfilled placeholders, math typed as plain characters, weak story or structure, readability and contrast, inconsistent design, quiz mistakes and tracking settings. Use when the user asks to review, check, proofread, critique, QA or give feedback on a presentation in uPresenter (for example kiểm tra bài giảng, revisar la presentación, 检查演示文稿, vérifier la présentation), or before they share or publish one."
---

# Review a presentation in uPresenter

A review answers one question for the user: is this deck ready to present, and if not, what exactly
should change? It looks at the deck the way a viewer does (the rendered slides), and measures what the
eye cannot judge. **Report first; change nothing until the user says which fixes they want.** Then
apply them with the `edit-presentation` workflow and review the changed slides again.

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
- The presentation must be **open in an editor tab** and stay open (ask the user not to click around
  in it). Not open: `listOpenEditorContexts`, else `listPresentations` and give the user the
  `editorUrl`; with several editors pass the `sessionId`.
- Call tools **one at a time**; pass `slideId` and `shapeId` together (shape ids repeat on every
  slide).
- Reviewing changes nothing. Fixes come after the user chooses them, with `edit-presentation`.
- `reviewSlides` measures, `getSlideImage` shows; use both, and trust the picture when they disagree.
  Up to 40 slides per `reviewSlides` call: follow `remaining`.
- Template first: judge the deck against its template (`getPresentationInfo` gives `templateId` and
  `themeId`; `listTemplates` and `getTemplateLayouts` show which layouts and `sample` slots exist).
  Recommend fixes that reuse a template layout rather than hand-built slides.
- The tools cannot play the deck, hear audio, see animations and transitions in motion, or see the phone layout:
  say so in the report.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

### 1. Gather

- `getPresentationInfo` and `getPresentationState {scope: "presentation_basic"}`: name, language,
  size, slide count, theme colors and fonts, positioning mode.
- `getPresentationState {scope: "all_slides"}`: every text, its slide and shape, `placeholder`,
  `maxChars`, `formulas`.
- `getQuestionsInfo {scope: "all-slides", includeAnswers: true}` (without `scope` only the current
  slide is read), `getPresentationSettings`.
- `getInteractions {slideId}` and `getVariables` on interactive slides and games.
- `getChartInfo` and `getTablesInfo` where a slide has them.

### 2. Measure

`reviewSlides {}` reviews up to 40 slides per call; while the result has `remaining`, call it again
with those `slideIds`. It reports
text spilling out of its box, card or the slide, text lying on other text, unfilled placeholders and
math typed as plain characters.

### 3. Look

`getSlideImage {presentationId, slideId}` for each slide (up to about 15 slides; in a longer deck one
slide of each layout plus every slide the measurement flagged). `presentationId` comes from
`listOpenEditorContexts`. If the tool cannot draw slides, say so in the report and review from the
measurements and the content alone. In a **fluid** presentation (`getPresentationInfo` says
`layoutType: "fluid"`) the "slides" are cards that grow with their content, so nothing spills
and each picture is one whole card, as tall as it is; judge reading length per card (about two
screens at most), the order of the sections and the look. You cannot see the phone layout: say so.

### 4. Judge

Go through the checklist. Note each finding with the slide number, the shape if there is one, what is
wrong and the fix.

**Story and structure**
- A clear opening (what this is, for whom) and a closing (summary, next step or call to action).
- One idea per slide; each headline states the message, not only the topic.
- A sensible order; no repeated or empty slides; length matches the purpose.

**Content**
- Facts, numbers and names are right and consistent between slides; claims are supported.
- Nothing from the template's sample copy remains (figures, labels, timeline entries, "Company name").
  Sample text is not marked as a placeholder, so `reviewSlides` will not flag it: compare the
  texts from `getPresentationState` with the template's `sample` slots (`getTemplateLayouts`) and
  check chart titles, legends and table cells too. List each leftover for replacement.
- Wording is clear and in one language; no typos; one register throughout.

**Text and layout** (from the measurements and the pictures)
- Nothing spills out of its card or the slide; no text overlaps; no unfilled placeholder.
- Body text is large enough to read when projected (about 18 px and up at 1280 px wide) and not a wall
  of text.
- Contrast between text and background is strong; light text on a light picture is a fault.
- Spacing and alignment look intentional; no large empty areas, no crowded corners.

**Design consistency**
- One theme, one set of fonts; slides look like one deck.
- Layout variety: not the same layout many times in a row.
- Pictures are relevant, sharp and not stretched; decor does not compete with content. Generated
  pictures have no distorted hands or faces and no gibberish lettering.

**Math and language**
- Equations are formulas, not characters (`x^2`, `x²`); formulas render correctly.
- The presentation language matches the content; quiz feedback is in that language.

**Quiz and tracking**
- Every graded question has a correct answer, sensible points, feedback and attempts.
- Question types are varied; answers are unambiguous; the total points match the intent.
- For graded e-learning: completion and success criteria are set, and the player settings match the
  purpose (navigation free for a lecture, locked or limited for an assessment).
- Fill-in answers are not case-sensitive unless the case matters; text-entry answers list the
  spellings learners will type.

**Tracking and interaction wiring** (`getVariables`, `getInteractions {slideId}` per interactive
slide, `getPresentationSettings`, `getQuestionsInfo`)
- Unused variables: a variable that no interaction, condition or `{{name}}` text reads or writes is
  dead weight; a variable read but never changed is a bug (a score nothing adds to), except the read-only
  system variables (`$score`, `$passed`...), which the player updates itself. A hand-made `score`
  variable that every question adds to only duplicates the built-in `$score`: suggest the built-in
  one unless the deck needs weighting, penalties or team scores.
- Dead conditions: a rule that can never be true (a threshold above the maximum score, comparing a
  text variable with a number, a variable that was deleted); two conditional `go-to-slide`
  actions that overlap or leave a gap (for example `>= 20` and `< 20` is complete, `> 20` and
  `< 20` is not).
- Questions without a correct answer, graded questions with 0 points, `question-correct` actions on
  a survey question, score interactions missing on some graded questions, points that do not add up
  to the progress `max` or to the success threshold.
- Completion and success sanity: `setCompletionCriteria` and `setSuccessCriteria` use units that
  match (percent vs absolute) and are reachable (a pass mark of 80% with too few points; "all
  slides viewed" in a deck with a locked, branching path that skips slides); attempts and resume
  option match the purpose; a graded deck has a final slide that finishes the attempt
  (`finish-presentation`).
- Player settings match the purpose: free navigation for a lecture, `setPlayerMode` quiz or game
  and a navigation lock only where the deck's own buttons can move through every slide.

**Interactions and games** (`getInteractions`, `getVariables`)
- Every button does something; every go-to-slide target exists; reveal targets start hidden.
- Game questions use `navigation: "stay"`; navigation is not placed on both `question-answered` and
  `question-correct`; a Play again button resets the variables; a game has a Start button that
  unmutes media first when it has music.
- Nothing essential is shown only on hover (phones have no hover).

**Accessibility**
- Text contrast is strong on every background, body text is large enough (18px or more), and no
  meaning depends on color alone (a right or wrong answer also needs a word or an icon).
- Titles are real title shapes (not large body text), and the presentation language matches the
  content (`getPresentationSettings`), which the player uses for its own labels.
- Media: audio and video keep their controls (`getPresentationState` shows `playback`), nothing
  plays sound on its own unless an `unmute-media` action comes first, and a video that carries the
  message is also explained in text (the tools cannot add captions).
- Nothing essential is shown only on hover or only after an animation; motion is subtle and
  consistent (`getSlideInfo` shows the slide transition, `getPresentationState` the animations and
  playback): flag long delays, many animated shapes on one slide, a different transition on every
  slide and effects on text learners must read quickly.
- Known app gaps (report as limits, not as the author's fault): the editor has no control for image
  alt text, speaker notes or video captions, so informative pictures cannot be described to screen
  readers; say what the picture shows in nearby text instead. How motion looks cannot be checked
  here, only what is set. Fixes go through `edit-presentation`.

### 5. Report

Give the user:

1. A verdict in one or two sentences: ready, ready after small fixes, or needs rework, and why.
2. The findings, most important first, in three groups: **fix before presenting** (errors, spills,
   wrong answers, leftover sample text), **should improve** (clarity, consistency, readability) and
   **nice to have**. Each finding: slide number, what, and the fix. Keep it a short table or list.
3. What you could not check (no slide pictures, audio and video content, text inside images,
   animations and transitions, and how interactions and games behave when played).
4. A question: which fixes should be made now. Offer to make all the "fix before presenting" ones.
   Also offer, without doing it unasked, to leave the findings as review comments on the slides
   (`addComment`, one per finding, written under the user's name). A review itself changes
   nothing: never add comments unasked and never resolve existing ones.

### 6. After the user chooses

Apply the chosen fixes with the `edit-presentation` workflow, then run `reviewSlides` and
`getSlideImage` on the changed slides and report what is now clean and what remains.

## Troubleshooting

| Symptom | Fix |
|---|---|
| "No presentation is open in an editor tab" | Ask the user to open the presentation and keep the tab open. |
| `getSlideImage` cannot draw the slide | Use the measurements and `getPresentationState` and say the pictures were not available. |
| `reviewSlides` returns `remaining` | Call it again with those slide ids. |
| A slide looks fine in the measurements but wrong in the picture | Trust the picture: report it as a design finding. |
