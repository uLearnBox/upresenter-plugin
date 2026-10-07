---
name: configure-player-tracking
description: "Configure how the uPresenter player tracks and presents a lesson: completion versus success (pass) criteria, maximum attempts, resume behaviour, toolbar and player buttons, Presentation, Quiz or Game player mode, navigation lock; then explain how the user exports SCORM 1.2, SCORM 2004, xAPI or an HTML5 package from the editor (Pro) and how to read Reports. Use when the user asks about pass mark, passing score, completion, attempts, resume, hide the next/previous button, quiz mode, SCORM, xAPI, LMS, reports or tracking learners in uPresenter, in any language (for example đặt điểm đạt, số lần làm bài, xuất SCORM, xem báo cáo học viên, configurar nota de aprobación, exportar SCORM, 设置及格分数, 导出SCORM)."
---

# Configure player tracking in uPresenter

The settings below are stored in the presentation and used by the player when a learner takes it.
This skill sets them with tools and explains the parts that only the user can do in the app.

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
- Call tools one at a time and use ids that tool results returned.
- The editor tab must be open: `listOpenEditorContexts`; if none, `listPresentations` for an
  existing deck or `getNewPresentationLink`, and ask the user to open it and keep the tab open,
  then check again. With several tabs pass the right `sessionId`.
- Read before writing: `getPresentationSettings` shows the current criteria, attempts, resume
  option and player settings; report old and new values.
- QA loop when the deck itself changed: `reviewSlides`, then `getSlideImage`.
- Fluid decks (scrolling cards) come only from fluid templates; never convert with `setLayoutType`.
  Tracking settings work the same in both kinds.
- **There are no MCP tools for** classes, assigning, reports, exporting packages (SCORM, xAPI,
  HTML5, PDF, PowerPoint, images). Say so
  plainly and give the user the steps in the editor or dashboard (`references/tracking-and-export.md`).
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

1. **Ask what the course needs** (only what is missing): is it assessed (pass mark), is it
   delivered through an LMS (which standard), how many attempts, may learners resume.
2. **Check the deck can be scored.** `getQuestionsInfo {scope: "all-slides", includeAnswers: true}`:
   score-based criteria need graded questions with a correct answer. A deck with no graded
   question has no score: use slide-viewed criteria. If the user needs questions, use a quiz
   skill if installed, else `addQuestion` with `getSupportedQuestionTypes`.
3. **Criteria.** `setCompletionCriteria {condition, value, unit}` and `setSuccessCriteria
   {condition, value, unit}`: `condition` `sc` (score) or `sv` (slides viewed); `unit` `p`
   (percent) or `a` (absolute points or slide count). Completion means "finished the lesson"
   (usually all slides viewed); success means "passed" (usually a score mark). Any subset of
   the three fields may be set.
4. **Attempts and resume.** `setMaxAttempts {attempts}` (0 means unlimited). `setResumeOption
   {option}`: `ask_resume`, `always_resume` or `always_restart`.
5. **Player.** `setPlayerMode {mode}`: `presentation` (every button, free navigation), `quiz`
   (keeps navigation, hides autoplay and share), `game` (see the `build-game` skill if installed:
   hides panel, previous and next, review, early finish, generic submit, autoplay, progress bar,
   share and slide number, and locks navigation). Fine-tune with `setPlayerButton {buttonId,
   visible}` (ids below), `setPlayerToolbarVisibility`, `setNavigationLock`.
6. **Confirm** with `getPresentationSettings`, then explain the export and the Reports in plain
   steps using `references/tracking-and-export.md`, including the limits.

## Player button ids (setPlayerButton)

`ssb` slides panel, `spb` progress bar, `spsb` previous, `snsb` next, `srb` review, `strb` try
again, `sfnb` early finish, `ssmb` submit, `sfsb` fullscreen, `sab` autoplay, `sshb` share, `ssn`
slide number ("3 / 15"). A slide that has its own button wired to `submit` hides the generic
Submit button automatically. Hiding the whole toolbar hides every button as well.

## What the settings mean (verified in the player)

- New presentations start with success = score, 80 percent; completion = slides viewed, 100
  percent; unlimited attempts; resume = ask.
- Score percent is earned score over the maximum graded score; with no graded points the
  percentage test counts as met. Slides-viewed absolute values are capped at the slide count.
- At the end the player sends one final result: passed or failed when the success test is
  evaluated, otherwise completed when the completion test is met, otherwise exited.
- `$passed` and `$completed` are usable in conditions once the criteria exist.

## Troubleshooting

- "Learners pass without answering": success is slide-viewed, or there are no graded points; set
  `sc` and check the questions are graded.
- "Never completes": completion needs all slides viewed by default; with free navigation locked
  make sure every slide is reachable.
- Resume does nothing: `always_restart` is set, or the export is run in a way that gives the
  player no saved state; the LMS decides for SCORM.
- Reports show "No data": see `references/tracking-and-export.md`, section 4.
