---
name: design-interactions
description: "Add interactivity to a uPresenter presentation: click-to-reveal, tabs, flip cards, tooltips and dialogs, hotspots on a picture, remembered reveals, variables with conditions, results screens by score tier, and progress bars bound to a variable, using the app's template layouts that already contain reveal, flip-card, explore or results structures before composing by hand. Use when the user asks for interactive slides, reveal on click, tabs, flip cards, flashcards, hotspot, tooltip, click to see the answer, branching on a score, a results screen or a progress meter in uPresenter, in any language (for example làm slide tương tác, thẻ lật, bấm để hiện đáp án, màn hình kết quả theo điểm, crear tarjetas giratorias, mostrar al hacer clic, 制作翻转卡片, 点击显示答案)."
---

# Design interactions in uPresenter

Interactions are events and actions stored on a slide or a shape and run by the player. The tools
add and edit them (`addInteraction`, `updateInteraction`, `removeInteraction`, `getInteractions`)
but cannot click through the player. Start from a template layout that already holds the
structure; compose by hand only when none does.

## Ground rules

- Call tools one at a time. Pass `slideId` with every `shapeId` (shape ids repeat on every slide)
  and use ids that tool results returned.
- The editor tab must be open: `listOpenEditorContexts`; if none, `getNewPresentationLink` (with
  a `templateId`, or `blank: true`) and ask the user to open it and keep the tab open, then check
  again. With several tabs pass the right `sessionId`.
- QA loop after each interactive slide: `reviewSlides`, then `getSlideImage`, then
  `getInteractions {slideId}`. Say that you could not play it.
- Fluid decks (scrolling cards) come only from fluid templates; never convert with `setLayoutType`.
  Overlapping tab panels and flip cards need absolute positioning, which fluid cards do not
  allow: in a fluid deck use stacked reveals (a hidden block shown in place) instead.
- Not available as tools: animations, slide transitions, narration or TTS, export, classes,
  reports. Point the user to the editor for these.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

1. **Name the interaction** the learner should have: reveal a fact, switch tabs, turn a card,
   explore a picture, branch on a choice or a score, see a result.
2. **Look in the templates first (template-first).** Open `references/interactive-templates.md`
   for the template layouts that already contain flip cards, explore toggles, decision points
   with branching and results screens, and how to find them: `listTemplates` (filters `query`,
   `category`, `language`, `layoutType`, `limit`), then `getTemplateLayouts {templateId, tag}`
   and look at `actionCount` (above zero means interactions are wired). If the open deck
   already comes from a suitable template, use its layouts; otherwise offer the user the template
   (with `previewUrl`) and open it with `getNewPresentationLink {templateId}`, or insert the
   layout into the open deck with `insertTemplateSlides {templateId, slides: [{tag}], index}`.
3. **Insert and fill**: `insertTemplateSlides` (always pass `templateId`), `setShapeText` for every
   slot, replace every `sample`, then `getInteractions {slideId}` to learn what is wired. Adjust
   copy counts by editing (`duplicateShape`, `deleteShape`) and fix targets with
   `updateInteraction`.
4. **Compose only when no layout fits.** Recipes in `references/recipes.md`: build inside a flow
   group (`addGroupLayout`, `selectShape` before every insert), `setShapeInitiallyHidden` for
   anything that starts hidden, then `addInteraction`.
5. **Variables, conditions, results.** `createVariable`, `adjust-variable` actions, conditions on
   actions, results screens by score tier and `addProgress` bound to a variable (recipes section
   6 to 8 of `references/recipes.md`).
6. **Check.** `getInteractions` for every slide you touched, `getVariables`, `reviewSlides`,
   `getSlideImage`. Report what each interactive slide does and which paths the user should try
   in the player.

## Core rules

- `addInteraction {slideId, shapeId?, eventType, actionType, actionData?, condition?}`; without
  `shapeId` the interaction belongs to the slide (`slide-enter`, `timer`).
- Events: `click`, `double-click`, `mouse-enter`, `mouse-leave`, `slide-enter`, `timer`,
  `question-correct`, `question-incorrect`, `question-answered`, `media-ended`.
- Actions on one event run in the order added; navigation does not stop the chain, so put show
  and hide before go-to.
- A shape cannot show, hide or toggle itself: target its group (a child may hide its parent).
- `hide` collapses the shape; initially hidden keeps its space until shown.
- Hover does nothing on phones and tablets: never hide essential content behind hover.
- `show-tooltip` is not allowed on `slide-enter`; `open-url` is blocked in public presentations.
- Operands are typed objects: `{"kind": "literal", "value": 10}` or `{"kind": "variable",
  "variableId": "..."}`, never a bare number. A condition is `{match: "all" | "any", rules:
  [{variableId, operator, operand}]}` and there is no else branch.

## Troubleshooting

- Click does nothing on a child: a child with any event (even an emptied list) swallows the click
  that would reach its parent group.
- Reveal vanishes when the learner returns: the slide is rebuilt on every visit; keep it with a
  boolean variable (recipes section 5).
- Panels shift when something hides: `hide` collapses; use initially hidden shapes or a fixed
  height.
- A jump goes to the wrong place after slides were deleted or copied: `getInteractions`, then
  `updateInteraction` with the new `slideId`.
- Tooltip text too long: `show-tooltip` is for a short note; use `open-dialog` for more.
