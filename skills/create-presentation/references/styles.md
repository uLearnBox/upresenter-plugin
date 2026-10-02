# Styles: choosing a look and finding it in the gallery

Every gallery template is one **style** (a design language): a theme with colors and fonts plus a
library of layouts drawn in that style. Most template names end with their style ("Startup Pitch
Deck — Duotone Mono"), so `listTemplates {query: "<style name>"}` finds a style directly. This
file maps topics to styles, styles to templates, and says what to do when the gallery has no
template in the style the user wants.

Contents: 1 Choosing a style, 2 Topic to style, 3 Style to template, 4 A style the gallery lacks,
5 Game templates for ordinary decks

## 1. Choosing a style

1. **The user names a style or a mood** ("Swiss", "dark and bold", "phong cách học đường",
   "sang trọng"): match it loosely to a style in sections 2 and 3, Vietnamese and English mood
   words alike.
2. **The user shows a reference** (a picture, a site, another deck): pick the style closest in
   base color, accent, font character and picture mood.
3. **Otherwise choose from the topic, audience and tone** (section 2). When two fit equally,
   prefer the calmer one for learning content and the bolder one for persuasion.
4. Say which style you used and why in the report, so the user can ask for another.

## 2. Topic to style

| Topic, audience, purpose | First choice | Also fits |
|---|---|---|
| Business review, sales kickoff, sports, product launch | Bold Dark Accent | Brutalist Poster, Duotone Mono |
| Corporate training, HR, policy, compliance, onboarding | Clean Corporate | Medical Clinical, Swiss Editorial |
| Strategy, consulting, research findings, reports | Swiss Editorial | Data Report, Infographic Flat |
| Finance, quarterly results, investor update, KPIs | Data Report | Swiss Editorial, One-Hue System |
| Startup, product, fundraising | Duotone Mono | Gradient Orb, Dark UI Product |
| SaaS, app and AI product demos | Dark UI Product | Gradient Orb, Dev Console |
| Primary and secondary school lessons | Warm Classroom | Building Blocks, Retro Flat Lesson |
| Secondary maths and science with activities | Retro Flat Lesson | Science Lab, Infographic Flat |
| Early childhood, stories (3-8 years) | Storybook | Warm Classroom, Kawaii Pastel |
| University lectures, thesis defence | Academic Scholarly | Swiss Editorial, Science Lab |
| STEM, engineering, lab work | Science Lab | Dev Console, Data Report |
| IT training, engineering reviews, developer topics | Dev Console | Science Lab, Dark UI Product |
| Health, patient education, safety | Medical Clinical | Clean Corporate, Cut-Paper Organic |
| Wellbeing, environment, NGOs, community | Cut-Paper Organic | Medical Clinical, Soft Pastel |
| Real estate, interiors, professional services | Slate Editorial | Ember Glow, Swiss Editorial |
| Premium proposals, evening keynotes, luxury, hospitality | Ember Glow | Slate Editorial, Cinematic Dark Photo |
| Travel, nature and culture showcases | Cinematic Dark Photo | Vietnamese Heritage, Cut-Paper Organic |
| Vietnamese culture, Tết, traditions, local history | Vietnamese Heritage | Ink Wash |
| Asian history, literature, poetry | Ink Wash | Vietnamese Heritage, Academic Scholarly |
| History or literature projects, memories | Vintage Scrapbook | Broadsheet, Ink Wash |
| Newsletters, monthly reports, media literacy | Broadsheet | Swiss Editorial |
| Marketing plans, agency proposals | Gradient Orb | One-Hue System, Duotone Mono |
| Brand guidelines, café and fashion brands | Organic Brand Kit | Slate Editorial, Duotone Mono |
| Company profile in one brand color | One-Hue System | Clean Corporate |
| Frameworks, processes, customer journeys | Infographic Flat | Clean Corporate, Data Report |
| Creative strategy, design reviews, user research | Dark Pastel Cards | Soft Pastel |
| Lifestyle, social media, design thinking, workshops | Soft Pastel | Warm Classroom |
| Creative briefs, youth events, music, streetwear | Acid Brutalist | Brutalist Poster, Y2K Digital Collage |
| Gen-Z marketing, trend reports, fan clubs | Y2K Digital Collage | Kawaii Pastel, Acid Brutalist |
| Bold campaigns, safety posters | Brutalist Poster | Acid Brutalist |
| Classroom routines, kids' programmes | Building Blocks | Warm Classroom |
| Quizzes, trivia and game nights (teens, adults) | Cyber HUD | Tech Neon |
| Games for children, young learners | Arcade Pixel | Kawaii Pastel |
| Seasonal and holiday decks | the seasonal template of that occasion (Halloween, Mid-Autumn, Vietnamese Teachers' Day, Vietnamese Women's Day) | |

## 3. Style to template

The gallery grows, so trust `listTemplates` over this list. The templates seen so far, with the
`query` that finds each (a word of its name) and the kind of deck it was built for:

| Style | `query` | Built for |
|---|---|---|
| Bold Dark Accent | `Bold Dark` | business and training |
| Clean Corporate | `Onboarding` | onboarding and compliance (fluid: `Employee Handbook`) |
| Swiss Editorial | `Swiss Editorial` | business plan (fluid: `Project Report`) |
| Data Report | `Quarterly Financial` | financial report |
| Duotone Mono | `Startup Pitch` | startup pitch |
| Gradient Orb | `Gradient Orb` | marketing plan |
| One-Hue System | `One-Hue` | company capability profile |
| Infographic Flat | `Consulting Frameworks` | frameworks and processes |
| Dark UI Product | `Product Demo` | SaaS and AI product demo |
| Dark Pastel Cards | `Design Review` | user research and design review |
| Dev Console | `Dev Console` | IT training and engineering review |
| Slate Editorial | `Real Estate` | real estate and interiors |
| Ember Glow | `Ember Glow` | premium proposal and evening keynote |
| Cinematic Dark Photo | `Travel Destination` | travel destination |
| Organic Brand Kit | `Brand Guidelines` | brand guidelines |
| Cut-Paper Organic | `Community & Environment` | community and environment |
| Medical Clinical | `Patient Education` | patient education |
| Science Lab | `Science Lab` | lab experiments |
| Academic Scholarly | `Thesis Defence` | thesis defence |
| Retro Flat Lesson | `Middle School` | middle-school maths and science |
| Warm Classroom | `Fun Classroom` | primary classroom |
| Building Blocks | `Classroom Routines` | classroom routines |
| Storybook | `Storytime` | early-years stories |
| Ink Wash | `Literature & History` | high-school literature and history |
| Vietnamese Heritage | `Lunar New Year` | Lunar New Year and Vietnamese culture |
| Broadsheet | `Newsletter` | monthly newsletter |
| Soft Pastel with doodles | `Pastel Doodle` | projects |
| Acid Brutalist | `Creative Portfolio` | creative portfolio |
| Y2K Digital Collage | `Gen Z` | campaigns and fan clubs |
| Brutalist Poster | `Workplace Safety` | workplace safety |
| Seasonal | `Halloween`, `Mid-Autumn`, `Teachers' Day`, `Women's Day` | those occasions |
| Cyber HUD | `Quiz Show` | quiz show with lives (game, section 5) |
| Tech Neon | `Jeopardy` | Jeopardy scoreboard (game, section 5) |
| Kawaii Pastel | `Last One Standing` | elimination quiz (game, section 5) |
| Arcade Pixel | `Dice Arena` | dice game (game, section 5) |
| Vintage Scrapbook | `Escape Room` | escape room (game, section 5) |

## 4. A style the gallery lacks

Some styles have no template yet: **Startup Gradient** (gradient blobs, glass cards), **Nature
Calm** (greens, organic crops), **Luxury Elegant** (black, ivory, gold, spacious centring),
**Vintage Heritage** (sepia, frames, ornaments) and **Iridescent Chrome** (3D chrome objects,
condensed titles). In that case, in this order:

1. **Offer the nearest template** and say it is the nearest, not the exact style:

   | Wanted | Nearest templates |
   |---|---|
   | Startup Gradient | Gradient Orb, Dark UI Product, Duotone Mono |
   | Nature Calm | Cut-Paper Organic, Medical Clinical, Cinematic Dark Photo |
   | Luxury Elegant | Ember Glow, Slate Editorial |
   | Vintage Heritage | Vintage Scrapbook (a game library, section 5), Ink Wash, Academic Scholarly |
   | Iridescent Chrome | Gradient Orb, Dark UI Product |

2. **Borrow a theme and compose.** `insertTemplateSlides {templateId, applyThemeFromTemplate:
   true}` with no slides gives the deck the colors and fonts of any template. Slides the
   template's layouts do not cover are then composed with `composed-slides.md`, using the
   signature devices of the wanted style listed there (section 4). Composing takes many more
   calls than inserting layouts: say so, and keep the number of composed slides small.
3. **A built-in theme.** `selectAppropriateTheme {query, style?, themeName?}` lets the app choose
   one of its built-in themes from a description. It asks an AI model and may spend the account's
   credits (the result says how many), and a theme alone is only colors and fonts: the layouts
   still come from a template or from composing.

Never invent a style the tools cannot reach: custom themes (your own colors and fonts) can only
be made in the editor's Design tab by the user. Tell them that when they need an exact brand.

## 5. Game templates for ordinary decks

The templates in the Game and Quiz categories (a template with `variableCount` above zero) are
**layout libraries in a game look**: besides their game screens (tagged with genre tags such as
`game-board`, `clue slide`, `team scoreboard`, `round`, `game over`) they carry the ordinary
layouts (`title`, `title and content`, `list-3` to `list-6`, `chart`, `timeline`...) in the same
look, without game buttons. So:

- for an ordinary deck in that style (a neon tech talk, a pixel-art lesson, a scrapbook history
  project), use the template like any other and insert only its ordinary tags;
- for a game, read `games.md`: the game screens carry slide-local mechanics, and the wiring
  between screens is built with the interaction tools.
