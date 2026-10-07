# Review loop (step 9 of the create-presentation workflow)

Never report a deck you have not reviewed. `reviewSlides` measures how each slide really renders,
`getSlideImage` shows the slide as a picture, `getPresentationState` and `getQuestionsInfo` check
the content.

In a fluid presentation the "slides" are cards that grow with their content: text cannot run past
the edge of a card's slide, but it can still spill out of its own box or an inner card, and
`getSlideImage` draws one whole card, as tall as it is (`fluid.md`, section 7).

## 1. Measure

`reviewSlides {}` for the whole deck. It reviews up to 40 slides per call: while the result has
`remaining`, call it again with those `slideIds`. Each issue has a `kind`, the `shapeId` and, for
spills, `spillPx`.

| kind | What is wrong | Fix |
|---|---|---|
| `placeholder` | A slot still shows its prompt | `setShapeText` with the content, or `deleteShape` |
| `spillsOutOfBox`, `outsideCard` | Text runs past its own box or the card it sits in | Shorten the text (within `maxChars`, shorter in languages with long words) or move the slide to a variant with a bigger budget; `setTextStyle` with a smaller size only as a last resort and never below about 75% |
| `outsideSlide` | Text runs past the edge of the slide | Same; if the content is too much for one slide, split it |
| `overlapsText` | Two texts lie on top of each other (`shapeId`, `otherShapeId`) | Shorten the longer one or choose another layout |
| `plainMath` | Math typed as plain characters | Rewrite as `[[katex:LATEX]]` (`math-formulas.md`) |

Fix, then `reviewSlides {slideIds: [the problem slides]}` again. At most three rounds. Tell the
user about anything you could not fix.

## 2. Look

`getSlideImage {presentationId, slideId}` slide by slide (every slide up to about 15; in a longer
deck one slide of each layout plus every slide you changed). `presentationId` is in
`listOpenEditorContexts`. Ask of each picture: is all text readable and fully inside its card; is
the contrast enough (light text on a light picture is a fault); do formulas show as typeset math;
are there empty or crowded areas or clashing pictures; does the slide look like the others; does a
sample picture show the wrong subject; does a chart still carry a sample title or legend; does a
leftover sample figure or label remain? The picture is drawn from the open editor's current state (the saved state when no editor is open;
sample text such as "Click to add title" shows as placeholder text), so look again after your
edits. If `getSlideImage` cannot draw a slide, carry on with the measured review and the content
checks.

## 3. Content checks

`getPresentationState {scope: "all_slides"}` and `getQuestionsInfo {scope: "all-slides",
includeAnswers: true}` (without `scope` only the current slide is read):

- no figure, label, table cell or timeline entry still holds the template's sample (dates,
  percentages and numbers from the sample must match your content);
- every text fits its `maxChars`; each headline states its message;
- the order matches the outline; every graded question has a correct answer;
- no two neighbouring slides repeat the same layout without a reason;
- interactions and variables are wired as planned (`getInteractions {slideId}`, `getVariables`):
  the tools cannot play the deck, so check the wiring.

## Report (step 10)

One line per slide: what was created and which template was used; that the deck was reviewed and
what you fixed; which features were used; and what needs the user's input: problems you could not
fix, facts to confirm, pictures to replace (list the slides whose sample picture stays), generated
pictures and the credits left, accents to check in a non-English deck, interactions and games to
test in the player, and what was asked but is not possible with the tools (exports, and narration
without `addNarration`). `getSlideImage` draws at most 1920 x 1080, so very tall or portrait presets (the social
story and A4 portrait sizes) come back cropped: say so instead of judging their edges. Suggest next steps: preview in the player, share, export
for an LMS from the editor, or restyle with another template.
