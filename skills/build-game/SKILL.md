---
name: build-game
description: "Build a learning game or quiz show in uPresenter starting from the app's game templates (Quiz Show with Lives, Escape Room, Jeopardy Scoreboard, Last One Standing, Dice Arena, Workplace Safety branching): lives, score, countdown timer, random dice or random slide, boards with done tiles, keys and a locked door, branching story, Game player mode, navigation lock and looping background music. Use when the user asks for a game, quiz show, escape room, Jeopardy board, trivia night, dice or board game, timed challenge, lives or scoreboard in uPresenter, in any language (for example tạo game ô chữ/đố vui, mật thất, trò chơi có 3 mạng, crear un juego de preguntas, sala de escape, 制作一个答题游戏, 密室逃脱)."
---

# Build a game in uPresenter

uPresenter has game templates: layout libraries in a game look whose **game screens** already carry
buttons, variables and reactions (hearts that react to wrong answers, tiles, keys, scoreboards).
Start from one of them, insert the screens the game needs, fill the texts, wire what is missing and
set up the player. Compose a screen by hand only when no template screen fits.

## Ground rules

- **Ask with the host's choice picker, not a paragraph.** When the user must decide (a template, a
  style or theme, a game, a language, a cost), use the host's structured question tool if it has one
  (for example `AskUserQuestion` in Claude Code, `request_user_input` in Codex, or the question picker of
  the Claude and ChatGPT apps): one question per decision, 2 to 4 options each with a one-line
  description (put a preview link in it), the recommended option first and marked "(Recommended)", and
  always room for the user's own answer. Batch several decisions into one call. Only when the host has no
  such tool, give a short numbered list and wait for the reply.
- Execute uPresenter operations through the `upresenter` MCP tools. Do not substitute Computer Use,
  browser automation, DOM scripts or direct HTTP calls unless the user explicitly requests that method.
- If a required tool is missing, use `searchTools`, `describeTool` and `callTool` when available.
  If those gateways are also missing, stop and report incomplete MCP tool discovery with the missing
  tool names. A successful sign-in does not prove that tools loaded; do not ask the user to sign in
  again without an authentication error. An absent editor requires the user to open the editor link,
  not a switch to browser automation.
- Call tools one at a time. Pass `slideId` with every `shapeId` (shape ids repeat on every slide)
  and use ids that tool results returned.
- The editor tab must be open: `listOpenEditorContexts`; if none, `getNewPresentationLink` (with
  the `templateId`) and ask the user to open it and keep the tab open, then check again. With
  several tabs pass the right `sessionId`.
- QA loop after each screen or group of screens: `reviewSlides`, then `getSlideImage` and look at
  it. Fix text that spills, leftover template sample text and unfilled placeholders.
- A game is a **slide** presentation. Fluid decks (scrolling cards) come only from fluid templates
  and are not for games; never convert with `setLayoutType`.
- The tools build a game but cannot play it. Say what you could not test.
- Not available as tools: narration or TTS (only when the connection lists `addNarration`), export, classes, reports, music generation. Point the user to the editor for these.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

1. **Understand the game.** Subject, audience, number of rounds or questions, language, win and
   lose conditions, whether a timer or lives are wanted. Ask only what is missing.
2. **Find the template (template-first).** `listTemplates {category: "Game"}` (also try
   `category: "Quiz"` and `query: "game"`), check `total` against what came back, and keep the ones
   with `variableCount` above zero. Pick by mechanic with the table in
   `references/game-templates.md`, show the user two options with `previewUrl` and a reason each,
   and wait for the choice. `getTemplateLayouts {templateId}` shows the game screens, their tags
   and `actionCount` (how wired each is).
3. **Open the deck.** `getNewPresentationLink {templateId}`, user opens it, then
   `listOpenEditorContexts` and `getPresentationInfo` (`layoutType: slide`,
   `templateId` is the chosen one). `setPresentationName`, `setPresentationLanguage`.
4. **Plan the flow** as one line per screen: cover with Start, How to play, the play screens in
   order (board, clue, question, hub, puzzle, door...), result screens (win, game over,
   scoreboard), end. Choose the template screen for each by tag.
5. **Insert all game screens in one call** with `insertTemplateSlides {templateId, slides, index}`
   in play order. Jumps between slides inserted together are re-pointed; jumps to slides not
   inserted are removed and counted in `warnings`. Delete the starter slide if it is not used.
6. **Fill every slot** with `setShapeText` (within `maxChars`), replace every `sample`, then
   questions with `setQuestionAnswers` and `setQuestionSettings`. Keep `{{lives}}`, `{{score}}`,
   `{{$timeLeft}}` placeholders written in one piece.
7. **Check the wiring**: `getVariables`, then `getInteractions {slideId}` on every game screen. Add
   the links that are missing (a board button to its question, a door to the win screen) with
   `addInteraction`, and fix ones that point at the wrong slide with `updateInteraction`. Recipes
   are in `references/mechanics.md`.
8. **Set the deck up** (details in `references/mechanics.md`, section 3): Start button runs
   `unmute-media` first, then `next-slide`; `setPlayerMode {mode: "game"}`; each question
   `setQuestionSettings {mode: "graded", navigation: "stay", maxAttempts: 0}`; a Play again button
   that resets every game variable and returns to the first slide.
9. **Music.** `setBackgroundMusic {source, name, volume}` with an HTTPS audio URL (WAV loops
   cleanly; MP3 leaves a gap) or base64 plus `mimeType` the user supplied. The tools cannot
   make music: when there is no file, tell the user to add a track in the editor's Settings,
   Background music. A silent game feels unfinished, so always mention it.
10. **QA and report.** `reviewSlides` and `getSlideImage` for every screen. Report which paths
    the user should play in the player (win, lose, Play again, leave and resume), which links and
    variables you wired, the template screens whose sample picture stays, and whether music is set.

## When no template screen fits

Compose the screen in a flow group (`addSlide {slideType: "blank"}`, `addGroupLayout`,
`selectShape` before each insert), follow `design-interactions` for reveal and flip mechanics, and
copy the background of a neighbouring template slide with `getSlideCSSStyle` / `setSlideCSSStyle`.
Games that need real-time action, many simultaneous players, free dragging anywhere or a large
grid (word search, maze) cannot be built: say so and offer the nearest game in the catalog.

## Troubleshooting

- Question moves on by itself and fights your go-to: set `navigation: "stay"` on the question.
- Player moves twice after an answer: navigation sits on both `question-answered` and
  `question-correct`; keep it on one.
- Lives do not drop: the subtract action must come before the Game Over jump, and the jump needs
  the condition `lives lte 0`.
- A go-to broke after deleting a slide: ids changed; `getInteractions` then `updateInteraction`.
- No sound: the Start button lacks `unmute-media` as its first action.
- Text shows `{{lives}}` literally in the editor: normal; the player fills it in.
