# Composed slides: building a slide the template has no layout for

Most slides come from template layouts. Some do not exist in any template: a results slide with a
score, a reveal or tab activity, a media slide, a hotspot question on a picture, a game screen, or
a whole deck in a style the gallery lacks (`styles.md`, section 4). Those slides are **composed**
from the tools. This file says how to compose them without the classic failures, and how to make
them look designed rather than like "text on a background".

Contents: 1 The freeform trap, 2 A safe skeleton, 3 Building blocks, 4 Pattern catalog, 5 Style
signatures, 6 Rules for composed slides

## 1. The freeform trap

A deck made from a template is a **freeform** deck (`getPresentationInfo` says `positioningMode:
"freeform"`): its layouts were measured into fixed positions when they were inserted. In such a
deck **every shape a tool adds lands near the middle of the slide, at the same default spot, on
top of the others**: three `addText` calls give three texts piled on each other, and a button
under another one never receives the click. Do not switch the whole deck to `auto` to escape
this: that would reflow every template layout.

The fix is one positioned flow group per composed slide (section 2). Inside the group content
stacks or forms columns like on a web page and nothing overlaps.

In a fluid deck or a blank deck switched to `auto`, the slide itself is a flow container: the
skeleton below still helps (one group keeps the slide in order) but needs no positioning.

## 2. A safe skeleton (verified)

1. `addSlide {slideType: "blank", index}` gives the slide and focuses it. Set its background to
   match the deck: read a neighbouring template slide with `getSlideCSSStyle {slideId}` and copy
   its `background` with `setSlideCSSStyle`.
2. `addGroupLayout {layout: "column", gap: 24}` returns the group's `shapeId`.
3. In a freeform deck pin the group to the content area: `setShapePositioningMode {slideId,
   shapeId: group, positioningMode: "absolute"}`, then `setShapeCSSStyle {slideId, shapeId:
   group, style: {left: "96px", top: "80px", width: "1088px", height: "560px"}}` on a 1280 x 720
   slide (keep the same margins as the template's slides).
4. **Before every insert** `selectShape {slideId, shapeId: group}`: after an insert the new shape
   is selected, so without it the next insert lands outside the group, back at the default spot.
   Text, button, icon, image and media inserts return `parentShapeId` (it must be the group);
   for lists, charts, questions, infographics and tables check with `getPresentationState {scope:
   "current_slide"}`.
5. Columns: an inner `addGroupLayout {layout: "col-2"}` (or `ratio-1-2`...) inside the group
   returns `childShapeIds`, one per column; select a column before each insert into it.
6. Decor (a blob, a big numeral, a picture behind the content) goes on its own absolute layer:
   add it, `setShapePositioningMode` absolute with left/top/width/height, then `moveShapeToBack`.
   Never make text absolute: absolute text does not grow and later overlaps.
7. `addList` stretches to fill the remaining height: `setShapeCSSStyle {flex-grow: "0", height:
   "auto"}` on it unless its cards should fill the group.
8. Look at the result with `getSlideImage` and run `reviewSlides` on the slide.

## 3. Building blocks

**Structure** (`setContainerLayout`, `addGroupLayout`): presets `row`, `column`, `col-2` to
`col-6`, `ratio-1-2`, `ratio-2-1`, `ratio-2-1-1`, `ratio-1-1-2`, `tiles-2-1`, `tiles-1-2`,
`tiles-2-1-col`, `tiles-1-2-col`. Nesting presets gives most asymmetric grids. `margin` as a
shorthand (`"48px 0px 0px 0px"`, negatives allowed) offsets a block (per-side keys such as
`margin-top` are ignored); `align-self` pins a block to one edge; `flex-grow` lets one block
take the space.

**Creative CSS** (`setShapeCSSStyle`):
- crops: `clip-path` `circle(48%)`, `ellipse(...)`, `polygon(15% 0, 100% 0, 100% 100%, 0 100%)` for
  a diagonal edge, `inset(0 round 400px 400px 0 0)` for an arch;
- rotation and nudges: `transform:rotateZ` (`-6deg`), `transform:translateX`, `transform:translateY`
  (the bare `transform` key is ignored);
- fades: `mask-image` `linear-gradient(to bottom, black 55%, transparent)`;
- duotone and tint: `filter:grayscale` `100%` with `mix-blend-mode` `multiply` over an accent group;
  `filter:blur`, `filter:brightness`, `filter:contrast`;
- shadows: a flat offset `12px 12px 0 rgb(var(--aie-accent-0-rgb))` (sticker, poster) or a soft
  `0 30px 60px rgba(0,0,0,.18)`; `addShape` draws its shadow around its whole box;
- frames and rules: `border-style`, `border-color` and `border-width` together; a multi-value
  `border-width` (`"0 0 0 4px"` a left bar, `"0 0 1px 0"` a hairline) (per-side `border-left` keys
  are ignored); `border-radius` `999px` for pills, `40px 0` for leaf corners;
- glass: `backdrop-filter:blur` `8px` on a translucent group over a picture.

**Marks**: `addShape {shapeType: "dv", subTypeName}` (`rect`, `oval`, `round-rect`, `donut`, `block-arc`, `chevron`,
`hexagon`, `wave`, `cloud`, `star-4` to `star-20`, `sun-flower`, `frame`, `half-frame`, `corner`,
`plaque`...); `addSvg {svg}` for blobs, underlines, scribbles, dot grids and ornaments, painted
with theme colors (`fill='rgb(var(--aie-accent-0-rgb))'`) and without ids (`<linearGradient
id>` and `url(#...)` break when the slide is copied); `addIcon` for pictograms, sized with
`font-size`.

**Native components**: `addInfographic` (16 types: beyond `timeline-horizontal`, `process`,
`comparison` and `funnel`, also `key-points`, `hierarchy`, `matrix`, `flowchart`, `modular-cards`,
`journey`, `relationship`, `anatomy`, `decision`, `educational-explanation`, `cycle`),
`addChart` (9 types), `addTimeline`, `addTable`, `addList`.

**Type as image**: one word or numeral at 200-400 px cropped by the slide edge; a ghost numeral in
a light tint behind the content; a vertical label (`transform:rotateZ` `-90deg`) at the edge.

**Theme colors only**: `rgb(var(--aie-accent-0-rgb))` (primary) to `--aie-accent-7-rgb`, the text
variables, tints as `rgba(var(--aie-accent-0-rgb), 0.14)` or lightness steps
(`rgb(var(--aie-accent-2-rgb-l90))`, steps 90, 80, 70, 60, 40, 30, 20, 10; there is no l50).
Literal colors break when the theme changes.

## 4. Pattern catalog

A composed slide looks designed when its composition has an idea: where the mass sits, what
overlaps what, what crops the picture. Choose a pattern, not just a stack.

| Family | Patterns |
|---|---|
| Splits | half text, half full-bleed picture (`col-2` gap 0); a narrow color panel with a big number beside the content (`ratio-1-2`); a picture with a diagonal edge (absolute, `clip-path` polygon); a picture in an arch or circle; an inset card floating over a full-bleed picture |
| Hero | a full-bleed picture with a dark gradient and the title bottom-left; a centred monument title with a lot of space; an oversized word cropped by the edge behind the real title; a big statistic as the hero (180-240 px numeral, caption, source) |
| Grids and cards | a bento grid (one big tile and small tiles, `ratio-2-1` with a column of two); staggered columns (each column a `margin` step lower); a hairline grid (`border-width` `"0 0 0 1px"`, radius 0); icon circles; giant ghost digits behind item titles; alternating zig-zag rows |
| Editorial | a drop numeral beside two text columns; a pull quote with a giant quotation mark; margin notes beside the main text; an annotated picture with callouts; a caption strip under a panorama |
| Sequence | a native timeline or process; a curved SVG road with dots and labels; a chevron ribbon in stepping tints; staircase steps; a journey, funnel or decision infographic |
| Data | a KPI strip above a chart; a ring statistic (a number inside a doughnut); a chart with an insight column; a pictogram row (7 of 10 icons in the accent); bars built from groups |
| Structure | a 2 x 2 matrix; overlapping circles (Venn, `mix-blend-mode: multiply`); a hierarchy; hub and spokes; a versus split with a round badge on the seam; a pyramid of widening bars |
| Playful | polaroid cards (white padding, rotation, overlap); cut-paper blobs; sticky notes; speech bubbles; tape labels; sticker badges; doodles at the edges |
| Section and close | a big number section; a split-color section with a preview of what follows; a closing card with a button (`open-url`) and a contact line |

Patterns that need many absolute layers (overlaps, rotations, collages) take many calls: use them
for one or two key slides, and keep the rest simple.

## 5. Style signatures

When you compose slides for a deck, borrow the **signature devices** of its style (the style is
in the template name, `styles.md`), so composed slides look like the template's own. For a style
the gallery lacks, these devices are the style.

| Style | Signature devices | Avoid |
|---|---|---|
| Bold Dark Accent | diagonal picture cut, oversized tint word, accent rule | pastel tints, collage |
| Clean Corporate | inset card on a picture, icon circles | rotation, blobs, doodles |
| Swiss Editorial | hairline grid, vertical label, giant digits | shadows, rounded cards, blobs |
| Data Report | ring statistic, KPI strip, pictograms | full-bleed pictures on data slides |
| Startup Gradient | gradient blobs, bento, glass cards (`backdrop-filter:blur`) | serif, hairline grids |
| Warm Classroom | sticky notes, tape labels, a road path | dark backgrounds, hairlines |
| Storybook | cut-paper blobs, speech bubbles, polaroids | tables, KPI strips |
| Academic Scholarly | margin notes, drop numerals, a footnote rule | stickers, rotation |
| Science Lab | annotated picture, dot grid, hub and spokes | pastel, doodles |
| Tech Neon | glow shadows (`0 0 24px` accent), diagonal cuts, scanlines | serif, paper textures |
| Nature Calm | arch and leaf crops (`40px 0` radius), mask fades, organic blobs | hard offsets, neon |
| Medical Clinical | icon circles, pictograms, labelled anatomy | rotation, collage |
| Luxury Elegant | a thin frame inset from the edge, arch crops, spacious centring | stickers, bold offsets, bento |
| Vintage Heritage | frame and ornament SVG, sepia duotone, polaroids | neon, gradients |
| Vietnamese Heritage | lotus and cloud ornaments, arch crops, red and gold frames | neon, cold palettes |
| Brutalist Poster | flat offset shadows, rotated bands, cropped giant type | soft shadows, pastel, blobs |
| Soft Pastel | blobs, pill shapes, overlapping multiply circles | hairline grids, dark backgrounds |
| Iridescent Chrome | a 3D chrome object in a corner, condensed stacked titles | flat color fills |
| Duotone Mono | a ghost echo of the title, duotone portraits | a third color, rounded corners |
| Cinematic Dark Photo | a black text box over a full-bleed photo | light slides, long lists |
| Gradient Orb | blurred orbs (an `oval` with `filter:blur` 40-70px half off the edge), a footer line | busy photos, many accents |
| Infographic Flat | one diagram per slide, icon circles, accent-bar cards | photos |

## 6. Rules for composed slides

- Flow first, then layers: every text the slide shows stays in the flow group.
- One focal point per slide; decor behind the content (`moveShapeToBack`) at a tint of 15% or less.
- One signature device repeated on several slides gives identity; five unrelated tricks look noisy.
- No layout family more than twice in a row, and no two consecutive slides with the same skeleton.
- Keep the deck's background rhythm: base, inverse and, rarely, a full accent slide.
- Inverse (dark) slides need an explicit light color on every text.
- Check every composed slide with `getSlideImage` and `reviewSlides` before moving on: composed
  slides go wrong more often than template layouts.
