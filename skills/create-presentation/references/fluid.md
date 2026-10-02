# Fluid presentations (scrolling card documents)

uPresenter builds two kinds of presentation, and they are not interchangeable:

| | Slide presentation | Fluid presentation |
|---|---|---|
| Shape | Fixed 16:9 slides (1280 x 720) | A scrolling document made of **cards** that are as tall as their content |
| Layout | Positioned shapes (freeform) | Flow layout: blocks stack, columns reflow |
| Reads well on | Projector, laptop | Phone, tablet, laptop (responsive) |
| Text limits | `maxChars` per slot | none: the card grows |

When the user says fluid, scrolling, "dạng cuộn", responsive, document-like, mobile-first or
"fluid layout", they want the second kind. **Never build it by taking a slide template and
switching it with `setLayoutType`:** that only changes the sizing mode, and the layouts, which are
fixed 16:9 compositions, stay as they are, so the result is a slide deck in disguise. Fluid
presentations are built from **fluid templates**.

Contents: 1 Finding a fluid template, 2 Creating the presentation, 3 Planning cards, 4 Filling
cards, 5 What not to do, 6 Review

## 1. Finding a fluid template

`listTemplates {layoutType: "fluid"}` lists them (without `layoutType` you only ever see slide
templates). They are card libraries with the usual layout tags (`title`, `title and content`,
`list-3` to `list-6`, `timeline`, `process`, `comparison`, `chart`, `table`, `gallery`,
`summary`, `end`...) plus tags for their kind of document (a report has `executive summary`,
`key figures`, `budget`, `risks`; a handbook has `policy`, `faq`, `glossary`). There are few of
them, so suggest two when you can and say how they differ, with the `previewUrl` of each, as for
slide templates.

If none fits the topic, the closest document-like one (a report or a handbook) still serves most
subjects: its layouts are generic cards, and you fill them with your own content. Tell the user
this is a choice from a small set. If they want a look the templates do not have, they can create
a blank fluid presentation themselves (Create Blank, Fluid Layout, pick one of the themes). Then
insert the cards of the closest fluid template into it **without** `applyThemeFromTemplate`: the
cards take the deck's own theme. Do not build fluid cards from blank shapes (`build-from-blank.md`
is for slides).

## 2. Creating the presentation

`getNewPresentationLink {templateId}` with the fluid template gives a link that **creates a fluid
presentation** from it (the result says `layoutType: "fluid"`). The user opens it and keeps the
editor tab open. Check with `getPresentationInfo`: `layoutType` must be `fluid` and
`positioningMode` `auto`. If the deck the user already has open says `slide`, do not build in it
and do not convert it: ask them to open the link instead, which makes a new fluid presentation.

`insertTemplateSlides` refuses to put fluid cards into a slide presentation and slide layouts into
a fluid one, with an error that says what to do instead.

## 3. Planning cards

Think document sections, not slides. About 8-14 cards. Each card holds one idea, in 2-6 blocks
(heading, paragraph, list, image, chart, callout), and is about 400-1200 px tall on a desktop. An
outline is a table of contents: cover, introduction or summary, the sections in reading order, a
conclusion or next steps. Choose each card's layout by the shape of its content, exactly as for
slides (`template-layouts.md`, section 2).

## 4. Filling cards

Everything about slots works as for slides (`template-layouts.md`): fill every slot and every
`sample`, replace sample figures, `setChartData` for charts, `[[katex:...]]` for math. Differences:

- Fluid slots have **no `maxChars`**: the card grows. Keep the text proportionate anyway: a heading
  of a few words, a paragraph of two to four sentences, list items of one or two lines. A card
  longer than about two screens is better split into two cards.
- The player shows one card at a time and the reader scrolls inside it, so a card should still
  make sense on its own.
- Pictures are usually wide banners at the top of a card or beside the text. A sample picture
  stays unless the user gives you one (`replaceImage`) or the connection offers
  `generateShapeImage` (a landscape ratio such as 16:9 or 3:2 for banners).
- One question per card for quizzes (`create-quiz`).

## 5. What not to do

- No x/y placement, no `setShapePositioningMode` absolute, no fixed pixel widths on text: they do
  not reflow on a phone.
- No `setSlideSize`: a fluid presentation has no fixed slide size.
- No wide tables: a table does not reflow, keep it to four columns or fewer, or give it its own
  card.
- Do not stack many tiny blocks in a row: on a phone every `col-N` group becomes one column.

## 6. Review

`reviewSlides` works on cards: it reports unfilled placeholders, overlaps and plain-character math.
It cannot report a text spilling out, because a card grows with its content. `getSlideImage` draws
**one whole card** at desktop width, as tall as the card, so look at every card. You cannot see the
phone layout: say so in the report, and note that the layouts are built to stack on narrow screens
as long as they stay in flow.
