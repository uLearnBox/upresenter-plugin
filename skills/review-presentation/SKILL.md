---
name: review-presentation
description: "Audit an existing uPresenter presentation or lesson and report what to improve, without changing it: text that spills out of cards, leftover template sample text and unfilled placeholders, math typed as plain characters, weak story or structure, readability and contrast, inconsistent design, quiz mistakes and tracking settings. Use when the user asks to review, check, proofread, critique, QA or give feedback on a presentation in uPresenter (for example kiểm tra bài giảng, revisar la presentación), or before they share or publish one."
---

# Review a presentation in uPresenter

A review answers one question for the user: is this deck ready to present, and if not, what exactly
should change? It looks at the deck the way a viewer does (the rendered slides), and measures what the
eye cannot judge. **Report first; change nothing until the user says which fixes they want.** Then
apply them with the `edit-presentation` workflow and review the changed slides again.

The presentation must be open in an editor tab (step 1 of `edit-presentation`). Call tools one at a
time, and pass `slideId` and `shapeId` together.

## Workflow

### 1. Gather

- `getPresentationInfo` and `getPresentationState {scope: "presentation_basic"}`: name, language,
  size, slide count, theme colors and fonts, positioning mode.
- `getPresentationState {scope: "all_slides"}`: every text, its slide and shape, `placeholder`,
  `maxChars`, `formulas`.
- `getQuestionsInfo {includeAnswers: true}`, `getPresentationSettings`.
- `getChartInfo` and `getTablesInfo` where a slide has them.

### 2. Measure

`reviewSlides {}`; if the result has `remaining`, call it again with those `slideIds`. It reports
text spilling out of its box, card or the slide, text lying on other text, unfilled placeholders and
math typed as plain characters.

### 3. Look

`getSlideImage {presentationId, slideId}` for each slide (up to about 15 slides; in a longer deck one
slide of each layout plus every slide the measurement flagged). `presentationId` comes from
`listOpenEditorContexts`. If the tool cannot draw slides, say so in the report and review from the
measurements and the content alone.

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

### 5. Report

Give the user:

1. A verdict in one or two sentences: ready, ready after small fixes, or needs rework, and why.
2. The findings, most important first, in three groups: **fix before presenting** (errors, spills,
   wrong answers, leftover sample text), **should improve** (clarity, consistency, readability) and
   **nice to have**. Each finding: slide number, what, and the fix. Keep it a short table or list.
3. What you could not check (no slide pictures, audio and video content, text inside images).
4. A question: which fixes should be made now. Offer to make all the "fix before presenting" ones.

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
