# Fluid templates and card libraries

Contents: 1 What a fluid presentation is, 2 Finding fluid templates, 3 Planning and filling
cards, 4 Composing a card, 5 What not to do, 6 Review

## 1. What a fluid presentation is

| | Slide presentation | Fluid presentation |
|---|---|---|
| Shape | Fixed 16:9 slides (1280 x 720) | A scrolling document of **cards** as tall as their content |
| Layout | Positioned shapes (freeform) or flow | Flow layout: blocks stack, columns reflow |
| Reads well on | Projector, laptop | Phone, tablet, laptop (responsive) |
| Text limits | `maxChars` per slot | none: the card grows |

The player shows one card at a time and the reader scrolls inside a long card. The content width
is capped by the fluid max width (default 1280px; `setFluidMaxWidth`). The page background shows
around the cards. Authored slide transitions do not animate a tall card.

## 2. Finding fluid templates

`listTemplates {layoutType: "fluid"}` lists them (`query`, `category`, `language`, `limit` still
apply; `total` says how many matched). They are card libraries with the usual layout tags
(`title`, `title and content`, `list-3` to `list-6`, `timeline`, `process`, `comparison`, `chart`,
`table`, `gallery`, `summary`, `section`, `end`, `quote`, `statistic`, `overview`) plus tags of
their kind of document, and the `question-*` card layouts.

The template packs in the app repository (`server/templates/presentations`) hold two, both
English sample copy and category Business. The live gallery is the authority: confirm with
`listTemplates {layoutType: "fluid"}`.

| Template (gallery name) | Good for | Document tags beyond the common ones |
|---|---|---|
| Employee Handbook, Clean Corporate (fluid) | handbook, policy, onboarding, FAQ, lessons for staff | `welcome`, `milestones`, `analysis`, `contents`, `policy`, `do and dont`, `benefits`, `leave`, `who to contact`, `faq`, `glossary`, `acknowledgement`, `contact` |
| Project Report, Swiss Editorial (fluid) | report, case study, research summary, lesson as an article | `executive summary`, `contents`, `project overview`, `key figures`, `budget`, `regional table`, `milestones`, `risks`, `story`, `lessons`, `conclusion`, `next steps`, `acknowledgements`, `contact` |

There are few fluid templates: suggest two when you can, say how they differ, give each
`previewUrl`, and add as the last option a fluid deck designed from scratch. If none fits the
topic, the closest document-like one still serves most subjects: its layouts are generic cards
you fill with your own content. Tell the user this is a choice from a small set. For a different
look:

- **Borrow a style.** Build from a fluid template, then switch the colors and fonts to those of
  another template: `insertTemplateSlides {templateId: <template in that style, slide or fluid>,
  applyThemeFromTemplate: true}` with no `slides`. The cards follow the theme. Slide themes are
  not tuned for cards: look at the cards afterwards, and set a quiet page background with
  `setPresentationBackground` if the theme's is harsh.
- **The user's own theme.** The user creates a blank fluid presentation in the dashboard (Create
  Blank, Fluid Layout, pick a theme), then you insert the cards of the closest fluid template
  **without** `applyThemeFromTemplate`: the cards take the deck's own theme.
- **From scratch.** `getNewPresentationLink {blank: true, layoutType: "fluid", themeId}` gives an
  empty fluid deck (one blank card, no layouts); `listThemes` and `createTheme` set the style;
  compose cards by section 4.

## 3. Planning and filling cards

Think document sections, not slides: cover, introduction or summary, sections in reading order,
conclusion or next steps. Each card holds one idea in two to six blocks (heading, paragraph, list,
image, chart, callout) and is about 400 to 1200px tall on a desktop. Choose each card's layout by
the shape of its content: three parallel points `list-3`, a sequence `timeline` or `process`,
figures `chart` or `statistic`, two options `comparison`, a rule `policy`, a question
`question-*`.

Fill as for slides:

- every slot and every `sample` gets real content (`setShapeText`); a slot you do not need goes
  with `deleteShape`;
- `setChartData` for charts, `editTableStructure` then `setTableCellsText` for tables;
- math as LaTeX in `[[katex:...]]` where the deck has formulas;
- pictures are usually wide banners at the top of a card or beside the text; a sample picture
  stays unless the user gives you one (`replaceImage`) or the connection offers
  `generateShapeImage` (ratio 16:9 for banners, 4:3 beside text); report which stay;
- one question per card for quizzes, with `setQuestionSettings`; fluid slots have no `maxChars`
  but keep text proportionate.

## 4. Composing a card

For a card no layout carries (a reveal activity, a media card, a results card): `addSlide
{slideType: "blank", index}`, then build it in flow:

- one `addGroupLayout {layout: "column", gap: 24}` holding the card, and `selectShape {slideId,
  shapeId: group}` before each insert (after an insert the new shape is selected, so without it
  the next one lands outside the group; for lists, charts, questions and tables check the parent
  with `getPresentationState {scope: "current_slide"}`);
- card padding at most 48px and group padding at most 32px (padding is not reduced on phones);
  gaps 16 to 32px;
- text at a reading width: the card column (`setFluidMaxWidth`, 960 to 1100px) or a `col-2` /
  `ratio-2-1` column, about 75 characters per line at most;
- a banner picture with `objectFit: "cover"` and a fixed height of 320 to 420px; a side picture
  in a `col-2` with an explicit height;
- reveals and answers stay inside the same card (`design-interactions` if installed: use stacked
  reveals, not overlapping tabs or flip cards, which need absolute positioning);
- dividers and accent bars short and start-aligned (`addDivider`).

## 5. What not to do

- Do not convert a slide deck with `setLayoutType`: only the sizing mode changes.
- No x/y placement, no `setShapePositioningMode` absolute, no fixed pixel widths on text: they do
  not reflow on a phone.
- No `setSlideSize`: a fluid presentation has no fixed slide size.
- No wide tables: a table does not reflow; four columns or fewer, or give it its own card.
- Do not stack many tiny blocks in a row: on a phone every `col-N` group becomes one column.
- Do not build a whole fluid deck from blank shapes when a fluid template card exists.

## 6. Review

`reviewSlides` works on cards: it reports unfilled placeholders, overlaps, plain-character math
and text spilling out of its box or an inner card. Nothing runs past the edge of the card itself,
because a card grows with its content. `getSlideImage` draws one whole card at desktop width, as
tall as the card: look at every card. You cannot see the phone layout: say so in the report, and
note the layouts stack on narrow screens as long as they stay in flow. To compare widths, set
`setFluidMaxWidth` to a narrower value, look again, and restore the chosen value.
