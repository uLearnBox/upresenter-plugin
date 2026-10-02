# Designing a deck from scratch

The way a template designer works, for one deck: decide a design language, turn it into a theme,
plan a small layout suite for the content, then compose every slide in that language and check
it by eye. Use it when the user picks it from the template shortlist (`SKILL.md`, step 2, where it is
always the last option), asks for it (a blank deck, "design it yourself", their own brand colors
and fonts, no template), or wants a style that has no template and not the nearest one
(`styles.md`, section 4). Building from a template is faster: say so once, and follow the user's
choice.

Designing from scratch takes many more calls than filling template layouts (often 10-20 per
slide). Keep the deck focused (6-12 slides) and tell the user it takes a while.

Contents: 1 Agree the design, 2 Set up the deck, 3 Plan the layout suite, 4 Build each slide,
5 Check the design, 6 Report

## 1. Agree the design

1. The brief as in `SKILL.md`, step 1 (topic, audience, language, purpose, length).
2. The design language: named by the user, matched to a reference they show, or chosen from the
   topic (`styles.md`, section 2), then its spec in `design-systems.md`. For a brand, start from
   the closest spec and replace its colors and fonts with the brand's (keep the roles: primary,
   seven accents, title, body, backgrounds).
3. In one short message tell the user the style, the palette (the hex colors with their roles),
   the title and body fonts, the background rhythm and two or three signature devices, and ask for
   a go. **Wait for the answer** unless they asked you to decide.

## 2. Set up the deck

1. A deck: if the user has an empty deck open (`listOpenEditorContexts`, then
   `getPresentationState`: one slide with only placeholders), use it. Otherwise
   `getNewPresentationLink {blank: true, layoutType: "slide" | "fluid"}` gives a link that creates
   an empty presentation; ask them to open it and keep the tab open.
2. The design system:
   - `createTheme {...}` with the spec from `design-systems.md` (changed for the brand). It
     saves the theme in the user's theme library and gives it to the deck; the result has the
     `themeId`. Colors are `#rrggbb`, `rgb()` or `rgba()`; backgrounds may be
     `linear-gradient(135deg, #111111 0%, #2A2A2A 100%)`. Name it after the deck or brand, in plain
     ASCII.
   - Or an existing theme: `listThemes {query}` (the app's built-in themes, with the topic and
     style each was made for, and the user's own) and `applyTheme {themeId}`.
3. `setPresentationPositioningMode {positioningMode: "auto"}` for a slide deck: with no template
   layouts in it, flow layout keeps every composed slide in order (a fluid deck is already flow).
4. `setPresentationName`, `setPresentationLanguage`, and `setSlideSize` only if the user wants
   another size.
5. The blank slide carries a title (shape 1) and a body placeholder (shape 2): use them for the
   cover or delete them.

## 3. Plan the layout suite

Before building, write the plan as a short table, one row per slide:

| Column | What to decide |
|---|---|
| Message | what the viewer should take away (a claim, not a topic) |
| Content | the real text, numbers and items |
| Pattern | a composition from `composed-slides.md`, section 4 (family and pattern) |
| Background | base, inverse or accent, following the spec's rhythm |
| Device | the signature device used on this slide, if any |

Rules, the same a template follows:

- **Variety.** At least four pattern families in a deck of ten; no family twice in a row; never
  the same skeleton (root layout and first groups) on two neighbouring slides; the even card grid
  at most twice.
- **Identity.** The style's signature device on the cover and on at least three other slides;
  nothing from the style's Avoid list.
- **Rhythm.** Backgrounds alternate as the spec says, with one full accent slide for the big
  moment (a statistic, a key quote, the closing).
- **Components.** Use the native pieces where the content has that shape: `addInfographic` for a
  process, cycle, matrix or hierarchy, `addTimeline` for dates, `addChart` for numbers, `addTable`
  for facts, `addQuestion` for checks (the `create-quiz` skill).
- **Cover and closing** mirror each other (the same pattern, reversed or on the other
  background).

## 4. Build each slide

In the planned order, one slide at a time:

1. `addSlide {slideType: "blank", index}`, then the slide frame: `setSlideCSSStyle {slideId,
   style: {padding: "72px 96px", background: ...}}` (inverse and accent backgrounds as theme
   variables), and `setContainerLayout {slideId, layout, gap, alignItems, justifyContent}` for the
   root: `column` with `justifyContent: "center"` for most slides, `col-2` or `ratio-1-2` for
   splits.
2. Groups for the blocks: `addGroupLayout {layout, gap, padding}`; `selectShape {slideId, shapeId}`
   on the group or column **before each insert** into it (the new shape is selected after every
   insert), and check `parentShapeId` in the result.
3. Text: `addText {text, textType: "title" | "subtitle" | "body"}` takes the theme's fonts, sizes
   and colors. Change sizes only for the spec's hierarchy (a display title, a giant numeral, a
   small uppercase eyebrow with letter spacing) with `setTextStyle {slideId, shapeId, style}`. On
   inverse and accent slides give every text an explicit color (`rgb(var(--aie-accent-7-rgb))` or
   a light theme color). Math as `[[katex:...]]`.
4. Decor and signature devices (`composed-slides.md`, sections 3 and 5): shapes, SVG, icons and
   big numerals on absolute layers (`setShapePositioningMode` absolute, then
   `setShapeCSSStyle {left, top, width, height}`), sent behind with `moveShapeToBack`, at a tint of
   15% or less behind text. Colors only as theme variables, so a later theme change carries the
   whole deck.
5. Pictures: the user's (`addImage {source, objectFit: "cover"}`), an `addImagePlaceholder` the
   user fills later, or `generateShapeImage` when the connection offers it (cost agreement and
   prompting in `template-layouts.md`, section 4; the spec names the model).
6. Interactions and questions: `interactions.md`, `games.md`, `create-quiz`.
7. Check the slide right away: `getSlideImage {presentationId, slideId}` and
   `reviewSlides {slideIds: [slideId]}`. Fix spills, overlaps, weak contrast and empty areas
   before the next slide: mistakes repeat when they are copied forward.

Recipes for common slides in auto layout (cover split, agenda grid, big statistic, label column,
KPI cards with a chart, three columns, quote, closing): `design.md`, "Layout skeletons".

## 5. Check the design

Look at every slide with `getSlideImage` and judge the deck as a set:

- one focal point per slide; generous, equal margins; aligned edges;
- the hierarchy reads at a glance: title, then the one thing to notice, then detail; body text at
  18 px or more;
- the rhythm of backgrounds and the signature device are there; the slides look like one deck;
- no two neighbours share a skeleton; no slide is a wall of text;
- contrast is strong everywhere, also on pictures and accent slides;
- nothing spills, overlaps or hangs off the slide (`reviewSlides {}`; call again with
  `remaining`).

Fix and look again (at most three rounds), then the content checks of `SKILL.md`, step 9.

## 6. Report

Tell the user the style and the theme (its name in their theme library: they can edit its colors
and fonts and give it to other presentations in the Design tab), the slides with their
patterns, what you checked and fixed, and what the tools cannot do (animations and transitions,
two-tone titles done as two text shapes, narration). Suggest previewing in the player and, if
they like the look, duplicating the deck as the starting point for the next one.
