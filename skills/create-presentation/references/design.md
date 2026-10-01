# Designing decks that look professional

How to make a uPresenter deck look designed rather than like "text on a themed background",
using only the uPresenter tools.

## Principles

- **A small palette with one accent.** A base color, its inverse, one accent and a few
  neutrals. Use the accent sparingly: keywords, numbers, rules, one highlighted box.
- **Background rhythm.** Alternate the base, the inverse and (rarely) a full accent slide for a
  big moment. Never put three identical backgrounds or layouts in a row.
- **Typographic hierarchy does the work.** Big confident titles, calm body text, generous line
  height. Emphasis comes from size, weight and the accent color, not from boxes and bold
  everywhere.
- **Generous, asymmetric layouts.** Wide margins (56-96 px), content aligned to a clear edge,
  empty space left empty. Split and ratio layouts (text | image, a narrow label column + a wide
  content column) look more designed than centered stacks.
- **Numbers as graphics.** Oversized statistics, big list numbers, KPI cards.
- **Structure from lines and surfaces.** Dividers, cards with a soft fill or hairline border,
  one highlighted box. Avoid cards with a thick colored stripe on one edge; carry the accent in
  the fill, a numeral, an icon or a label instead.
- **One image mood.** All pictures share lighting and style and fill their frames edge to edge.
- **Charts and tables in the palette.** Accent for the series that matters, a neutral for
  comparison, and the takeaway as the title.

## Working with the theme

Colors and fonts come from the theme, never from literal values, so the whole deck follows a
theme change. Use `rgb(var(--aie-accent-N-rgb))` (N = 0 primary to 7), the text variables
(`--aie-title-rgb`, `--aie-body-rgb`) and `var(--aie-title-font-family)`. Soft tints:
`rgba(var(--aie-accent-0-rgb), 0.14)`. Inverse (dark) slides need explicit colors on every
text, because the theme text colors are tuned for the base background.

## Layout skeletons

`A` is `rgb(var(--aie-accent-0-rgb))`. Before every insert into a column call
`selectShape <column id>` and check `parentShapeId` in the result.

**Cover, split text | picture**: `setSlideCSSStyle {padding: "0px"}`, `setContainerLayout
{layout: "col-2", gap: "0px", alignItems: "stretch"}`; a column group with an eyebrow line, the
title (72-90 px), `addDivider {thickness: 3, color: A}` and a subtitle; the second column holds
`addImage` (`width: 100%`, `height: 100%`, `object-fit: cover`) or a placeholder.

**Agenda, numbered grid**: slide `padding: 72px`, layout `column`, `justifyContent: center`; a
title, a one-line description and an accent divider; `addGroupLayout {layout: "col-2",
gap: 32}`; put the items into the two child columns (select each column before inserting).

**Big statistic (rhythm breaker)**: `setSlideCSSStyle {background: A, padding: "64px"}`, layout
`column`, centered; the number as a title (110-130 px, centered) and a caption (24 px) in a
contrasting color.

**Label column + content (inverse slide)**: inverse background, `padding: 72px`, layout
`ratio-1-2`, gap 64. Left: a short title, a rule, a description (about 12 characters per line
or 44-48 px). Right: `addList` with markers `"01"`, `"02"`, restyled with `setTextStyle`.

**KPI cards + chart**: slide padding `56px 72px`, layout `column`; a title; `addGroupLayout
{layout: "col-3", gap: 20}` where each column has a soft fill, `border-radius`, `padding` and a
big number plus a small label; then `addChart`, `setChartText` for the title and
`setShapeCSSStyle {width: "100%", height: "300px"}`.

**Three columns on the accent**: `background: A`, white title and rule, `addGroupLayout
{layout: "col-3", gap: 36}` with a bold claim and a detail line per column.

**Quote / key message**: one sentence at 44-56 px, one word in the accent color, an attribution
in 18 px, generous padding (96 px).

**Closing**: mirror the cover (picture left), a closing line, an accent rule, a contact line.

Notes that apply everywhere:

- `addList` stretches to fill the remaining height: set `setShapeCSSStyle {flex-grow: "0",
  height: "auto"}` unless the cards should fill the slide.
- Give content slides `justifyContent: "center"` so short content does not cling to the top.
- Empty groups keep a minimum size of about 64 px: use an icon for bullets and dots instead of
  a tiny group.
- Wrap a slide's content in one `addGroupLayout {layout: "column"}` so a title that grows to
  two lines pushes the rest down instead of overlapping it.
- Set line height with `setTextStyle {style: {"line-height": "1.25"}}`; very large numerals
  need at least 1.2 or they clip.

## Check before reporting

For each slide (`getPresentationState` with `scope: "current_slide"`): does it share the deck's
palette, fonts and margins? Is there one focal point? Is text readable on inverse and accent
slides? Is anything empty, clipped or stuck to the top? Charts: are series distinguishable and
is the title set? Fix what you find and report remaining compromises honestly.
