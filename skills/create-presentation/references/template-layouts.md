# Template layouts: choosing, inserting and filling them

A uPresenter **template** is a library of designed slides in one visual style, not a finished deck.
Every slide of it is a **layout** with a tag, and its text slots hold a placeholder prompt and a
character budget. Building a deck from a template means picking the layouts that carry your
content, inserting only those, and replacing every placeholder with real content. The design
(colors, fonts, spacing, decor) comes with the layout and follows the deck's theme, so do not
restyle it.

Contents: 1 Finding and reading a template, 2 Layout tags, 3 Slots and budgets, 4 Filling each
kind of content, 5 Choosing between variants, 6 Pitfalls, 7 Game and quiz-show templates

Everything here is about **slide** templates (the default). A **fluid** presentation (scrolling
cards) has its own templates, listed with `layoutType: "fluid"`: see `fluid.md`. The tags and slots
work the same way, but fluid slots have no `maxChars`.

## 1. Finding and reading a template

`listTemplates` returns up to `limit` templates (default 20, at most 50), sorted by name, and
`total`, the number that matched. The gallery has about forty themed templates, so a call without
filters shows only the first half: narrow before raising `limit`, and check that `total` is not
larger than what came back.

- `query` is a text search over name, description and categories: use it for a genre, a topic or
  a style ("pitch", "lesson", "Swiss Editorial", "report"). Most template names end with their
  style, and `styles.md` lists the query that finds each style.
- `category` must equal one of the gallery's categories exactly (any case): Business, Education,
  Game, Quiz, Healthcare, Nonprofit, Design, Marketing, Travel. A mood or genre word there
  ("dark", "pitch") matches nothing.
- `language` is the language of the sample copy. Most templates carry English sample copy and only
  a few are Vietnamese, so it narrows hard: try with and without it.

Per template it returns `templateId`, `name`, `description`, `categories`, `language`, `themeId`,
`palette` (theme colors), `fonts`, `slideCount`, `layouts` (how many slides it has per tag),
`previewUrl` and `variableCount`. Choose from these:

- `previewUrl` opens a page that shows every slide of the template: give it to the user next to
  each suggestion, so they can look before they choose;
- `variableCount` above zero marks a **game template**: a library with game screens as well as
  ordinary layouts in the game look (section 7);
- the **description and categories** say what it is for (pitch, lesson, report, quiz...);
- `layouts` tells whether it has what the plan needs: a `timeline`, a `chart`, `list-5`, the
  `question-*` types of a quiz, or genre tags such as `problem`, `traction` or `key idea`;
- `language` is the language of the sample copy. Layouts work in any language, but for a
  Vietnamese or other non-English deck prefer a template whose `language` matches, and otherwise
  ask the user to check accents in the preview;
- `palette` and `fonts` give a feel for the style when the user asked for a mood ("dark",
  "playful", "formal").

`getTemplateLayouts {templateId, tag?}` returns every slide of the template (or only those with
the `tag`): `slideId`, `index`, `tag`, `name`, `actionCount` (interactions wired into the slide),
`itemCount` (list-N), `hasChart` and `chartShapeId`,
`tableShapeId` with `tableRows` and `tableColumns`, `hasInfographic` and `entryCount`, `imageSlots`,
`questionShapeId` and `questionType` (the `addQuestion` name, such as `multiple-choice`), and the
`slots`. Read it once per deck and note the `slideId` of every layout you will use. If it says the
deck's own template is not available (it was taken out of the gallery), insert from it by `tag`
without `templateId`.

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
| `title` | The slide headline: a short claim, not a topic word | 20-60 characters; `maxChars` when given |
| `subtitle` | One line under a cover or section title | up to 100 |
| `label` | A 1-3 word kicker above the title ("Roadmap", "02 - Market") | at most 24 |
| `content`, `text` | A paragraph, or a short caption or footer when it has no budget | `maxChars` when given; otherwise keep captions under 40 |
| `item-title` | The heading of a list item | 20-40 |
| `item-content` | The sentence under a list item | one or two sentences within `maxChars` (often 60-120) |
| `entry-label`, `entry-title`, `entry-text` | Timeline/process entries: a short date or step marker, a heading and a line | label a few characters, title up to about 30, text up to about 80 |

- **`maxChars` is a hard limit**: the box was sized for it. Count your text. Longer text spills
  out of its card or shrinks unreadably. If your text does not fit, shorten it, or pick a variant
  of the same tag with a roomier budget (section 5).
- **`minChars`**, when present, is the least that keeps the card from looking empty.
- A list's numbering badges (01, 02...) are automatic; do not write them.
- **Fill every slot or delete the shape.** A shape that still shows its prompt appears in
  `getPresentationState` with a `placeholder` field and renders as "Click to add title" in the
  editor. If the planned content has no use for a slot (a second caption, a footer), remove it
  with `deleteShape {slideId, shapeId}`. Shape ids repeat on every slide, so the `slideId` is what
  makes it delete the slot on that slide and not a shape of the current one.
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
  The answers keep the layout's designed answer cards; extra answers copy the last card.
- **Images:** layouts with `imageSlots` ship a sample picture that suits the template. Keep it
  unless the user gave you a picture: then `replaceImage {slideId, shapeId, source}` with that
  public HTTPS URL. Never invent image URLs. If a sample picture clashes with the topic, choose a
  variant without image slots and say so in the report.
- **Generated pictures (only when the connection offers `generateShapeImage`).** It fills a picture
  slot from a text prompt and **spends the account's credits for every picture**; its description
  lists the models and the prices. So:
  1. Plan which slots get a generated picture: only slots with no picture from the user and no
     suitable sample. Prefer fewer, larger pictures: a cover and a few key slides, not every card.
     One picture can serve several slots: `replaceImage {slideId, shapeId, source}` with the
     stored image name the generation returned costs nothing.
  2. Before generating, tell the user how many pictures, which model and what they cost in
     credits, and wait for a yes. Stay within what they agreed to.
  3. One call per picture: `generateShapeImage {slideId, shapeId, prompt, model, ratio}`, with the
     slot's ids (from `imageSlots` of `insertTemplateSlides`, or `addImagePlaceholder`). **Always
     pass `model`**: without it a template picture slot reuses the model saved on it (often `fu`,
     60 credits), not the cheap default. Pass the `ratio` closest to the slot (`16:9` for a
     full-bleed or banner slot, `3:4` for a half-slide column, `1:1` for a square card).
  4. Write the prompt in English: one clear subject, setting, light and composition. Describe what
     you want rather than what you do not want: models ignore "no text", so ask for "plain
     unbranded surfaces, solid-color clothing without markings". For people say "waist-up
     portrait" or "full body in frame" and keep poses simple; avoid hands holding small objects
     and crowds. The deck theme's picture style, when it has one, is added to the prompt by the
     app: describe the subject, not the style. Pick the model for the job: `fu` for photographs
     (`fp` cheaper), `rc3` for illustrations, `ig3b` when the picture must contain readable words,
     `fs` (cheap) only for scenery and objects without people.
  5. Look at every generated picture with `getSlideImage`. Distorted hands or faces, gibberish
     lettering or a picture that does not match the slide: regenerate that one picture once, and
     ask before spending credits a second time. The slide picture is small: say in the report
     that faces and hands are worth a closer look in the editor.
  6. In the report list the generated pictures and the credits left (`creditsRemaining`).
- **Math:** write each equation, power or fraction as `[[katex:LATEX]]` inside the text, in any
  text slot, answer or table cell: it becomes a typeset formula. Never type math as plain characters
  (`x^2`, `x²`). Details: `math-formulas.md`.
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

- **A different slide size.** Templates are 1280 x 720. In a deck of another size
  `insertTemplateSlides` still inserts the layouts but returns a `warnings` entry: set the size
  first with `setSlideSize`, or choose another template, and tell the user.

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
- **Shape ids without a slide.** Shape ids repeat on every slide: pass `slideId` with `shapeId` on
  every call (the editing tools take both). Inserts (`add*`) go to the current slide.
- **Adding shapes to a template deck.** Shapes added to a template deck land on top of each other
  at a default spot, because such decks are freeform. Compose extra slides with the skeleton in
  `composed-slides.md`.
- **Legacy templates.** `includeLegacy: true` lists older packs without a theme of their own:
  `applyThemeFromTemplate` fails on them, so use one only for a deck that already has the look you
  want, or not at all.
- **Skipping the review.** `maxChars` is the template's estimate. Run `reviewSlides` after filling:
  it measures the real rendering and names any text that spills out of its card or the slide. Then
  look at the slides with `getSlideImage`: only the picture shows a clumsy layout or a leftover
  sample.
- **Counting characters.** Letters, spaces and punctuation all count. Non-English text is often
  longer than English: leave headroom.

## 7. Game and quiz-show templates

A template with `variableCount` above zero (Game or Quiz category: a quiz show, an escape room, a
dice game, a scoreboard) is a **library in a game look**, like any other template, with extra
**game screens** under genre tags (`game-board`, `clue slide`, `team scoreboard`, `round`,
`game over`...) that carry their own buttons, variables and reactions (`actionCount`).

- For an **ordinary deck** in that look, insert only its ordinary tags (`title`, `list-4`,
  `chart`...): they have no game logic.
- For a **game**, follow `games.md`: insert the game screens the game needs in one call, in play
  order (jumps among slides inserted together are re-pointed, jumps to slides not inserted are
  removed and counted in `warnings`), fill the texts, then wire the missing links and set the
  player up.
- If the user only wants a quiz, take the `question-*` layouts (`create-quiz`).
