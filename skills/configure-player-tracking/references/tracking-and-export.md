# Tracking, export and Reports: what the user does

Contents: 1 The settings, 2 Exporting from the editor, 3 Known gaps, 4 Reading Reports,
5 What no tool can do

## 1. The settings

| Tool | Values | Meaning |
|---|---|---|
| `setCompletionCriteria` | `condition` sc or sv, `value`, `unit` p or a | when the lesson counts as completed |
| `setSuccessCriteria` | same | when the learner has passed |
| `setMaxAttempts` | `attempts` (0 = unlimited) | attempt limit per learner |
| `setResumeOption` | `ask_resume`, `always_resume`, `always_restart` | what happens when a learner reopens |
| `setPlayerMode` | `presentation`, `quiz`, `game` | preset of toolbar buttons and navigation |
| `setPlayerButton` | `buttonId`, `visible` | one toolbar button |
| `setPlayerToolbarVisibility` | `visible` | the whole toolbar |
| `setNavigationLock` | `locked` | free navigation (keys, swipe, slide list) |
| `setBackgroundMusic` | see the game skill | one looping track |
| `getPresentationSettings` | none | read all of the above |

Examples: a 70 percent pass mark: `setSuccessCriteria {condition: "sc", value: 70, unit: "p"}`.
Completion at ten viewed slides: `setCompletionCriteria {condition: "sv", value: 10, unit: "a"}`.
A single try, restart each time: `setMaxAttempts {attempts: 1}`, `setResumeOption {option:
"always_restart"}`.

## 2. Exporting from the editor

Export is done by the user. No tool exports. In the editor, open the **Download** button in the
top bar. The menu groups the options:

- Presentation source;
- Documents: PDF Document, PowerPoint (.pptx) where available;
- Images: PNG Images, JPEG Images;
- eLearning packages: **HTML5 Package**, **xAPI Package**, **SCORM 1.2**, **SCORM 2004**.

The groups beyond the source file carry a **Pro** badge when the account is not on a Pro plan; a
locked option opens the upgrade prompt instead of exporting. Tell the user which plan is needed
rather than promising an export.

Choosing a standard:

- **SCORM 1.2**: widest LMS compatibility; completion and pass share one status field.
- **SCORM 2004**: richer tracking; completion and success are separate.
- **xAPI**: statements sent to a Learning Record Store; use when the LMS or LRS speaks xAPI.
- **HTML5**: a self-contained package that runs in any browser, no LMS tracking.

Before exporting: success and completion criteria set, graded questions with correct answers,
attempts and resume chosen, the deck's language set (it drives player texts). Upload the zip to the
LMS and test one attempt end to end.

## 3. Known gaps (from the repository's SCORM and xAPI audit)

`SCORM_XAPI_REPORT_AUDIT.md` (2026-06-15, rechecked 2026-09-10) lists these. Status below also
checked against the current player code.

- SCORM score and completion: fixed. The score sent to the LMS is the real score, and
  completion and pass or fail are sent on finish.
- SCORM 2004 keeps completion and success separate. Fixed in the live path.
- SCORM 1.2 interaction responses use the 2004 and xAPI delimiters instead of the 1.2 ones, so some
  LMSs reject or ignore interaction detail (open). Overall score and status are not affected.
- A deck with no graded question reports score 0 to a SCORM 1.2 LMS (open); some LMSs read 0 as
  failed. Advise at least one graded question, or slide-viewed criteria and an LMS that ignores
  score.
- Titles with `&`, `<`, `>` or quotes in the package manifest: escaping not verified; advise a
  plain title for packages.
- xAPI: score, completion and pass are sent on finish (final verbs passed, failed, completed or
  exited). The audit notes `initialized` is never sent and some report logic depends on it; this
  affects platform Reports, not the package itself (not re-verified).
- SCORM data goes to the customer's LMS only. It does not feed uPresenter Reports.

## 4. Reading Reports

Reports in the dashboard read learner statements from the platform's own Learning Record Store
for presentations taken **inside uPresenter** (the hosted player, including classes). The user
opens **Reports** in the dashboard sidebar; classes are under **Classes**. Class creation,
assignment and report access are in the app only: no tool does them.

- A report that says "No data" has several causes: nobody has taken the lesson yet, the learner
  took a preview rather than a real attempt (preview attempts are filtered out), the attempt was
  voided, or the Learning Record Store is unreachable for that environment. If the lesson has
  attempts and the report stays empty, the user should contact support or the administrator
  rather than re-publishing.
- Best attempt per learner, voided and preview attempts excluded, and slides a learner never
  viewed are rules of the report, not settings you can change.
- SCORM and HTML5 exports do not appear in Reports.

## 5. What no tool can do

No MCP tool exists for: creating or assigning classes, reading or exporting reports, exporting
packages or files, slide animations, slide transitions, text-to-speech narration. Be explicit,
give the user the editor or dashboard steps, and never claim these were done.
