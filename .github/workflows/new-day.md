---
name: New Day
description: Adds today's UTC daily update entry to index.html when missing and opens one constrained pull request.
on:
  schedule: daily
  workflow_dispatch: null
permissions:
  contents: read
  copilot-requests: write
engine:
  id: copilot
tools:
  edit: null
safe-outputs:
  create-pull-request:
    title-prefix: "[new-day] "
    max: 1
    allowed-files:
      - index.html
  noop: null
---

# New Day

Update only `index.html`.

Goal:
- Use the workflow run's UTC date.
- Add that date to the existing `Daily Updates` navigation.
- Add a matching accessible dialog that confirms the daily update ran.

Hard constraints:
- Follow the existing HTML structure, ID conventions, date wording, and styling patterns already present in `index.html`.
- Do not modify `styles.css`.
- Do not remove or alter existing daily updates.
- Do not duplicate a date, navigation control, or dialog.
- If the UTC date is already present, make no change and call `noop`.

Implementation details:
1. Determine the run date in UTC from the current workflow run time.
2. Format the human-readable date exactly like the existing wording pattern (for example, `1st of August`).
3. Follow the existing ID pattern used by dialogs and their label/description IDs:
   - dialog id pattern like `<month>-<day>-dialog` (lowercase month)
   - heading id pattern like `<month>-<day>-question`
   - paragraph id pattern like `<month>-<day>-answer`
4. Before editing, check whether any of these already exist for the UTC date:
   - the same human-readable date in the Daily Updates navigation
   - the dialog id for that date
   - a trigger button whose `aria-controls` points to that dialog id
5. If any of the above exists, do not add duplicates.
6. If missing, append exactly one new Daily Updates navigation entry with the same markup pattern as existing entries:
   - `button.daily-update-trigger`
   - `type="button"`
   - `aria-haspopup="dialog"`
   - `aria-controls` set to the new dialog id
   - `data-dialog-trigger`
7. Add one matching `<dialog class="daily-update-dialog">` block with:
   - `aria-labelledby` linked to the new question id
   - `aria-describedby` linked to the new answer id
   - header text in the same pattern: `Daily Update / <formatted date>`
   - accessible close button pattern matching existing structure
8. In the dialog body, clearly confirm that the daily update ran for that UTC date.
9. Preserve indentation, element order, and existing script behavior.

Output behavior:
- Create a pull request only when `index.html` changed.
- If no change is needed, use `noop` with a short explanation that the UTC date already exists.