---
name: build-fluid-lesson
description: "Build a fluid (responsive, scrolling card document) lesson, report, handbook or guide in uPresenter from the app's fluid templates and card libraries (Employee Handbook, Project Report and any other template listed with layoutType fluid), with setFluidMaxWidth, container layouts, flow positioning and a responsive check with getSlideImage. Use when the user asks for a fluid or scrolling presentation, a mobile-first or responsive lesson, a document-like course, a handbook, report or guide in uPresenter, in any language (for example tạo bài giảng dạng cuộn/fluid, tài liệu responsive trên điện thoại, crear una presentación fluida, documento con tarjetas, 制作流式滚动演示文稿, 响应式课程)."
---

# Build a fluid lesson in uPresenter

A fluid presentation is a scrolling document made of **cards** that are as tall as their content
and reflow on phones. It is a different kind of presentation from fixed 16:9 slides, and it is
built from **fluid templates**, never by converting a slide deck.

## Ground rules

- Call tools one at a time. Pass `slideId` with every `shapeId` (shape ids repeat on every card)
  and use ids that tool results returned.
- The editor tab must be open: `listOpenEditorContexts`; if none, `getNewPresentationLink` and ask
  the user to open it and keep the tab open, then check again. With several tabs pass the right
  `sessionId`.
- QA loop after every card or group of cards: `reviewSlides`, then `getSlideImage` (one whole
  card at desktop width) and look at each card.
- **Fluid decks come only from fluid templates** (or from a blank fluid deck the link creates).
  Never use `setLayoutType` to turn a slide deck into a fluid one: it changes only the sizing
  mode, the 16:9 layouts stay as they are and the result is a slide deck in disguise. If the
  open deck is a slide deck, do not build in it: give the user a new fluid link.
- Not available as tools: animations, slide transitions, narration or TTS, export, classes,
  reports. Point the user to the editor for these.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

1. **Plan the document.** About 8 to 14 cards: cover, introduction or summary, sections in reading
   order, conclusion or next steps. One idea per card, two to six blocks each. Choose each card
   by the shape of its content. Add a question card where the lesson needs a check.
2. **Find the fluid template (template-first).** `listTemplates {layoutType: "fluid"}`: without
   `layoutType` you only see slide templates. Add `query` or `category` to narrow, check `total`.
   Offer two options with `previewUrl` and how they differ (`references/fluid-templates.md` lists
   what exists and the card tags), recommend one, and wait. As a last option offer a fluid deck
   designed from scratch (`getNewPresentationLink {blank: true, layoutType: "fluid", themeId?}`).
3. **Create the deck.** `getNewPresentationLink {templateId}` with the fluid template creates a
   fluid presentation from it. After the user opens it: `listOpenEditorContexts`, then
   `getPresentationInfo`: `layoutType` must be `fluid` and `positioningMode` `auto`. If not, stop
   and ask the user to open a new fluid link. `setPresentationName`, `setPresentationLanguage`.
4. **Read the cards.** `getTemplateLayouts {templateId}` (fluid slots have no `maxChars`: the
   card grows). Choose a `slideId` per planned card; vary variants when a tag repeats.
5. **Insert the cards in one call**: `insertTemplateSlides {templateId, slides: [{slideId}...],
   index}`. Always pass `templateId`. It refuses to put fluid cards into a slide deck and slide
   layouts into a fluid deck. Delete the starter card if it is unused.
6. **Fill every card**: `setShapeText` for each slot, replace every `sample` (figures, labels,
   timeline entries, chart titles, table cells), `setChartData`, table tools, `setQuestionAnswers`
   and `setQuestionSettings` for questions. Keep text proportionate: a heading of a few words, a
   paragraph of two to four sentences. A card longer than about two screens should be split.
7. **Width and background.** `setFluidMaxWidth {maxWidth}` sets the content width cap (CSS
   string such as "1024px" or "100%"; the default is 1280px). Use 960 to 1100px for reading
   comfort. If the theme's page background is harsh, set a quiet one with
   `setPresentationBackground`.
8. **Cards no layout carries** (a reveal, a media card, a results card): compose in flow, see
   `references/fluid-templates.md` section 4.
9. **Responsive check.** `getSlideImage` shows the desktop layout only. Check every card; say that
   the phone layout cannot be seen with the tools and that groups are built to stack on narrow
   screens as long as they stay in flow. `reviewSlides` flags text that spills out of its box or
   an inner card and unfilled placeholders.
10. **Report**: template and why, card list, what is sample content that stayed (pictures),
    and what the tools cannot do (phone preview, animations, export).

## Flow rules (what keeps a card responsive)

- Positioning mode stays `auto`. No x/y placement, no `setShapePositioningMode` absolute, no fixed
  pixel widths on text, no `setSlideSize`.
- Containers: `setContainerLayout {slideId, shapeId?, layout, gap, alignItems, justifyContent}`
  with `row`, `column`, `col-2` to `col-6`, `ratio-2-1-1`, `ratio-1-1-2`, `ratio-1-2`, `ratio-2-1`,
  `tiles-2-1`, `tiles-1-2`, `tiles-2-1-col`, `tiles-1-2-col`. Without `shapeId` it lays out the card.
- Breakpoints: at 1024px and below `col-4` to `col-6` become two columns; at 768px and below
  every `col-N`, `ratio-*`, `tiles-*` and `row` becomes one column. Order blocks so the stacked
  order still reads well (heading before its picture, numbers before their text).
- Card padding 48px at most, group padding 32px at most, gaps 16 to 32px.

## Troubleshooting

- The tool says fluid cards cannot go into this deck: the deck is a slide deck; open a fluid link.
- A card looks like a slide: it came from a slide template or `setLayoutType` was used; rebuild
  from the fluid template.
- Text runs too wide: lower `setFluidMaxWidth` or wrap the text in a `col-2` or `ratio-2-1`.
- A wide table is cut or does not reflow: keep it to four columns or fewer, or give it its own card.
- Picture too tall on a phone: use a banner with `objectFit: "cover"` and a fixed height of 320
  to 420px.
