# Game templates: what exists and how to find it

Contents: 1 Finding game templates, 2 The templates and their screens, 3 Choosing, 4 Insert rules

## 1. Finding game templates

- `listTemplates {category: "Game"}` returns the game templates; `category: "Quiz"` adds quiz
  shows. `category` must equal a gallery category exactly (any case): Business, Education, Game,
  Quiz, Healthcare, Nonprofit, Design, Marketing, Travel. A genre word ("trivia") there matches
  nothing: use `query` for words, for example `query: "escape"` or `query: "jeopardy"`.
- `limit` defaults to 20 (maximum 50) and results are sorted by name: compare `total` with the
  number returned before deciding nothing fits.
- `variableCount` above zero marks a game or quiz-show template (it defines variables such as
  lives or score and its screens are wired together). Ordinary templates have 0.
- `layouts` per tag shows which game screens a template has. `getTemplateLayouts {templateId}`
  lists every slide with `slideId`, `tag`, `actionCount` (interactions already wired) and slots;
  `getTemplateLayouts {templateId, tag: "game board"}` narrows to one tag.
- `previewUrl` shows every slide to the user.

A game template is a **library in a game look**, not a finished game. Its ordinary layouts
(`title`, `title and content`, `list-N`, `chart`, `timeline`, `question-*`...) have no game logic
and serve any deck in that style. Its game screens carry genre tags and slide-local mechanics.
The links between screens of a whole game, its music and its player mode are not part of what a
template gives a new deck: you wire and set them.

## 2. The templates and their screens

Names, variables and tags below come from the template packs in the app repository
(`server/templates/presentations`). The live gallery is the authority: confirm with `listTemplates`
and `getTemplateLayouts`, because a template can be renamed, hidden or added.

| Template (gallery name) | Language of sample copy | Variables | Game screen tags |
|---|---|---|---|
| Quiz Show with Lives, Cyber HUD | Vietnamese | `lives` | `game cover`, `how to play`, `section`, `quiz-question`, `end`, `game-over` |
| Escape Room, The Last Letter | English | `keysFound`, `k1`, `k2`, `k3`, `secondsLeft` | `game cover`, `letter`, `room`, `puzzle`, `door`, `game-over`, `end` |
| Jeopardy Scoreboard, Tech Neon | English | `score1` to `score4` | `jeopardy board`, `category intro`, `clue slide`, `answer reveal`, `team scoreboard`, `daily double`, `final round`, `winner podium`, `how to play` |
| Last One Standing, Kawaii Pastel | English | `lives`, `phase`, `alive`, `hp1` to `hp4` | `hearts`, `speed round`, `shuffled answers`, `elimination board`, `sudden death`, `flash card`, `word of the round`, `winner podium`, `how to play` |
| Dice Arena, Arcade Pixel | English | `lives`, `coins`, `position`, `roll`, `luck`, `turns`, `bossDone`, `correct` | `game start`, `how to play`, `game board`, `challenge`, `lucky block`, `boss battle`, `win`, `scoreboard`, `game over` |
| Workplace Safety, Brutalist Poster | English | `safety`, `risk`, `hazards`, `ppe` | `scenario intro`, `decision point`, `consequence`, `safety score`, `risk meter`, `ending`, `safety certificate`, `hazard identification`, `ppe checklist`, `do and dont`, `incident report`, `toolbox talk` |

Every one also has the ordinary layout tags and most also the ten `question-*` layouts
(`question-mc`, `-mr`, `-tf`, `-fte`, `-sq`, `-ma`, `-dd`, `-fb`, `-dw`, `-sl`). Quiz Show with
Lives and Escape Room carry their questions inside the game screens (`quiz-question`, `puzzle`).

`secondsLeft` in Escape Room is the countdown of the timed door; the engine's own timer
(`addInteraction {eventType: "timer"}` and `{{$timeLeft}}`) is described in `mechanics.md`.

## 3. Choosing

| Game wanted | Mechanics (see mechanics.md) | Questions | Start from |
|---|---|---|---|
| Quiz show with lives | M1, M4, M5 | multiple-choice, true-false, fill-blanks | Quiz Show with Lives, or Last One Standing |
| Jeopardy board, team play | M2, M5 | any graded type | Jeopardy Scoreboard |
| Escape room, countdown door | M3, M4 | fill-text-entry codes, matching, sequence | Escape Room |
| Dice or board game | M10, M2 | any graded type | Dice Arena |
| Elimination, speed round | M1, M4 | true-false, multiple-choice | Last One Standing |
| Millionaire ladder | M1 with one life, M5 | multiple-choice, one attempt | Quiz Show with Lives |
| Branching scenario | M6, M9 | survey questions for reflection | Workplace Safety |
| Flashcards | flip cards (`design-interactions`) | survey self-check | Last One Standing (`flash card`) or a `flip cards` template |
| Sort it, put in order, map quest | none | matching, drag-words, label, sequence, hotspot | any template; see `create-quiz` if installed |

If the topic and the language of the sample copy differ from the user's, say so and fill every
text anyway (layouts work in any language); for Vietnamese content Quiz Show with Lives has
Vietnamese sample copy.

## 4. Insert rules

- Insert the screens of one game **in one `insertTemplateSlides` call**, in play order, passing
  `templateId`. Jumps by slide id among those slides are re-pointed to the new slides.
- For several copies of one screen (many `clue slide` or `puzzle` screens), duplicate the filled
  slide with `duplicateSlide` after inserting, then check its interactions with `getInteractions`:
  a copy keeps the jumps of the original.
- Without `templateId` the tool uses the deck's own starter template: always pass it.
- After the insert, read the result's `warnings`: each counts a jump to a screen you did not insert.
- `getInteractions {slideId}` on each screen is the source of truth for what is wired.
