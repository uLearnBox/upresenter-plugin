# uPresenter plugin for Claude, Codex and ChatGPT

Build and edit interactive presentations, lessons and quizzes in your
[uPresenter](https://upresenter.ai) account from Claude, Codex or ChatGPT.

## What it does

The plugin connects your AI assistant to your uPresenter account through the uPresenter MCP
server at `https://mcp.upresenter.ai/mcp`. With it the assistant can:

- list your presentations and give you a link to open or create one;
- browse the template gallery and build a deck from a template: it picks a design that fits your
  topic, inserts only the layouts your content needs and fills them with real content, so the
  result looks as designed as the template itself. It builds both kinds of presentation: fixed
  slides, and fluid ones (a scrolling, responsive document of cards) from the fluid templates;
- create and edit slides in the presentation you have open in the uPresenter editor: text,
  lists, timelines, infographics, charts, tables, layouts, styling and interactions;
- add graded quizzes in 14 question types, with scoring, feedback and results slides;
- build games (quiz shows, escape rooms) with timers, lives, variables and branching, and
  interactions such as reveals, tabs and hotspots;
- set up the player: completion and success criteria, attempts, resume, buttons and mode;
- review and summarize an existing presentation.

Nine skills teach the assistant how to do this well: `create-presentation` (from a template or
designed from scratch), `create-quiz`, `edit-presentation`, `localize-presentation` (translate a
deck and adapt it to a locale), `review-presentation` (audit a deck and report what to fix),
`build-game`, `design-interactions`, `configure-player-tracking` and `build-fluid-lesson`.
`AGENTS.md` summarizes the workflow for agents that read it (Codex and others).

The tools cannot create animations or transitions, narration, exports (PowerPoint, PDF, SCORM) or
classes and reports: do those in the uPresenter editor.

## Install

### Claude Code

```bash
claude plugin marketplace add uLearnBox/upresenter-plugin
claude plugin install upresenter@upresenter
```

Then run `/mcp`, choose `upresenter` and Authenticate. To add only the server, without the skills:
`claude mcp add --transport http upresenter https://mcp.upresenter.ai/mcp`.

### Claude (web and desktop)

Open the Connectors and Plugins directory, find uPresenter and click Install, then Connect. To add
the server by hand: Customize, Connectors, Add custom connector, and paste
`https://mcp.upresenter.ai/mcp`.

### Codex

```bash
codex plugin marketplace add uLearnBox/upresenter-plugin
```

Then open `/plugins` in Codex, choose uPresenter and install it, and sign in to uPresenter when
asked. The plugin's `.mcp.json` registers the server `upresenter`; to register it yourself, run
`codex mcp add upresenter --url "https://mcp.upresenter.ai/mcp?tools=lean"` and then `codex mcp login upresenter`.

Codex fails when a server lists around 110 tools, so the Codex plugin uses the lean profile (`.codex-mcp.json`, `?tools=lean`): about 17 core tools plus three gateway tools, `searchTools`, `describeTool` and `callTool`. On any client that shows only part of the tools, the assistant finds a missing tool with `searchTools`, reads its schema with `describeTool` and runs it with `callTool`. Claude Code and ChatGPT keep the full list.

### ChatGPT

Open the Plugins directory, find uPresenter and install it, then connect your account.

Connecting opens uPresenter in your browser: sign in if asked, then click Allow. You need a
uPresenter account (a free plan is enough). Editing tools work on the presentation you have open
in an editor tab, so keep that tab open while the assistant works.

## Data and privacy

The plugin sends only what the assistant needs for your request to the uPresenter server:
presentation content and your instructions. It does not read other websites or your files, and
it never sees your password: access is granted with an OAuth sign-in that you can remove at any
time under Connected apps in your uPresenter account settings. See the
[privacy policy](https://upresenter.ai/privacy) and [terms](https://upresenter.ai/terms).

## Support

[upresenter.ai/contact](https://upresenter.ai/contact)

## License

MIT, see [LICENSE](LICENSE).
