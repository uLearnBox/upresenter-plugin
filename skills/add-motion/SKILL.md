---
name: add-motion
description: "Add motion to a uPresenter presentation: slide transitions (fade, push, flip, wipe, cover and 30 more), entrance, exit and emphasis animations on shapes with delays and staggering, looping or autoplaying media, auto-advancing and self-running decks, and the player look. Picks a transition style to fit the deck genre and keeps motion subtle and consistent. Use when the user asks for animations, transitions, effects, a fade between slides, text that flies in, a staggered reveal, a kiosk or self-running deck, or a livelier or calmer deck in uPresenter, in any language (for example thêm hiệu ứng chuyển slide, thêm animation cho tiêu đề, hiện lần lượt từng ý, añadir transiciones, 添加切换效果, ajouter des animations)."
---

# Add motion in uPresenter

Motion is a slide transition (how the deck moves onto a slide) and shape animations (a shape
appears, leaves or draws attention). The tools set them; they cannot play the deck, and
`getSlideImage` shows the end state, not the movement. Say that you could not watch it.

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
- The editor tab must be open: `listOpenEditorContexts`; if none, `getNewPresentationLink` and ask
  the user to open it and keep the tab open, then check again. With several tabs pass the right
  `sessionId`.

## Tools

- `setSlideTransition {slideId, transition, direction?, duration?, applyToAll?}`: `none` removes
  it; `applyToAll: true` gives every slide the same one; `duration` 0.1 to 10 s (default 0.5).
  Transitions: fade, push, flip, wipe, clock, bars, shape, flash, gallery, cover, box, switch,
  ferris-wheel, zoom, conveyor, windmill, zigzag, wave, spiral, rubik, rotate, morph, galaxy,
  bounce, wind-sweep, quantum-tunnel, arrow-shot, tornado, matrix-glitch, star, prism, rain,
  paper-fold, ripple, split, elastic, swing. The tool description lists the direction codes per
  transition; those without one ignore `direction`.
- `setShapeAnimation {slideId, shapeId, effect, direction?, delay?, duration?, repeat?,
  trigger?}`: one entrance, one emphasis and one exit per shape; setting an effect replaces the one
  of the same kind; `effect: "none"` clears it (with `trigger` to clear one kind). `delay` is
  seconds from the slide opening (for emphasis, after the entrance ends); `repeat` -1 loops
  forever. `trigger: hover | click` plays it when the learner points at or clicks the shape.
  Entrances: fade-in, fly-in, zoom-in, grow-n-turn, float-in, swivel-in, center-revolve-in,
  stretch-in, compress, credits-in, rise-up, pinwheel-in, basic-float-in, boomerang-in. Exits are
  the `-out` forms plus collapse, sink-down, shrink-n-turn. Emphasis: spin, teeter, pulse, grow,
  shrink, blink, complementary-color.
- Related: `setSlideAutoAdvance {slideId, seconds}`, `setMediaPlayback {slideId, shapeId, loop?,
  autoplay?, controls?}`, `setBackgroundMusic`, `setPlayerAppearance {skin?, padding?}`.
- Errors you will see and how to fix them: an effect that does not match its trigger (`fade-in`
  as `exit`), a direction outside the effect's list, `duration` or `delay` out of range. Read the
  message and pick a valid value.
- If a tool named here is not in your tool list, find it with `searchTools`, read its schema with
  `describeTool` and run it with `callTool`.

## Workflow

1. **Ask only what matters**: how lively (calm, balanced, lively), and whether the deck is
   presented live, self-running or read on a phone. Otherwise decide from the genre below.
2. **Check the deck**: `getPresentationInfo` for the layout type. A fluid deck is a scrolling card
   document: authored slide transitions do not animate a tall card, so leave transitions off and
   use at most a gentle entrance on a few shapes.
3. **One deck-wide transition first**: `setSlideTransition {transition, applyToAll: true}` with
   the genre default. Then override only where the story changes: section slides and the ending.
4. **Animate sparingly**: entrances on the title and the key points of a few slides, not
   everywhere. Stagger a list or group by raising `delay` in steps of 0.15 to 0.3 s, at most 3
   animated shapes per slide. Use one entrance style per deck (for example `fade-in` or
   `rise-up`) and an emphasis such as `pulse` only on a call to action. Never animate the text a
   learner must read quickly, and keep durations between 0.4 and 0.8 s.
5. **Self-running decks and kiosks**: `setSlideAutoAdvance` per slide (long enough to read),
   `setBackgroundMusic` with `loop`, `setMediaPlayback {loop: true}` for ambient audio,
   `setPlayerAppearance` for the player skin. Sound autoplay is blocked until the learner has
   interacted: the first button needs an `unmute-media` action (see `design-interactions`).
6. **Check**: `getSlideInfo` or `getPresentationState` to confirm what was set, `getSlideImage`
   for the end state, and report the motion in words ("title fades in after 0.3 s") so the user
   knows what to look for in the player.

## Style by deck genre

| Genre | Transition (duration) | Shape motion |
| --- | --- | --- |
| Pitch, corporate, report | `fade` or `push` (0.4 to 0.6 s) | `fade-in` on titles, staggered key points |
| Academic, thesis | `fade` or none | almost none; reveal one step at a time with interactions instead |
| Lesson, classroom, kids | `bounce`, `flip` or `box` on 2 or 3 slides, `fade` elsewhere | `zoom-in` or `float-in` on a mascot or key term, staggered lists |
| Luxury, editorial | slow `fade` (0.8 to 1 s) | `fade-in`, very slow |
| Tech, product launch | `matrix-glitch`, `galaxy` or `push` on key slides | `fly-in` for the product, `pulse` on the call to action |
| Brutalist, bold | fast `wipe` or `cover` (0.3 s) | `fly-in` from a side, no easing flourishes |
| Quiz, game | `fade` only | none on question slides; `blink` or `pulse` on a timer or lives, confetti belongs to interactions |

## Limits

- No tool plays or previews motion, edits easing, or builds a custom path animation.
- Templates already bring their own animations and transitions: read what is set with
  `getSlideInfo` before replacing it, and do not overwrite a template's motion without a reason.
- Export and sharing are editor tasks; narration is `addNarration` when listed.
