# uPresenter plugin: agent instructions

This plugin connects the assistant to the user's uPresenter account through the MCP server
`upresenter` (`https://mcp.upresenter.ai/mcp`, OAuth sign-in). Read this file once, then follow the
skill that matches the task.

## Before any editing: the editor tab must be open

Editing and reading tools (`addSlide`, `setShapeText`, `getPresentationState`, ...) act on a
presentation that the user has open in a uPresenter editor tab. They fail without one.

1. Call `listOpenEditorContexts`. If a presentation is open, use it. If several editors are open,
   pass the `sessionId` of the right one to every tool.
2. If none is open, ask which presentation to use. For an existing one call `listPresentations`
   and give the user its editor link. For a new one call `getNewPresentationLink` (with the
   `templateId` of the chosen template, or for a fluid or blank deck the link it returns for that
   kind) and give the user that link.
3. Ask the user to open the link and keep the tab open, then call `listOpenEditorContexts` again
   until the editor appears. Do not start editing before it does.

`listPresentations`, `listTemplates`, `listThemes` and `getNewPresentationLink` work without an
editor.

## Which skill for which task

| Task | Skill |
|---|---|
| New presentation, lesson, pitch or report (fixed slides or fluid) | `create-presentation` |
| Quiz, test, knowledge check, scoring and results | `create-quiz` |
| Change an existing deck: wording, order, layout, styling, undo | `edit-presentation` |
| Translate or adapt a deck to another language or locale | `localize-presentation` |
| Audit a deck and report what to fix, without changing it | `review-presentation` |
| Games: quiz show, escape room, timers, lives, variables, branching | `build-game` |
| Reveals, tabs, flip cards, hotspots, events, actions, conditions | `design-interactions` |
| Completion and success criteria, attempts, resume, player buttons and mode | `configure-player-tracking` |
| Fluid (responsive, scrolling) lessons | `build-fluid-lesson` |

A request often needs two: build the deck with `create-presentation`, then `create-quiz` or
`build-game`. Finish with `review-presentation` (or the `reviewSlides` and `getSlideImage` check
the skills describe) before telling the user it is done.

## What the tools cannot do

Say so plainly, point the user to the uPresenter editor, and never pretend:

- animations and slide transitions (templates bring their own);
- narration, text to speech, music generation;
- export (PowerPoint, PDF, video, SCORM, xAPI) and publishing or sharing settings;
- classes, assignments, reports and account or billing settings.

AI image generation is not part of the public tool set. Do not convert a fixed deck to fluid with
`setLayoutType`: choose a fluid template instead.

## Ground rules

- Use only the tools the server lists; the tool names in the skills are exact.
- Codex connects to a lean profile (`?tools=lean`): about 17 core tools plus the gateway tools
  `searchTools`, `describeTool` and `callTool`. On any client that shows only part of the tools, when a
  tool named in a skill is missing, find it with `searchTools`, read its schema with `describeTool`
  and run it with `callTool`.
- Prefer a designed template over a blank deck unless the user asks for a custom design.
- Run one tool at a time and read each result before the next call.
- Look at slides (`getSlideImage`) after building: no text spilling out of its card, no leftover
  sample text or empty placeholders.
- Report what was built, what was skipped and what the user still has to do in the editor.
