# Deck genres: proven slide sequences

Most real decks belong to a genre with a recognisable running order: a pitch deck, a lesson, a
quiz night, a brand-guidelines book. Use the sequence below as a **menu, not a checklist**:
take the genre that fits the request, drop every slide the content does not need, add what it
does need, and stop at the length the user asked for. A request for "5 slides about our launch"
gets five slides, not the whole product-launch sequence.

Each line reads: slide, then the layout tag that carries it. The tags are the ones in
`template-layouts.md`. Every template has the common ones (`title`, `title and content`,
`list-3` to `list-6`, `timeline`, `chart`, `process`, `comparison`, `table`, `overview`,
`summary`, `gallery`, `statistic`, `quote`, `section`, `end`, `question-*`); some templates also
have genre tags of their own (`problem`, `solution`, `traction`, `team`, `roadmap`, `ask`,
`key idea`, `worked example`, `answer key`, `kpi`, `budget`, `swot`...). `listTemplates` shows
the tags a template has, so prefer a template whose genre tags match the genre, and fall back to
the common tag when it has none.

Keep the running order, vary the layout variants (see `template-layouts.md`), and never put two
slides with the same tag next to each other unless the content repeats on purpose.

Contents: 1 Pitch deck, 2 Lesson, 3 Quiz / game show, 4 Brand guidelines, 5 Company profile,
6 Portfolio, 7 Group / school project, 8 Thesis defence, 9 Business / finance report,
10 Workshop / webinar, 11 Marketing plan, 12 Product launch, 13 Travel and culture showcase,
14 Event planner, 15 Framework / infographic deck, 16 Mood / vision board

## 1. Pitch deck (10-12 slides)
1. Cover: company, one-line promise: `title`
2. Problem (3 pains): `list-3` (or `problem`)
3. Solution (the product and 3 benefits): `title and content` (or `solution`)
4. Market opportunity (one giant number): `statistic` or `chart`
5. Business model: `overview` or `process`
6. Traction (3-4 big numbers with labels): `summary`, `statistic` or `traction`
7. Go-to-market, strategy: `process`
8. Competition: `comparison` (or `competition`)
9. Team (people with roles): `gallery` (or `team`)
10. Financials or roadmap: `chart` or `timeline`
11. The ask and contact: `end` (or `ask`)

## 2. Lesson (15-25 slides)
1. Cover: lesson title, subject, grade: `title`
2. Lesson outline (3-4 parts): `overview`
3. Learning outcomes ("By the end you will be able to..."): `list-3`
4. Hook or discovery question: `title and content`
5. Concept explanation (definition plus visual): `title and content` (or `key idea`)
6. Rules or steps: `process`
7. Worked example (problem | solution): `comparison` (or `worked example`)
8. Activity (think, pair, share): `list-3`
9. "Try this" checks, 2-4 questions: `question-mc`, `question-fb`, `question-dd`
10. Answer key: `title and content` (or `answer key`)
11. Challenge, a harder question: any `question-*`
12. Summary ("Today you learned..."): `summary`
13. Assignment or homework: `end`

Build the interactivity for real (questions with scoring), because that is what separates an
e-learning lesson from a slide deck. Use the `create-quiz` skill for the questions.

## 3. Quiz / game show (12-30 slides)
1. Cover with the game name: `title` (or `game cover`)
2. How to play (rules, round types): `list-3` (or `how to play`)
3. Categories or teams: `overview`
4. Round divider ("Round 1: Easy"): `section`
5. Question, one per slide: `question-mc`, `question-fb`, `question-tf`...
6. Answer reveal with a fun fact: `title and content`
7. Repeat 4-6 per round; a scoreboard between rounds: `statistic` or `summary`
8. Final, winner: `end`

Give each round a different difficulty. The app has no countdown timer.

## 4. Brand guidelines (10-14 slides)
Cover (`title`) · contents (`overview`) · brand story: mission, vision, values (`list-3`) ·
logo (`title and content` or `gallery`) · logo variations and misuse (`comparison` or
`do and don't`) · colour palette (`gallery` or `table`) · typography (`table` or
`title and content`) · graphic elements (`gallery`) · photography style (`gallery`) · voice and
tone, we are / we are not (`comparison`) · mockups (`gallery`) · contact (`end`).

## 5. Company profile (12-16 slides)
Cover · contents · about us (`title and content`) · vision · mission · services, 3-6 cards
(`list-3` to `list-6`) · our process (`process`) · market or clients (`gallery`) · goals ·
projects (`gallery`) · achievements, numbers (`statistic` or `summary`) · team · future plan
(`timeline`) · contact (`end`).

## 6. Portfolio (10-15 slides)
Cover (name, discipline) · about me · skills and tools (`list-4` to `list-6`) · 3-5 case
studies, each a brief (`title and content`), a process (`process`), a result (`gallery` or
`statistic`) · testimonials (`quote`) · contact (`end`).

## 7. Group / school project (10-14 slides)
Cover · meet the group (`gallery`) · introduction · background (`chart`) · project overview ·
topics 1-2-3 (`list-3`) · timeline (`timeline`) · goals (`list-3` or `list-4`) · results and
findings (`chart`, `statistic`) · conclusion (`summary`) · a favourite quote (`quote`) · thank
you (`end`).

## 8. Thesis defence (15-25 slides)
Title (candidate, supervisor, date) · outline (`overview`) · background and motivation ·
problem statement · research questions or hypotheses (`list-3`) · literature map (`diagram` or
`overview`) · methodology (`process`) · data and sample (`table`) · results, one finding per
slide (`chart` or `statistic`) · discussion · limitations · conclusion and contributions
(`summary`) · future work · references · Q&A (`end`).

## 9. Business / finance report (8-12 slides)
Cover (period) · executive summary, 3 highlights (`list-3`) · KPI highlights (`statistic` or
`kpi`) · revenue and P&L (`chart`) · sales channels (`list-4`) · conversion funnel (`process`
or `funnel`) · key wins and challenges (`comparison`) · risks · strategy and action plan
(`process`) · thank you (`end`).

## 10. Workshop / webinar (15-30 slides)
Cover (title, host) · housekeeping (`list-3`) · agenda (`overview`) · about the speaker
(`title and content`) · warm-up question · 3-5 modules, each an opener statement (`quote` or
`statistic`), 2-3 content slides, a reflection question · myths vs facts (`comparison`) ·
practice sheet (`table`) · key takeaways (`summary`) · Q&A · resources (`end`).

## 11. Marketing plan (12-16 slides)
Cover · agenda · objectives (`list-3`) · needs and solution (`comparison`) · headline number
(`statistic`) · targets (`chart`) · growth (`chart`) · segments (`chart`) · strategy, 3
numbered strategies (`list-3`) · steps (`process`) · timeline (`timeline`) · team · thank you
(`end`).

## 12. Product launch (12-16 slides)
Cover · brand introduction · the product (`title and content`) · market opportunity ·
target audience (`list-4`) · product concept · key features (`list-3`) · packaging or design
(`gallery`) · variants (`list-4`) · competitive advantage (`comparison`) · marketing campaign
(`process`) · launch event (`timeline`) · sales and distribution · expected results
(`statistic`) · thank you (`end`).

## 13. Travel and culture showcase (8-14 slides)
Cover with the place name · agenda · one `section` slide per chapter (landscape, regions,
cuisine, people), each followed by a content slide (`gallery`, `list-3`, `title and content`) ·
thank you (`end`).

## 14. Event planner (wedding, conference) (10-14 slides)
Cover (names or event, date) · our story · schedule (`timeline` or `table`) · venue (`title and
content`) · theme and mood (`gallery`) · vendors (`list-4`) · budget (`chart` plus `table`) ·
checklist (`list-5`) · guest info · contacts (`end`).

## 15. Framework / infographic deck (8-12 slides)
One framework per slide with a centred title and a one-line lede: process steps (`process`),
core components (`diagram` or `list-4`), touchpoint categories (`list-5`), evolution
(`timeline`), maturity levels (`process`), continuous cycle (`diagram`), objectives
(`list-3`), traditional vs modern (`comparison`).

## 16. Mood / vision board (6-10 slides)
Cover · one board per theme (`gallery`, with 2-3 keywords) · colour palette (`gallery`) ·
keywords and feelings (`list-4`) · inspiration sources (`list-3`) · next steps (`end`).
