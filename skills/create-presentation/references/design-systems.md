# Design systems: a look built from scratch

The specs to create a style as a theme with `createTheme`, for a deck designed from scratch
(`design-from-scratch.md`). Each one is a complete design language: the theme spec (paste it into
`createTheme` as it is, or change the colors and fonts to the user's brand), the background
rhythm, the signature devices that make it recognisable, the picture style and what to avoid.
Composition patterns and how to build the devices: `composed-slides.md`.

Notes that apply to all of them:

- The fonts cover Vietnamese; after creating the theme, look at one accented line in the slide
  picture ("Tiếng Việt có dấu: ắ ặ ề ổ ữ").
- `accents[0]` (accent1) is the second chart series, so it stays clearly different from
  `primary`; `accents[6]` (accent7) is the ink for text on inverse slides.
- The `imageStyle` is added to every AI picture prompt of the deck; the model named under
  Pictures is the one to pass to `generateShapeImage` when that tool is offered.
- Two-tone titles (one word in the accent) and hollow letters need per-word styling that the
  tools do not have: use two text shapes side by side in a `row` group, or leave the title in
  one color.
- Rhythm percentages are shares of slides: base background, inverse (dark or light opposite)
  and full accent slides. Set an inverse or accent slide with `setSlideCSSStyle {background}`
  and give every text on it an explicit color.
- Theme names keep ASCII letters only: name a theme after the deck or brand ("Wedding Plan
  2027"), not with accents or dashes.

Contents (by style): Bold Dark Accent, Clean Corporate, Swiss Editorial, Data Report, Startup Gradient, Warm Classroom, Storybook, Academic Scholarly, Science Lab, Tech Neon, Nature Calm, Medical Clinical, Luxury Elegant, Vintage Heritage, Vietnamese Heritage, Brutalist Poster, Soft Pastel, Acid Brutalist, Cyber HUD, Arcade Pixel, Y2K Digital Collage, Building Blocks, Retro Flat Lesson, Dark UI Product, Organic Brand Kit, Gradient Orb, Slate Editorial, Vintage Scrapbook, Iridescent Chrome, Duotone Mono, Cut-Paper Organic, Cinematic Dark Photo, Kawaii Pastel, Broadsheet, Ink Wash, Infographic Flat, One-Hue System, Dev Console, Ember Glow, Dark Pastel Cards

### 1. Bold Dark Accent
Mood: confident, energetic, keynote-like. Best for: business reviews, launches, sport.

```json
{"name": "Bold Dark Accent", "primary": "#E8622C", "accents": ["#BDBDBD", "#F4A261", "#FFFFFF", "#8A8A8A", "#2C2C2C", "#FBE3D6", "#1E1E1E"], "title": "#FFFFFF", "subtitle": "#E8622C", "body": "#D6D6D6", "slideBackground": "#1E1E1E", "pageBackground": "#0E0E0E", "titleFont": "Public Sans", "bodyFont": "Public Sans", "titleSize": 56, "titleWeight": 600, "bodySize": 20, "radius": 0, "shadow": "0 24px 60px rgba(0, 0, 0, 0.45)", "imageStyle": "moody high-contrast editorial photography, dark surroundings, warm accent rim light, cinematic, shallow depth of field"}
```

- **Rhythm**: 60 % dark · 25 % white (ink `#1E1E1E`) · 15 % full accent.
- **Signature**: uppercase letter-spaced eyebrow in the accent; two-tone titles (last word in accent); short accent rule under titles; split cover with full-bleed photo; giant statistic on the accent; KPI cards on `#2C2C2C`; "you are here" filled box.
- **Pictures**: `fu` photos, one or two people or objects, dark with warm rim light.
- **Avoid**: more than one accent, centred body text, light photos.

### 2. Clean Corporate
Mood: calm, trustworthy, orderly. Best for: training, HR, policy, onboarding.

```json
{"name": "Clean Corporate", "primary": "#1F5EFF", "accents": ["#9AA8BD", "#00B3A4", "#0B1F3A", "#5B6B82", "#EEF2F8", "#DCE6FF", "#0B1F3A"], "title": "#0B1F3A", "subtitle": "#1F5EFF", "body": "#3B4A5E", "slideBackground": "#FFFFFF", "pageBackground": "#EEF2F8", "titleFont": "Montserrat", "bodyFont": "Source Sans 3", "titleSize": 48, "titleWeight": 700, "bodySize": 20, "radius": 12, "shadow": "0 12px 32px rgba(11, 31, 58, 0.12)", "imageStyle": "bright modern office photography, natural daylight, diverse professionals, clean blue-grey palette, candid"}
```

- **Rhythm**: 75 % white · 15 % navy `#0B1F3A` (white text) for section and statement slides · 10 % pale blue `#EEF2F8` cards backgrounds.
- **Signature**: left-aligned titles with a small blue label above; rounded white cards with soft shadow on the pale page; icon lists (`addList` with `icon:` markers) in blue circles; process infographics and tables with a navy header row.
- **Pictures**: `fu` photos of people at work, bright and candid; icons for concepts.
- **Avoid**: dark full-bleed photos, decorative fonts, more than two colours per slide.

### 3. Swiss Editorial
Mood: intellectual, precise, typographic. Best for: strategy, research, reports.

```json
{"name": "Swiss Editorial", "primary": "#E30613", "accents": ["#9A9A9A", "#111111", "#FFFFFF", "#6B6B6B", "#F2F2F2", "#FFE5E6", "#111111"], "title": "#111111", "subtitle": "#E30613", "body": "#2B2B2B", "slideBackground": "#FFFFFF", "pageBackground": "#F2F2F2", "titleFont": "Inter", "bodyFont": "Inter", "titleSize": 64, "titleWeight": 800, "bodySize": 19, "radius": 0, "shadow": "none", "imageStyle": "minimal black-and-white architectural photography, strong geometry, high contrast, lots of negative space"}
```

- **Rhythm**: 85 % white · 10 % black (`#111111`, white text) statements · 5 % red accent.
- **Signature**: huge tight headlines (64–96 px, weight 800) aligned to a strict left edge; thin black dividers (1 px) separating rows; numbered sections in red ("01 —"); asymmetric `ratio-1-2` layouts with a narrow label column; tables and charts in black + one red series.
- **Pictures**: few; black-and-white `fu` photos cropped hard, or none — typography carries it.
- **Avoid**: rounded corners, shadows, gradients, icons as decoration, centred layouts.

### 4. Data Report
Mood: analytical, dense but clear. Best for: finance, quarterly results, KPIs.

```json
{"name": "Data Report", "primary": "#0E7C66", "accents": ["#A3B1C6", "#D64545", "#12263F", "#5E6E85", "#F4F7FA", "#E3F4EF", "#12263F"], "title": "#12263F", "subtitle": "#0E7C66", "body": "#33445C", "slideBackground": "#FFFFFF", "pageBackground": "#E9EEF4", "titleFont": "Roboto", "bodyFont": "Roboto", "titleSize": 44, "titleWeight": 700, "bodySize": 18, "radius": 8, "shadow": "0 6px 18px rgba(18, 38, 63, 0.10)", "imageStyle": "clean corporate still life, city skyline at dusk, subtle blue-green tones, uncluttered"}
```

- **Rhythm**: 85 % white · 15 % deep navy `#12263F` for section/summary.
- **Signature**: KPI strips of 3–4 cards with big numbers, green for positive and red (`accents[1]`) for negative; one chart per slide with a one-line takeaway as the title ("Revenue grew 18 % on subscriptions"); comparison tables with side headers; source lines in small grey text at the bottom.
- **Pictures**: rare; charts, tables and infographics are the visuals.
- **Avoid**: decorative photos, 3D effects, more than 6 data points per chart label set.

### 5. Startup Gradient
Mood: optimistic, modern, ambitious. Best for: startup and product decks, SaaS demos.

```json
{"name": "Startup Gradient", "primary": "#7B5CFF", "accents": ["#C9C3E6", "#FF5CA8", "#00D1C1", "#8E88B0", "#1B1535", "#EDE8FF", "#14102A"], "title": "#FFFFFF", "subtitle": "#FF5CA8", "body": "#D9D5F0", "slideBackground": "linear-gradient(135deg, #1B1535 0%, #2A1E5C 100%)", "pageBackground": "radial-gradient(#3A2A7A 0%, #0F0B22 100%)", "titleFont": "Manrope", "bodyFont": "Manrope", "titleSize": 56, "titleWeight": 800, "bodySize": 20, "radius": 20, "shadow": "0 30px 80px rgba(123, 92, 255, 0.35)", "imageStyle": "sleek product and people photography, violet and pink neon rim light, soft glow, futuristic but friendly"}
```

- **Rhythm**: 70 % gradient dark · 20 % light `#EDE8FF` (ink `#14102A`) · 10 % statement on a vivid gradient (`setSlideCSSStyle {background: "linear-gradient(135deg, #7B5CFF 0%, #FF5CA8 100%)"}`).
- **Signature**: glass cards (`background: rgba(255, 255, 255, 0.08)`, `backdrop-filter:blur`, radius 20); gradient-coloured key numbers via rich text in pink; pill-shaped labels; product screenshots in rounded frames; roadmap timeline.
- **Pictures**: `fu` product/people shots with violet light, or device mockups.
- **Avoid**: flat grey slides, serif fonts, busy backgrounds behind charts.

### 6. Warm Classroom
Mood: friendly, encouraging, energetic. Best for: school lessons (6–15 y).

```json
{"name": "Warm Classroom", "primary": "#FF7A59", "accents": ["#4CC9F0", "#FFD166", "#06D6A0", "#8D99AE", "#FFFFFF", "#FFE8DF", "#2B2D42"], "title": "#2B2D42", "subtitle": "#FF7A59", "body": "#3D405B", "slideBackground": "#FFF8EE", "pageBackground": "#FFE8DF", "titleFont": "Nunito", "bodyFont": "Nunito", "titleSize": 52, "titleWeight": 800, "bodySize": 22, "radius": 24, "shadow": "0 14px 30px rgba(43, 45, 66, 0.12)", "imageStyle": "colourful flat vector illustration, friendly rounded characters, soft paper texture, bright cheerful classroom palette"}
```

- **Rhythm**: warm cream base; every third slide a bold colour block (coral, sky or yellow).
- **Signature**: rounded cards in different accent colours for list items; big friendly icons; numbered steps in circles; "Em có biết?" callout buttons with pop-up dialogs; questions with confetti; mascots via illustration.
- **Pictures**: `rc3` illustrations (consistent characters), not photos.
- **Avoid**: dense text, dark slides, thin fonts, stock-photo realism.

### 7. Storybook
Mood: gentle, whimsical, imaginative. Best for: early childhood, stories, reading.

```json
{"name": "Storybook", "primary": "#E07A5F", "accents": ["#81B29A", "#F2CC8F", "#3D405B", "#A8A29E", "#FFFDF7", "#FBEAE5", "#3D405B"], "title": "#3D405B", "subtitle": "#E07A5F", "body": "#4A4E69", "slideBackground": "#FFFDF7", "pageBackground": "#F4EDE1", "titleFont": "Baloo 2", "bodyFont": "Quicksand", "titleSize": 56, "titleWeight": 700, "bodySize": 24, "radius": 28, "shadow": "0 10px 24px rgba(61, 64, 91, 0.10)", "imageStyle": "soft watercolour children’s book illustration, gentle pastel colours, hand-drawn outlines, cosy and warm"}
```

- **Rhythm**: paper-white base; full-page illustrations for story beats; soft coloured cards.
- **Signature**: one large illustration per slide with 1–2 short lines in big type; reveal activities (tap the picture → show the word); drag-words and matching with images; narration if the user has recordings (`interactions.md`, section 9).
- **Pictures**: `rc3` watercolour illustrations with the same characters described each time.
- **Avoid**: more than 12 words per slide, sharp corners, photos.

### 8. Academic Scholarly
Mood: rigorous, classic, credible. Best for: university lectures, thesis defence.

```json
{"name": "Academic Scholarly", "primary": "#7A1F2B", "accents": ["#9C8F7A", "#1F3A5F", "#C9A227", "#6B6258", "#F7F3EA", "#EFE3DA", "#2A2522"], "title": "#2A2522", "subtitle": "#7A1F2B", "body": "#3E3833", "slideBackground": "#FBF8F2", "pageBackground": "#EDE6D8", "titleFont": "Playfair Display", "bodyFont": "Source Sans 3", "titleSize": 48, "titleWeight": 700, "bodySize": 20, "radius": 4, "shadow": "0 8px 22px rgba(42, 37, 34, 0.10)", "imageStyle": "archival documentary photography, warm muted tones, soft natural light, fine film grain"}
```

- **Rhythm**: ivory base · burgundy section dividers with gold rule · occasional navy statement slide.
- **Signature**: serif titles, sans body; numbered sections ("§2"); definition boxes with a burgundy left bar (narrow coloured group + text in a `row`); citations in small grey text; quotes in italic serif; tables with thin rules; timelines for historical context.
- **Pictures**: `fu` archival-looking photos or diagrams; charts with muted palette.
- **Avoid**: neon, heavy shadows, playful animations, emoji-like icons.

### 9. Science Lab
Mood: technical, exploratory. Best for: STEM lessons, engineering, tech talks.

```json
{"name": "Science Lab", "primary": "#22D3EE", "accents": ["#94A3B8", "#A3E635", "#F472B6", "#64748B", "#0F1B2D", "#CFFAFE", "#0B1220"], "title": "#F1F5F9", "subtitle": "#22D3EE", "body": "#CBD5E1", "slideBackground": "#0B1220", "pageBackground": "#050A14", "titleFont": "Space Grotesk", "bodyFont": "Inter", "titleSize": 52, "titleWeight": 700, "bodySize": 19, "radius": 10, "shadow": "0 20px 50px rgba(34, 211, 238, 0.12)", "imageStyle": "scientific macro photography style, dark background, cyan and lime rim light, crisp detail, shallow depth of field"}
```

- **Rhythm**: dark navy base · white "lab notebook" slides for worked examples · cyan highlight slides for key formulas.
- **Signature**: monospace-looking labels (JetBrains Mono via rich text `font`) for variables and units; diagrams with thin cyan lines and labelled shapes (flowchart shapes); process and cycle infographics; hotspot/label questions on diagrams; slider questions for estimates.
- **Pictures**: `fu` macro/lab photos or `rc3` technical diagrams.
- **Avoid**: cartoon illustrations, warm beige backgrounds.

### 10. Tech Neon
Mood: electric, immersive. Best for: gaming, esports, cyber security, youth events.

```json
{"name": "Tech Neon", "primary": "#00EAFF", "accents": ["#FF3A56", "#A855F7", "#FFD666", "#7C7A99", "#16122A", "#CCFBFF", "#0C0A16"], "title": "#FFFFFF", "subtitle": "#00EAFF", "body": "#E0E4F5", "slideBackground": "#0C0A16", "pageBackground": "radial-gradient(#1D1640 0%, #05040B 100%)", "titleFont": "Oswald", "bodyFont": "Be Vietnam Pro", "titleSize": 60, "titleWeight": 700, "bodySize": 20, "radius": 14, "shadow": "0 0 40px rgba(0, 234, 255, 0.35)", "imageStyle": "cinematic neon cyberpunk scenes, cyan and magenta glow, light fog, high contrast, sharp reflections"}
```

- **Rhythm**: dark throughout; accent glow cards; one or two full-bleed neon photos.
- **Signature**: uppercase condensed titles; glowing borders (`border-color` accent + `box-shadow` glow); HUD-style numbered labels; progress bar bound to a score variable; timed challenges with confetti.
- **Pictures**: `fu` neon scenes; no people close-ups needed.
- **Avoid**: pastel colours, serif fonts, white slides.

### 11. Nature Calm
Mood: calm, organic, reassuring. Best for: wellbeing, sustainability, food, environment.

```json
{"name": "Nature Calm", "primary": "#3E8E5E", "accents": ["#C9B79C", "#E9A23B", "#1F3D2B", "#8C9A86", "#FFFFFF", "#E3F0E6", "#1F3D2B"], "title": "#1F3D2B", "subtitle": "#3E8E5E", "body": "#3B4A3F", "slideBackground": "#F3F1EA", "pageBackground": "#E6E1D3", "titleFont": "Lora", "bodyFont": "Nunito", "titleSize": 50, "titleWeight": 600, "bodySize": 21, "radius": 18, "shadow": "0 12px 28px rgba(31, 61, 43, 0.10)", "imageStyle": "soft daylight nature photography, earthy greens and warm sand tones, gentle, airy, organic textures"}
```

- **Rhythm**: sand base · deep green `#1F3D2B` sections (cream text) · photo slides of nature.
- **Signature**: rounded organic cards; leaf/sun icons from Material Symbols (`eco`, `spa`, `water_drop`); cycle infographic for life cycles; before/after comparisons; calm reflection questions in survey mode.
- **Pictures**: `fu` nature and people-outdoors photos, soft light.
- **Avoid**: neon, black backgrounds, aggressive red.

### 12. Medical Clinical
Mood: clean, precise, caring. Best for: medical training, patient education, safety.

```json
{"name": "Medical Clinical", "primary": "#0FA3B1", "accents": ["#9FB3C8", "#F25F5C", "#1D3557", "#6C7A89", "#F5FAFB", "#DDF4F6", "#1D3557"], "title": "#1D3557", "subtitle": "#0FA3B1", "body": "#34495E", "slideBackground": "#FFFFFF", "pageBackground": "#E8F3F5", "titleFont": "Lexend", "bodyFont": "Lexend", "titleSize": 46, "titleWeight": 600, "bodySize": 20, "radius": 14, "shadow": "0 10px 26px rgba(29, 53, 87, 0.10)", "imageStyle": "clean clinical photography, bright white and teal environment, caring professionals, soft even light"}
```

- **Rhythm**: white base · teal-tint `#DDF4F6` panels · red (`accents[1]`) only for warnings.
- **Signature**: step-by-step procedures (process infographic, numbered list); do/don't comparison cards (green check vs red cross icons); anatomy label questions; hotspot questions on images; clear warning callouts.
- **Pictures**: `fu` photos of care settings; labelled diagrams for anatomy.
- **Avoid**: dark themes, playful motion, red outside warnings.

### 13. Luxury Elegant
Mood: refined, premium, spacious. Best for: fashion, hospitality, real estate, weddings.

```json
{"name": "Luxury Elegant", "primary": "#C8A45D", "accents": ["#8C8577", "#E9DFC9", "#FFFFFF", "#6E685C", "#1A1814", "#F3ECDD", "#14120F"], "title": "#F3ECDD", "subtitle": "#C8A45D", "body": "#D8D0BF", "slideBackground": "#14120F", "pageBackground": "#0A0907", "titleFont": "Playfair Display", "bodyFont": "Montserrat", "titleSize": 60, "titleWeight": 500, "bodySize": 18, "radius": 0, "shadow": "0 30px 70px rgba(0, 0, 0, 0.55)", "imageStyle": "luxury editorial photography, soft golden hour light, rich textures, marble and velvet, elegant and minimal"}
```

- **Rhythm**: near-black base · ivory `#F3ECDD` slides (ink `#14120F`) · full-bleed photos.
- **Signature**: thin gold rules and frames (1 px borders in gold); wide letter-spaced uppercase labels; big italic serif quotes; lots of empty space; single hero image per slide.
- **Pictures**: `fu` editorial photos with golden light.
- **Avoid**: bright colours, icons, dense bullets, bouncy motion.

### 14. Vintage Heritage
Mood: warm, nostalgic, storytelling. Best for: history, literature, arts, culture.

```json
{"name": "Vintage Heritage", "primary": "#B5542C", "accents": ["#8F8A6B", "#D9A441", "#3E4A3D", "#7A6F5E", "#EFE4CF", "#F2D9C4", "#2E2A24"], "title": "#2E2A24", "subtitle": "#B5542C", "body": "#4A4337", "slideBackground": "#F4EAD5", "pageBackground": "#E3D3B3", "titleFont": "Merriweather", "bodyFont": "Lora", "titleSize": 48, "titleWeight": 700, "bodySize": 20, "radius": 2, "shadow": "0 10px 24px rgba(46, 42, 36, 0.18)", "imageStyle": "vintage film photography, warm sepia and faded colours, grain, archival documentary feel"}
```

- **Rhythm**: parchment base · olive `#3E4A3D` sections (cream text) · archival photo slides.
- **Signature**: framed photos (border + shadow like prints); timelines of eras; quotes from historical figures; "then vs now" comparisons; sequence questions for chronology.
- **Pictures**: `fu` with vintage/sepia treatment, or `rc3` engraving-style illustrations.
- **Avoid**: neon, glassmorphism, modern tech iconography.

### 15. Vietnamese Heritage
Mood: festive, dignified, rooted. Best for: Vietnamese culture, Tết, tourism, local history.

```json
{"name": "Vietnamese Heritage", "primary": "#B3202A", "accents": ["#C9A227", "#1F4E5F", "#F4E3C1", "#8A6D4B", "#FFF8EC", "#F7DADA", "#3A1A12"], "title": "#3A1A12", "subtitle": "#B3202A", "body": "#4B2E24", "slideBackground": "#FFF8EC", "pageBackground": "#F1E2C6", "titleFont": "Playfair Display", "bodyFont": "Be Vietnam Pro", "titleSize": 52, "titleWeight": 700, "bodySize": 20, "radius": 6, "shadow": "0 12px 30px rgba(58, 26, 18, 0.14)", "imageStyle": "Vietnamese cultural photography, lanterns, ao dai, temples and rice fields, warm red and gold tones, soft natural light"}
```

- **Rhythm**: warm ivory base · deep red `#B3202A` section slides (gold and ivory text) · photo slides of places and festivals.
- **Signature**: gold thin frames and dividers; red section covers with large serif titles; subtle traditional pattern backgrounds generated as images (`imageStyle` + "seamless subtle pattern, low contrast") behind section titles; timelines of dynasties/festivals; gallery layouts for places; matching questions (festival ↔ month).
- **Pictures**: `fu` photos of Vietnamese places and people in respectful, documentary style.
- **Avoid**: clichés that stereotype, garish saturation, mixing Chinese/Japanese motifs.

### 16. Brutalist Poster
Mood: loud, raw, attention-grabbing. Best for: marketing campaigns, creative agencies, youth workshops.

```json
{"name": "Brutalist Poster", "primary": "#FFE500", "accents": ["#A0A0A0", "#FF3D00", "#0047FF", "#5C5C5C", "#FFFFFF", "#FFF7B3", "#000000"], "title": "#000000", "subtitle": "#000000", "body": "#111111", "slideBackground": "#FFFFFF", "pageBackground": "#000000", "titleFont": "Anton", "bodyFont": "Archivo", "titleSize": 88, "titleWeight": 400, "bodySize": 20, "radius": 0, "shadow": "none", "imageStyle": "high-contrast grainy street photography, harsh flash, bold colour blocking, raw urban textures"}
```

- **Rhythm**: white and full yellow `#FFE500` slides alternate; black statement slides (white text); no gradients.
- **Signature**: gigantic uppercase words filling the slide; thick black borders (4–6 px) on boxes; hard offset shadows (`box-shadow: 8px 8px 0 #000000`); stickers/labels rotated a few degrees (`transform:rotate`); numbers as huge graphic elements.
- **Pictures**: `fu` grainy flash photos, cut into hard rectangles.
- **Avoid**: soft shadows, pastels, thin fonts, centred calm layouts.

### 17. Soft Pastel
Mood: friendly, light, contemporary. Best for: lifestyle, social media, community workshops, design thinking.

```json
{"name": "Soft Pastel", "primary": "#8B7CF6", "accents": ["#C4C1D6", "#F9A8D4", "#6EE7B7", "#9C98B3", "#FFFFFF", "#EDE9FE", "#2E2A47"], "title": "#2E2A47", "subtitle": "#8B7CF6", "body": "#4B4666", "slideBackground": "#FBF9FF", "pageBackground": "linear-gradient(135deg, #FDE2F3 0%, #E0E7FF 100%)", "titleFont": "Be Vietnam Pro", "bodyFont": "Be Vietnam Pro", "titleSize": 50, "titleWeight": 700, "bodySize": 20, "radius": 22, "shadow": "0 16px 40px rgba(139, 124, 246, 0.18)", "imageStyle": "airy lifestyle photography, pastel lavender and peach tones, soft diffused light, minimal props"}
```

- **Rhythm**: near-white base on a pastel gradient page · pastel colour-block cards · one lavender statement slide per section.
- **Signature**: floating rounded cards with coloured headers; pill tags; illustrated icons; sticky-note style brainstorm grids (`tiles` layouts); polls in survey mode; reveal cards.
- **Pictures**: `fu` airy lifestyle photos or `rc3` soft illustrations.
- **Avoid**: black backgrounds, harsh contrast, dense tables.

### 18. Acid Brutalist
Best for: creative briefs, youth events, music, streetwear, design-school portfolios.

```json
{"name": "Acid Brutalist", "primary": "#D7FF3A", "accents": ["#B9A4FF", "#FFFFFF", "#D7FF3A", "#6B6B6B", "#161616", "#EDE6FF", "#0B0B0B"], "title": "#D7FF3A", "subtitle": "#B9A4FF", "body": "#E6E6E6", "slideBackground": "#0B0B0B", "pageBackground": "#000000", "titleFont": "Big Shoulders Display", "bodyFont": "JetBrains Mono", "titleSize": 84, "titleWeight": 800, "bodySize": 16, "radius": 0, "shadow": "none", "imageStyle": "grainy high-contrast photo with blurred acid-green and violet light leaks, motion blur, film grain, dark background"}
```

- **Signature**: a 1 px neon **cell grid**: every block sits in a bordered cell, and the cells tile the slide like a spec sheet; giant uppercase titles over two lines, the second line **hollow** or as a tint echo; barcode, glyph and star SVG stickers in cell corners; outline pill buttons (`border-radius: 999px`, 1 px lime border); grainy blurred photos (`filter:blur` 2px + `filter:contrast` 140%).
- **Avoid**: soft shadows, rounded cards, more than two accents.

### 19. Cyber HUD
Best for: quizzes, trivia nights, ice-breakers, cyber-security awareness, esports.

```json
{"name": "Cyber HUD", "primary": "#39FF6A", "accents": ["#00E0FF", "#FFFFFF", "#39FF6A", "#2E6B3F", "#07140C", "#C9FFD8", "#030805"], "title": "#39FF6A", "subtitle": "#00E0FF", "body": "#C9FFD8", "slideBackground": "#030805", "pageBackground": "#000000", "titleFont": "Chakra Petch", "bodyFont": "JetBrains Mono", "titleSize": 64, "titleWeight": 700, "bodySize": 18, "radius": 0, "shadow": "0 0 24px rgba(57, 255, 106, 0.45)", "imageStyle": "dark sci-fi corridor with neon green light, wireframe grid floor, digital haze, no text"}
```

- **Signature**: chamfered HUD frames around every question and answer; a wireframe perspective grid floor as an SVG at the bottom of the cover and round slides; round-divider slides ("ROUND 1") using the tunnel image; answer tiles in `col-2`, with the correct answer revealed by a click interaction.
- **Avoid**: serif fonts, photos of people, pastel colours.

### 20. Arcade Pixel
Best for: primary-school quizzes, game nights, gamified reviews, coding clubs.

```json
{"name": "Arcade Pixel", "primary": "#FFC928", "accents": ["#3AA0FF", "#2FB344", "#FFFFFF", "#8A5A2B", "#101010", "#FFF3C4", "#101010"], "title": "#FFFFFF", "subtitle": "#FFC928", "body": "#101010", "slideBackground": "linear-gradient(180deg, #2E8BFF 0%, #8FD3FF 100%)", "pageBackground": "#1B5FD1", "titleFont": "VT323", "bodyFont": "Chakra Petch", "titleSize": 88, "titleWeight": 400, "bodySize": 20, "radius": 12, "shadow": "6px 6px 0 #101010", "imageStyle": "16-bit pixel art sprite on transparent background, bright saturated colours, crisp pixels"}
```

- **Signature**: a pixel ground strip of grass and dirt as an SVG at the bottom of every slide; pixel coins, bricks, stars and pipes as SVG or `rc3` sprites at the edges; questions on a black rounded "screen" card with a yellow pill label above it; the answer revealed on the next slide or by a click.
- **Avoid**: photos, gradients other than the sky, thin fonts.

### 21. Y2K Digital Collage
Best for: Gen-Z marketing, social media, trend reports, fan clubs, creative portfolios.

```json
{"name": "Y2K Digital Collage", "primary": "#5B5BF7", "accents": ["#FF6FD8", "#D7FF3A", "#FFFFFF", "#7C8BB8", "#FFFFFF", "#E3EDFF", "#1D1D5C"], "title": "#1D1D5C", "subtitle": "#5B5BF7", "body": "#2D2D6B", "slideBackground": "linear-gradient(160deg, #BFE3FF 0%, #E8F4FF 55%, #D9D2FF 100%)", "pageBackground": "#9FCBFF", "titleFont": "Space Grotesk", "bodyFont": "Space Grotesk", "titleSize": 60, "titleWeight": 700, "bodySize": 18, "radius": 10, "shadow": "6px 6px 0 #1D1D5C", "imageStyle": "iridescent chrome 3D objects, retro 2000s gadgets, holographic flowers, clean studio light, cut-out on white"}
```

- **Signature**: **retro window cards**: a gradient title bar with square buttons, a navy 2 px border and a flat offset shadow; a mixed title: bold grotesk plus one word in `Playfair Display` italic (richText `font`); a marquee ticker strip at the top edge; chrome and holographic cut-out objects overlapping the card corners; small pixel-icon stickers.
- **Avoid**: dark bases, serif body text, minimal empty slides.

### 22. Building Blocks
Best for: classroom routines, onboarding, kids' workshops, community programmes.

```json
{"name": "Building Blocks", "primary": "#2F6BFF", "accents": ["#FF5FA2", "#FFC21A", "#22A45D", "#8C8579", "#FFFFFF", "#FFF3D6", "#1B1B1B"], "title": "#1B1B1B", "subtitle": "#2F6BFF", "body": "#33312C", "slideBackground": "#F7F1E6", "pageBackground": "#EFE6D4", "titleFont": "Bricolage Grotesque", "bodyFont": "Be Vietnam Pro", "titleSize": 60, "titleWeight": 800, "bodySize": 18, "radius": 16, "shadow": "none", "imageStyle": "bright candid photo of children or teams, natural light, cheerful, clean background"}
```

- **Signature**: geometric primitives in the four primaries: quarter circles and semicircles, arches, `sun-flower` and `plus` shapes, peeking from behind photo corners; photos in radius-24 rounded rectangles; full-width colour header bars on content slides; white rounded cards with coloured pill labels; schedule rows as pills.
- **Avoid**: gradients, thin type, dark slides.

### 23. Retro Flat Lesson
Best for: secondary-school lessons, maths and science units, workshops with activities.

```json
{"name": "Retro Flat Lesson", "primary": "#E8765A", "accents": ["#2F8F8A", "#F6C85F", "#FFFFFF", "#9A8F85", "#FFFFFF", "#FBE3D6", "#2A2622"], "title": "#2A2622", "subtitle": "#E8765A", "body": "#3D3833", "slideBackground": "#F8E1D2", "pageBackground": "#EFCDB9", "titleFont": "Oswald", "bodyFont": "Nunito", "titleSize": 60, "titleWeight": 700, "bodySize": 19, "radius": 14, "shadow": "0 8px 0 rgba(42, 38, 34, 0.12)", "imageStyle": "flat retro vector illustration of school objects, muted peach teal and mustard, grain texture, no text"}
```

- **Signature**: flat illustrated objects (calculator, lamp, pencils) cropped at the slide edges; colour-blocked halves (teal | peach) behind examples; Think / Pair / Share or Step 1–3 as three white cards; a rotated "Answer key" sticker badge on solution slides; star bullets for learning outcomes; interactive checks ("Try this!") with real questions.
- **Avoid**: photos mixed with the illustrations, neon colours.

### 24. Dark UI Product
Best for: SaaS and product demos, app launches, tech company profiles, AI products.

```json
{"name": "Dark UI Product", "primary": "#7C5CFF", "accents": ["#3DD6F5", "#FFFFFF", "#A796FF", "#8A8A99", "#17171D", "#EAE6FF", "#0B0B0F"], "title": "#FFFFFF", "subtitle": "#A796FF", "body": "#B9B9C6", "slideBackground": "#0B0B0F", "pageBackground": "#050507", "titleFont": "Plus Jakarta Sans", "bodyFont": "Plus Jakarta Sans", "titleSize": 52, "titleWeight": 600, "bodySize": 18, "radius": 28, "shadow": "0 30px 80px rgba(0, 0, 0, 0.6)", "imageStyle": "clean architectural or product photography, cool blue hour light, glass and steel, minimal"}
```

- **Signature**: content inside rounded panels (`#17171D`, radius 28) with a violet glow rising from the panel's bottom edge; a pill tag "Topic 01" top-left; pagination dots and a small page counter as UI chrome; photos in rounded rectangles, often inside a device mock; KPI tiles.
- **Avoid**: serif fonts, illustrations, bright backgrounds.

### 25. Organic Brand Kit
Best for: brand guidelines, fashion and café brands, creative agencies, product lines.

```json
{"name": "Organic Brand Kit", "primary": "#E8742C", "accents": ["#1F4A2E", "#B9C98A", "#FFFFFF", "#8C8373", "#FFFFFF", "#EFE6D2", "#1B2A1F"], "title": "#1F4A2E", "subtitle": "#E8742C", "body": "#2F3A31", "slideBackground": "#EFE6D2", "pageBackground": "#E4D8BE", "titleFont": "Fraunces", "bodyFont": "Be Vietnam Pro", "titleSize": 56, "titleWeight": 800, "bodySize": 18, "radius": 0, "shadow": "none", "imageStyle": "colourful street-style fashion photography, warm film tones, candid, urban"}
```

- **Signature**: organic star, flower and blob SVGs in green and orange at the corners; a colour-block **photo bento** on the cover and gallery (photo tiles mixed with solid green and orange tiles); a left colour panel holding the title; small pill notes ("Tip:") in orange; a palette slide with swatch cards and a type-specimen slide.
- **Avoid**: gradients, cold blues, thin type.

### 26. Gradient Orb
Best for: marketing plans, agency proposals, product strategy, modern corporate.

```json
{"name": "Gradient Orb", "primary": "#FF4D4D", "accents": ["#FF9A3D", "#FF4FA3", "#FFFFFF", "#9A9AA5", "#FFFFFF", "#FFE3E3", "#1C1C24"], "title": "#1C1C24", "subtitle": "#FF4D4D", "body": "#44444F", "slideBackground": "#FFFFFF", "pageBackground": "#F4F4F6", "titleFont": "Epilogue", "bodyFont": "Epilogue", "titleSize": 56, "titleWeight": 700, "bodySize": 18, "radius": 12, "shadow": "none", "imageStyle": "bright modern office or product photo, warm pink-orange accent light, clean"}
```

- **Signature**: 1–3 large **blurred orbs** in the pink→orange gradient bleeding off the slide edges; one slide filled with the full gradient per section; a dot-grid SVG patch in a corner; a short accent dash under titles; a footer metadata line ("PLAN NAME | MONTH YEAR") and a vertical side label.
- **Avoid**: photos with busy colour, many accent colours.

### 27. Slate Editorial
Best for: real estate, interior design, consulting, professional services, portfolios.

```json
{"name": "Slate Editorial", "primary": "#6B7A8F", "accents": ["#B9A58C", "#FFFFFF", "#E7E3DC", "#9AA3AE", "#FFFFFF", "#EEF0F3", "#1F2630"], "title": "#1F2630", "subtitle": "#6B7A8F", "body": "#3F4752", "slideBackground": "#FFFFFF", "pageBackground": "#E7EAEE", "titleFont": "Cormorant Garamond", "bodyFont": "Be Vietnam Pro", "titleSize": 60, "titleWeight": 500, "bodySize": 17, "radius": 0, "shadow": "none", "imageStyle": "bright interior architecture photography, soft daylight, neutral palette, editorial composition"}
```

- **Signature**: alternating full slate slides (white type) and white slides (slate type); titles in light serif with **one italic phrase** ("Make Your *Game Plan*"); photos as clean rectangles, often two or three in a row; thin rules and small page numbers; testimonial cards with circle-cropped headshots.
- **Avoid**: icons in circles, bright accents, bold sans titles.
- **Variant, Fashion Editorial Pastel**: light grey base, black high-contrast serif, pale yellow / cyan / lavender label cards and one black metric panel with chartreuse bars.

### 28. Vintage Scrapbook
Best for: history and literature projects, group projects, memories, heritage trips.

```json
{"name": "Vintage Scrapbook", "primary": "#8C5A3C", "accents": ["#C9A66B", "#E9DFC7", "#FFFFFF", "#8A7F70", "#F6F0E1", "#EFE4CC", "#2E241B"], "title": "#2E241B", "subtitle": "#8C5A3C", "body": "#3F352B", "slideBackground": "#D9C7A7", "pageBackground": "#C8B28C", "titleFont": "Old Standard TT", "bodyFont": "Alegreya", "titleSize": 54, "titleWeight": 700, "bodySize": 19, "radius": 2, "shadow": "0 6px 14px rgba(46, 36, 27, 0.25)", "imageStyle": "vintage sepia photograph, aged paper texture, soft grain, faded edges"}
```

- **Signature**: **torn-paper edges** at the top and bottom; pressed-flower line drawings at the corners (SVG or `rc3`); newspaper scraps and stamps as images; cream note cards with paper-clip and tape pieces, slightly rotated (±2°); polaroid photos; a timeline on wooden sign arrows (SEQ-3 variant).
- **Avoid**: neon, flat vector icons, perfectly aligned grids.

### 29. Iridescent Chrome
Best for: project proposals, fashion and beauty launches, tech events, creative agencies.

```json
{"name": "Iridescent Chrome", "primary": "#6E4CF5", "accents": ["#C9B8FF", "#FFFFFF", "#FF9ED8", "#9B98A6", "#FFFFFF", "#F1EEFA", "#141414"], "title": "#141414", "subtitle": "#6E4CF5", "body": "#3A3A3A", "slideBackground": "#FAFAFB", "pageBackground": "#EDEAF3", "titleFont": "Anton", "bodyFont": "Be Vietnam Pro", "titleSize": 80, "titleWeight": 400, "bodySize": 16, "radius": 10, "shadow": "none", "imageStyle": "iridescent chrome liquid 3D render, holographic pearl surface, soft studio light, isolated on white"}
```

- **Signature**: one iridescent 3D blob image bleeding off a corner of every slide, in a different corner each time; giant condensed black titles with tight leading, stacked over two lines; small dot-grid patches; numbered outlined boxes (1 px black border, radius 10); a footer contact line.
- **Avoid**: photos of people on every slide, colour fills.

### 30. Duotone Mono
Best for: pitch decks, consulting, agencies, bold annual reports.

```json
{"name": "Duotone Mono", "primary": "#5E0F16", "accents": ["#C9868B", "#F8D7D7", "#FFFFFF", "#8E4A50", "#5E0F16", "#F8D7D7", "#3A070C"], "title": "#F8D7D7", "subtitle": "#F8D7D7", "body": "#F2C9C9", "slideBackground": "#5E0F16", "pageBackground": "#3A070C", "titleFont": "Anton", "bodyFont": "Archivo", "titleSize": 96, "titleWeight": 400, "bodySize": 15, "radius": 0, "shadow": "none", "imageStyle": "high-contrast studio portrait, plain background, monochrome, sharp"}
```

- **Signature**: the deck uses only two tones of one hue: dark slides with light type, then light slides with dark type; giant uppercase condensed titles stacked top-left; a **ghost echo** of the title in the slide's darker tone; big numbers ("$900B+") as tint text next to boxed labels; duotone portraits (grayscale plus `mix-blend-mode: multiply` over the hue); tiny 11–13 px body copy in columns.
- **Avoid**: any third colour, icons, rounded corners.

### 31. Cut-Paper Organic
Best for: wellness workshops, self-care, art education, cultural talks, NGOs.

```json
{"name": "Cut-Paper Organic", "primary": "#2F4A8A", "accents": ["#E0703A", "#3F7D4E", "#FFFFFF", "#A38C7E", "#F4ECE1", "#F6D9CC", "#2B211C"], "title": "#2B211C", "subtitle": "#2F4A8A", "body": "#43362F", "slideBackground": "#F4ECE1", "pageBackground": "#E3CDBE", "titleFont": "Playfair Display", "bodyFont": "Lexend", "titleSize": 56, "titleWeight": 600, "bodySize": 18, "radius": 0, "shadow": "none", "imageStyle": "warm soft lifestyle photography, calm people, natural textures, terracotta and cream tones"}
```

- **Signature**: cut-paper leaves, palms, starbursts and blobs as flat SVG in blue, orange and green, clustered at two opposite corners; alternating terracotta `#D98C74` and cream slides; photos in **organic crops** (`ellipse()` or `circle()` clip-path, or a blob-shaped SVG frame behind a rectangle); one bold keyword in otherwise regular serif titles.
- **Avoid**: hard grids, neon, stock icons.

### 32. Cinematic Dark Photo
Best for: nature and environment, travel and culture showcases, documentaries, film pitches.

```json
{"name": "Cinematic Dark Photo", "primary": "#D8B45A", "accents": ["#8FA38A", "#FFFFFF", "#E8D8A8", "#7A7A72", "#0A0A0A", "#F2E8CC", "#050505"], "title": "#E8D8A8", "subtitle": "#D8B45A", "body": "#D9D6CC", "slideBackground": "#0A0A0A", "pageBackground": "#000000", "titleFont": "Playfair Display", "bodyFont": "Be Vietnam Pro", "titleSize": 56, "titleWeight": 500, "bodySize": 17, "radius": 0, "shadow": "none", "imageStyle": "cinematic dark nature photography, deep shadows, rich greens, dramatic side light, 35mm film look"}
```

- **Signature**: every slide is a full-bleed photo; text lives in a **solid black box** that overlaps the photo edge, in a different position on each slide; the cover word in a swash script (`Great Vibes`) with the rest in serif; gold small-caps metadata in the corners; section words set huge and centred in caps over the photo (travel variant: `Anton`, white).
- **Avoid**: light slides, icons, bullet lists longer than 4 lines.

### 33. Kawaii Pastel
Best for: young learners, K-culture and fandom topics, class clubs, cute group projects.

```json
{"name": "Kawaii Pastel", "primary": "#FF7FAF", "accents": ["#A7C7FF", "#FFE08A", "#FFFFFF", "#C9A3B5", "#FFFFFF", "#FFE4EE", "#5A3A4A"], "title": "#FF5C98", "subtitle": "#8A6CFF", "body": "#5A3A4A", "slideBackground": "#FDE8F0", "pageBackground": "#F9CFE0", "titleFont": "Mali", "bodyFont": "Quicksand", "titleSize": 54, "titleWeight": 700, "bodySize": 19, "radius": 24, "shadow": "0 6px 0 rgba(255, 127, 175, 0.35)", "imageStyle": "kawaii chibi sticker illustration, pastel pink lilac and butter yellow, thick outlines, white border"}
```

- **Signature**: a **checker or gingham background**; bows, stars and a small mascot as `rc3` stickers; content on white cards with **stitched frames** (dashed border); notebook paper and clipboards as cards; chunky rounded titles in pink with a white offset shadow.
- **Avoid**: photos, dark colours, serif fonts.

### 34. Broadsheet
Best for: monthly reports, history and media-literacy lessons, press reviews, newsletters.

```json
{"name": "Broadsheet", "primary": "#111111", "accents": ["#8C8C8C", "#E8E4DA", "#FFFFFF", "#5C5C5C", "#F2EFE8", "#DAD5C8", "#111111"], "title": "#111111", "subtitle": "#3A3A3A", "body": "#262626", "slideBackground": "#F2EFE8", "pageBackground": "#DAD5C8", "titleFont": "Playfair Display", "bodyFont": "Spectral", "titleSize": 54, "titleWeight": 800, "bodySize": 16, "radius": 0, "shadow": "none", "imageStyle": "black and white documentary photograph, newspaper halftone feel, candid, high contrast"}
```

- **Signature**: a **masthead strip** on every slide (publication name · Volume · date between hairlines); a condensed serif headline centred or left; grayscale photos (`filter:grayscale 100%`); body text in 2–3 justified columns; one pull quote in italic; charts in black and grey only.
- **Avoid**: colour, rounded corners, icons.

### 35. Ink Wash
Best for: Asian history and literature, poetry, calligraphy, cultural festivals, martial arts.

```json
{"name": "Ink Wash", "primary": "#B8322A", "accents": ["#3A3A3A", "#D9CBB0", "#FFFFFF", "#8C8578", "#F1E9DA", "#E9D5C8", "#1C1A17"], "title": "#1C1A17", "subtitle": "#B8322A", "body": "#34302A", "slideBackground": "#F1E9DA", "pageBackground": "#E3D6BE", "titleFont": "Charm", "bodyFont": "Alegreya", "titleSize": 60, "titleWeight": 700, "bodySize": 19, "radius": 0, "shadow": "none", "imageStyle": "traditional East Asian ink wash painting, sumi-e brush strokes, misty mountains, muted black with red accents, rice paper"}
```

- **Signature**: ink-wash paintings (`fu`) as full-bleed or half-bleed backgrounds; a **red brush-stroke swash** SVG behind each title; script titles; torn rice-paper edges; a red seal square as a sticker; cream text panels over the painting.
- **Avoid**: sans-serif titles, bright colours other than the red.

### 36. Infographic Flat
Best for: consulting frameworks, process training, customer journeys, strategy explainers.

```json
{"name": "Infographic Flat", "primary": "#1F3B73", "accents": ["#2BB673", "#4FA3E0", "#FFFFFF", "#8795A8", "#FFFFFF", "#E9F0F8", "#13213F"], "title": "#13213F", "subtitle": "#2BB673", "body": "#3A4659", "slideBackground": "#F1F4F9", "pageBackground": "#E1E7F0", "titleFont": "Montserrat", "bodyFont": "Be Vietnam Pro", "titleSize": 44, "titleWeight": 700, "bodySize": 16, "radius": 12, "shadow": "0 8px 24px rgba(19, 33, 63, 0.10)", "imageStyle": "flat vector icon illustration, navy and green, simple shapes, white background"}
```

- **Signature**: **one diagram per slide** with a centred title and a one-line lede; the native infographics cover it: `journey`, `hierarchy`, `cycle`, `matrix`, `funnel`, `relationship`, `decision`; drawn diagrams otherwise: puzzle, target (concentric `oval` shapes), stairs, pyramid; icons in navy→green circles; white label cards with a left accent bar.
- **Avoid**: photos, decorative type.

### 37. One-Hue System
Best for: company decks, reports, training — any brand with one strong colour.

```json
{"name": "One-Hue Green", "primary": "#1F9D55", "accents": ["#0F5E32", "#7FD3A0", "#FFFFFF", "#9AB5A4", "#FFFFFF", "#E6F6EC", "#0B3B20"], "title": "#0B3B20", "subtitle": "#1F9D55", "body": "#2E4A3A", "slideBackground": "#FFFFFF", "pageBackground": "#E6F6EC", "titleFont": "Lexend", "bodyFont": "Lexend", "titleSize": 50, "titleWeight": 700, "bodySize": 18, "radius": 14, "shadow": "none", "imageStyle": "clean bright photography with a subtle green colour grade"}
```

- **Signature**: the cover and section dividers are filled edge to edge with the saturated hue; interiors are white; every card border, table header, progress bar, chart series and heat-map cell uses a step of the same hue ramp (5–6 steps); the accent1 chart colour is a darker step of that hue, not a new colour.
- **Avoid**: a second accent hue.
- **Variant, Gradient Spine**: the cover and dividers use a two-hue gradient (violet → hot pink), interiors are cream, and pill badges graduate through the gradient's steps.

### 38. Dev Console
Best for: engineering reviews, developer docs, API launches, hackathons, IT training.

```json
{"name": "Dev Console", "primary": "#2DD4BF", "accents": ["#5B8DEF", "#94A3B8", "#FFFFFF", "#64748B", "#111A2E", "#DDF7F3", "#070B16"], "title": "#FFFFFF", "subtitle": "#2DD4BF", "body": "#CBD5E1", "slideBackground": "#0B1224", "pageBackground": "#070B16", "titleFont": "JetBrains Mono", "bodyFont": "IBM Plex Sans", "titleSize": 46, "titleWeight": 700, "bodySize": 17, "radius": 10, "shadow": "none", "imageStyle": "dark isometric tech illustration, glowing circuit lines, teal and blue, minimal"}
```

- **Signature**: monospace headlines, some prefixed with `>` or `//`; slate cards (`#111A2E`) with a teal left accent bar; code blocks as mono text on `#050914` cards; chevron step rows; area and line charts in steel-blue steps; pill tags for status ("beta", "v2.1").
- **Avoid**: serif, photos of people, rounded blobs.

### 39. Ember Glow
Best for: pitch decks, proposals, keynotes, premium services, evening events.

```json
{"name": "Ember Glow", "primary": "#FF8A3D", "accents": ["#FFC08A", "#FFFFFF", "#E0A071", "#8C7466", "#2A1A12", "#FFE3CC", "#120B08"], "title": "#FFE3CC", "subtitle": "#FF8A3D", "body": "#E8CDB8", "slideBackground": "#120B08", "pageBackground": "#080504", "titleFont": "Bricolage Grotesque", "bodyFont": "Be Vietnam Pro", "titleSize": 56, "titleWeight": 600, "bodySize": 18, "radius": 12, "shadow": "none", "imageStyle": "warm low-key photography, candlelight and amber glow, deep shadows, intimate"}
```

- **Signature**: an orange-amber **corner glow** on the cover and dividers, in the lower-right corner; peach type; chocolate cards (`#2A1A12`) with a burnt-orange 1 px border; numbered bordered rows; chevron process arrows in brown steps; a peach callout strip at the bottom of key slides.
- **Avoid**: cold colours, white slides.

### 40. Dark Pastel Cards
Best for: creative strategy, design reviews, research findings, modern business.

```json
{"name": "Dark Pastel Cards", "primary": "#F5B8C8", "accents": ["#A9D6F5", "#F3E7B3", "#B9D8B4", "#7E9A97", "#123B3A", "#F4EFE3", "#0B2625"], "title": "#F4EFE3", "subtitle": "#F5B8C8", "body": "#CFE0DC", "slideBackground": "linear-gradient(160deg, #0B2625 0%, #13463F 100%)", "pageBackground": "#071A19", "titleFont": "Unbounded", "bodyFont": "Be Vietnam Pro", "titleSize": 48, "titleWeight": 600, "bodySize": 17, "radius": 16, "shadow": "none", "imageStyle": "moody teal-toned photography with soft pastel highlights, dreamy, calm"}
```

- **Signature**: a dark teal base; thin 1 px cream-bordered cards; each card or chart series gets one pastel (pink, sky, butter, sage) in a fixed order; donuts and progress bars in pastels; a pill eyebrow badge above every title.
- **Avoid**: photos on every slide, saturated primaries. photos, light serif, quarter-circle leaf cut-outs. in faded tints, photos with thick offset frames, header pills. (`Yeseva One`), soft cards in sage, blue-grey and pink.
- **Variant, Split Personality**: alternate dark slate-green sections with blush-pink pages, and make the one accent electric chartreuse `#D4FF3A`.
