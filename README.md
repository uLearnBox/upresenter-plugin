# uPresenter plugin for Claude and ChatGPT

Build and edit interactive presentations, lessons and quizzes in your
[uPresenter](https://upresenter.ai) account from Claude or ChatGPT.

## What it does

The plugin connects your AI assistant to your uPresenter account through the uPresenter MCP
server at `https://mcp.upresenter.ai/mcp`. With it the assistant can:

- list your presentations and give you a link to open or create one;
- browse the template gallery and build a deck from a template: it picks a design that fits your
  topic, inserts only the layouts your content needs and fills them with real content, so the
  result looks as designed as the template itself;
- create and edit slides in the presentation you have open in the uPresenter editor: text,
  lists, timelines, infographics, charts, tables, layouts, styling and interactions;
- add graded quizzes in 14 question types, with scoring, feedback and results slides;
- review and summarize an existing presentation.

Three skills teach the assistant how to do this well: `create-presentation`, `create-quiz` and
`edit-presentation`.

## Install

- **Claude:** open the Connectors and Plugins directory, find uPresenter and click Install, then
  Connect. To add the server by hand: Customize, Connectors, Add custom connector, and paste
  `https://mcp.upresenter.ai/mcp`.
- **ChatGPT:** open the Plugins directory, find uPresenter and install it.
- **Claude Code:** `claude mcp add --transport http upresenter https://mcp.upresenter.ai/mcp`,
  then run `/mcp` and choose Authenticate.

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
