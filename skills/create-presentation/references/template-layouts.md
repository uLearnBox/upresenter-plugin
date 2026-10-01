# Template layouts: choosing, inserting and filling them

A uPresenter **template** is a library of designed slides in one visual style, not a finished deck.
Every slide of it is a **layout** with a tag, and its text slots hold a placeholder prompt and a
character budget. Building a deck from a template means picking the layouts that carry your
content, inserting only those, and replacing every placeholder with real content. The design
(colors, fonts, spacing, decor) comes with the layout and follows the deck's theme, so do not
restyle it.

Contents: 1 Reading a template, 2 Layout tags, 3 Slots and budgets, 4 Filling each kind of
content, 5 Choosing between variants, 6 Pitfalls

## 1. Reading a template

`listTemplates` returns, per template: `templateId`, `name`, `description`, `categories`,
`language`, `themeId`, `palette` (theme colors), `fonts`, `slideCount` and `layouts` (how many
slides it has per tag). Choose from these:

- the **description and categories** say what it is for (pitch, lesson, report, quiz...);
- `layouts` tells whether it has what the plan needs: a `timeline`, a `chart`, `list-5`, the
  `question-*` types of a quiz, or genre tags such as `problem`, `traction` or `key idea`;
- `language` is the language of the sample copy. Layouts work in any language, but for a
  Vietnamese or other non-English deck prefer a template whose `language` matches, and otherwise
  ask the user to check accents in the preview;
- `palette` and `fonts` give a feel for the style when the user asked for a mood ("dark",
  "playful", "formal").

`getTemplateLayouts {templateId, tag?}` returns every slide of the template (or only those with
the `tag`): `slideId`, `index`, `tag`, `name`, `itemCount` (list-N), `hasChart` and `chartShapeId`,
`tableShapeId` with `tableRows` and `tableColumns`, `hasInfographic` and `entryCount`, `imageSlots`,
`questionShapeId` and `questionType`, and the `slots`. Read it once per deck and note the `slideId`
of every layout you will use.

## 2. Layout tags

| Tag | Use it for |
|---|---|
| `title` | The cover: deck title, one-line subtitle, small labels (presenter, date). Usually first. |
| `title and content` | One idea in a headline plus a short paragraph (or a paragraph and a few caption cards). The default text slide. |
| `list-3` ... `list-6` | A list with exactly that many items, each with a short title and a sentence. Pick by the number of items you have. |
| `timeline` | Dated or ordered milestones (3-6 entries). Entry count is fixed by the layout. |
| `process` | Steps, a flow, a cycle (3-5 entries). |
| `comparison` | Two sides or options, before/after, pros/cons. |
| `chart` | A native chart plus one short insight text. Fill the numbers with `setChartData`. |
| `statistic` | A headline number, or a few KPI tiles. Only with a real figure. |
| `quote` | A quotation or testimonial with its author. Only with a real quote. |
| `table` | Structured facts in rows and columns. |
| `overview`, `summary` | An agenda or contents (`overview`), key takeaways (`summary`). |
| `gallery` | Pictures with captions: team, products, places. |
| `analysis`, `diagram`, `cards` | A chart or labelled diagram with notes; extra note cards. |
| `section` | A chapter divider in a long deck (10+ slides). |
| `end` | The closing slide: thank you, the ask, contacts, next step. Usually last. |
| `question-mc`, `-mr`, `-tf`, `-fte`, `-sq`, `-ma`, `-fb`, `-sl`, `-dd`, `-dw`... | A quiz question in the template's style. See the `create-quiz` skill. |
| genre tags (`problem`, `solution`, `traction`, `team`, `roadmap`, `ask`, `key idea`, `worked example`, `answer key`, `kpi`, `budget`, `swot`...) | Present only in some templates; use them when they match the planned slide, otherwise use the common tag. |

Rules of thumb: the tag follows the **shape of the content**, not the topic. Three parallel
points are a `list-3`, not a `title and content` with three bullet characters. A number worth
showing is a `statistic`. A sequence in time is a `timeline`. Do not use `chart`, `timeline`,
`statistic` or `quote` unless you have the real data, dates, figure or quote to put in.

## 3. Slots and budgets

Each `slots` entry is `{shapeId, role, maxChars?, minChars?, prompt?, sample?}`. A slot has either
a `prompt` or a `sample`:

- the `prompt` is what an **empty placeholder** shows until it is filled: it says what belongs
  there ("Company name · Presented by · Date") and how long it should be;
- the `sample` is text that **ships in the layout and is not marked as a placeholder**: a figure
  ("$48B"), a label ("Strengths"), an infographic entry. It looks like real content, so it must be
  overwritten. Keep about the same length as the sample: the box was sized for it.

Roles:

| Role | What goes in it | Typical budget |
|---|---|---|
| `title` | The slide headline: a short claim, not a topic word | cover 20-60 characters, text slides 20-60, list slides up to 80 |
| `subtitle` | One line under a cover or section title | up to 100 |
| `label` | A 1-3 word kicker above the title ("Roadmap", "02 - Market") | at most 24 |
| `content`, `text` | A paragraph, or a short caption or footer when it has no budget | `maxChars` when given; otherwise keep captions under 40 |
| `item-title` | The heading of a list item | 20-40 |
| `item-content` | The sentence under a list item | 40-270, see `maxChars` |
| `entry-label`, `entry-title`, `entry-text` | Timeline/process entries: a short date or step marker, a heading and a line | label a few characters, title up to about 30, text up to about 80 |

- **`maxChars` is a hard limit**: the box was sized for it. Count your text. Longer text spills
  out of its card or shrinks unreadably. If your text does not fit, shorten it, or pick a variant
  of the same tag with a roomier budget (section 5).
- **`minChars`**, when present, is the least that keeps the card from looking empty.
- A list's numbering badges (01, 02...) are automatic; do not write them.
- **Fill every slot or delete the shape.** A shape that still shows its prompt appears in
  `getPresentationState` with a `placeholder` field and renders as "Click to add title" in the
  editor. If the planned content has no use for a slot (a second caption, a footer), remove it
  with `deleteShape`.
- **Sample copy is not marked as a placeholder**, so an unfilled `sample` slot looks finished:
  the deck would show "$48B" and "Paying teams" in a deck about something else. Overwrite every
  `sample` slot. Text the template marks as design (numbering, a fixed label such as "TAM" or
  "Thank you") is not listed in `slots`; after filling, read the slide's texts in
  `getPresentationState` and change any leftover figure, date or percentage that does not fit
  your content (for example quarter labels on a roadmap or the shares of a use-of-funds bar).

## 4. Filling each kind of content

All text goes through `setShapeText {slideId, shapeId, text}`; a line break starts a new
paragraph. Take `slideId` and `shapeId` from the insert result and pass both.

- **Text, lists, timelines, process steps:** one `setShapeText` per slot. For a list, the number
  of items is the number of the layout (`list-4` has four): choose the layout that matches, do
  not leave an empty item.
- **Chart:** `setChartData {slideId, shapeId: chartShapeId, title?, labels, datasets: [{label,
  data}]}` replaces the sample figures with one number per label and keeps the chart type and
  style. Then write the insight into the layout's `content` slot (two or three short sentences
  about what the chart shows). `setChartText` only edits labels and titles.
- **Table:** the layout's table (`tableShapeId`, `tableRows` x `tableColumns`) holds sample data.
  First `editTableStructure` to add or remove rows and columns so the grid has the shape of your
  data, then one `setTableCellsText` with `cells: [{rowIndex, columnIndex, text}, ...]` for every
  cell, header row included (indexes start at 0).
- **Questions:** insert the `question-*` layout, `setShapeText` the question into the `title`
  slot, then `setQuestionAnswers` and `setQuestionSettings` with the returned `questionShapeId`.
- **Images:** layouts with `imageSlots` ship a sample picture that suits the template. Keep it
  unless the user gave you a picture: then `replaceImage {slideId, shapeId, source}` with that
  public HTTPS URL. Never invent image URLs. If a sample picture clashes with the topic, choose a
  variant without image slots and say so in the report.
- **Colors, fonts, sizes:** leave them. They come from the theme. To change the whole look, use
  another template (`applyThemeFromTemplate`), not per-shape styling.

## 5. Choosing between variants

A template often has several slides with the same tag (two or three `list-4`, four `title and
content`). They differ in composition and budgets. From `getTemplateLayouts` with the `tag`:

- take the one whose `maxChars` values hold your text (a variant with `item-content` 38 holds a
  phrase, one with 270 holds a paragraph);
- prefer one with no `imageSlots` when you have no suitable picture;
- vary: when a tag repeats in the deck, use a different variant each time (the `name` tells them
  apart), and avoid the same tag on two consecutive slides;
- first and last: a `title` variant that suits the tone, and an `end` variant whose slot count
  matches what you have to say (some `end` layouts carry contact lines, some only a thank-you).

## 6. Pitfalls

- **Inserting the whole library.** Insert only what the outline needs. A 10-slide request gets 10
  slides, whatever the template offers.
- **Mixing styles.** `insertTemplateSlides` without `templateId` uses the template the deck was
  created from, and a deck made with "Create Blank" starts from whatever template the dialog had
  selected. Pass the chosen `templateId` on every insert, and switch the deck's theme first with
  `applyThemeFromTemplate: true` (no slides needed), or the layouts look foreign. The theme change
  restyles the **whole** deck, so decide on the template before building and do not switch
  halfway.
- **Writing text before reading the budgets.** Draft the content in the plan, then fit it to the
  slots when you fill: shorten rather than overflow.
- **Leaving sample text behind.** After building, run `getPresentationState` with `scope:
  "all_slides"`: no shape may carry a `placeholder`, and no figure, label, table cell or
  timeline entry may still hold the template's sample.
- **Selecting shapes between calls.** Tools without a `slideId` act on the current slide; always
  pass `slideId` and `shapeId` together.
- **Counting characters.** Letters, spaces and punctuation all count. Non-English text is often
  longer than English: leave headroom.
