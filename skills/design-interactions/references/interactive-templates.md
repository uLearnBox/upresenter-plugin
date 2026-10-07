# Template layouts that already contain interactions

Contents: 1 How to find them, 2 What the packs contain, 3 Reading and reusing a wired layout

## 1. How to find them

- `listTemplates` takes `query` (text over name, description, categories), `category` (exactly one
  of Business, Education, Game, Quiz, Healthcare, Nonprofit, Design, Marketing, Travel, any case),
  `language` (of the sample copy), `layoutType` (`"slide"` default, `"fluid"`), `includeLegacy`
  and `limit` (default 20, at most 50). Results are sorted newest first and `total` says how many
  matched: compare it with the number returned.
- The `layouts` field of each result counts slides per tag, so a template with a `flip cards` or
  `results` tag shows it there without another call.
- `getTemplateLayouts {templateId, tag}` returns the slides with that tag, each with `slideId`,
  `actionCount` (interactions wired into the slide), slots and, for questions, `questionType`.
  `actionCount` above zero means the structure works as soon as the texts are filled.
- `previewUrl` shows every slide; give it to the user next to each suggestion.

## 2. What the packs contain

Tags and wiring below come from the template packs in the app repository
(`server/templates/presentations`); `actions` counts the actions inside the tagged slide. The
live gallery is the authority: confirm with `listTemplates` and `getTemplateLayouts`.

| Structure | Template (gallery name) | Tag | Wiring in the pack |
|---|---|---|---|
| Flip cards (front and back, click to turn) | Halloween, Midnight Pumpkin Patch; Vietnamese Teachers' Day, Golden Chalk | `flip cards` | about 24 actions: `show` and `hide` pairs |
| Flash card (one card, reveal the back) | Last One Standing, Kawaii Pastel | `flash card` | 4 actions: `show` and `hide` |
| Explore toggles (click to show or hide) | Storytime, Early Years | `explore` | 4 actions: `toggle-visibility` |
| Hotspot picture (layout only) | Patient Education, Medical Clinical | `hotspot` | none: add the hotspot question or the click targets yourself |
| Branching decision | Workplace Safety, Brutalist Poster | `decision point` (with `consequence`, `ending`) | 10 to 15 actions: `adjust-variable`, `show`, `hide`, `next-slide` |
| Score and risk meters | Workplace Safety, Brutalist Poster | `safety score`, `risk meter` | variables `safety`, `risk`, `hazards`, `ppe`; one `next-slide` |
| Results with finish and restart | Onboarding and Compliance, Clean Corporate | `results` | 2 actions: `finish-presentation`, `go-to-first-slide` |
| Results (layout only) | Thesis Defence, Academic Scholarly | `results` | none: wire it |
| Answer reveal, board, clue, scoreboards | Jeopardy Scoreboard, Tech Neon | `answer reveal`, `jeopardy board`, `team scoreboard` | see `build-game` |

Every template also carries the question layouts `question-mc`, `question-mr`, `question-tf`,
`question-fte`, `question-sq`, `question-ma`, `question-dd`, `question-fb`, `question-dw`,
`question-sl` (some also `question-es`, `question-hs`, `question-lb`, `question-mdd`), which are
graded or survey questions and need no interaction wiring.

Tabs have no dedicated template tag in the packs: compose tabs (recipes section 3) or use an
`explore` layout with one toggle per panel.

## 3. Reading and reusing a wired layout

1. Insert it: `insertTemplateSlides {templateId, slides: [{slideId}], index}` (always pass
   `templateId`; without it the tool uses the deck's own starter template).
2. `getInteractions {slideId}` lists every event and action with `eventIndex` and `actionIndex`
   and the target shapes. Read it before editing: the layout may hide shapes at start by
   `setShapeInitiallyHidden` and rely on the order of its actions.
3. Fill all texts; keep the structure. To change the count of cards, `duplicateShape` a whole
   card group, then check with `getInteractions` that the copy's actions target the copy's shapes
   and fix them with `updateInteraction` (not verified how duplicates re-point their targets), or `deleteShape` a group and `removeInteraction` the actions
   that targeted it.
4. If the layout gives structure but no wiring (`hotspot`, some `results`), add the interactions
   from `recipes.md`.
5. Layouts carry their own design. Prefer layouts from the deck's own template; mixing in a
   layout from another template is possible but check it with `getSlideImage`.
