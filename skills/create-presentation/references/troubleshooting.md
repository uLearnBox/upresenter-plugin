# Troubleshooting

| Symptom | Fix |
|---|---|
| "No presentation is open in an editor tab" | Ask the user to open the presentation and keep the tab open. Do not retry in a loop. |
| "More than one editor tab is open" | `listOpenEditorContexts`, then pass the `sessionId`. |
| `listTemplates` returns nothing | `category` takes only a gallery category: put genre and mood words in `query`. Widen `language`, or search the style (`styles.md`). |
| The wanted style has no template | Offer the nearest template, or borrow a theme and compose (`styles.md`, section 4). |
| The user asked for a fluid presentation and the deck is `slide` | Do not convert it with `setLayoutType`. Pick a fluid template (`listTemplates {layoutType: "fluid"}`) and give the user its `getNewPresentationLink`. |
| `insertTemplateSlides` says the template is a slide (or fluid) template | Template and deck are of different kinds: use a template of the deck's kind, or open a new deck of the kind you need. |
| `insertTemplateSlides` says a slide was not found | Take the `slideId` or `tag` from `getTemplateLayouts` of the same template. |
| Inserted slides look different from the rest | Style and layouts come from different templates. `insertTemplateSlides {templateId, applyThemeFromTemplate: true}` (no slides) and pass the chosen `templateId` on every insert. |
| A slide still shows "Click to add title" or sample figures | A slot was not filled: fill it or `deleteShape` it, then `reviewSlides` again. |
| Text spills out of its card | `reviewSlides` names the shape and the overflow: shorten to the slot's `maxChars` or use a variant with a larger budget. |
| Edit landed on the wrong slide | A shape id was passed without its `slideId`. Inserts (`add*`) go to the current slide, so `goToSlide` first. |
| Added shapes lie on top of each other | The deck is freeform (made from a template). Use the skeleton in `composed-slides.md`. |
| `getTemplateLayouts` says the deck's own template is not available | It left the gallery: insert its layouts by `tag` without `templateId`. |
| "Editor tool execution timed out" | Check the state before retrying so nothing is inserted twice. |
| Bad argument errors | Read the tool's schema and fix the arguments. |
