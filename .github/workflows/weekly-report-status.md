---
name: Weekly Report Status
description: Generates a concise weekly activity report for commits, issues, and pull requests.
on:
  schedule:
    - cron: "0 9 * * 1"
  workflow_dispatch:
permissions:
  contents: read
  issues: read
  pull-requests: read
  copilot-requests: write
engine:
  id: copilot
tools:
  github:
    mode: gh-proxy
    toolsets: [default]
safe-outputs:
  create-issue:
    title-prefix: "[weekly-report] "
    max: 1
---

# Weekly Report Status

Create a concise weekly repository activity report for the previous seven full days ending at workflow start time (UTC).

Scope and requirements:
- Cover commits, issues, and pull requests.
- Use only repository activity in the reporting window.
- Keep the report brief and scannable.
- Always publish exactly one new issue with the report.

Report structure:
- `### Summary`
  - Total commits in the last 7 days
  - Total issues opened and closed in the last 7 days
  - Total pull requests opened, merged, and closed in the last 7 days
- `### Commits`
  - Short bullet list of the most relevant recent commits (sha short id, author, date, message)
- `### Issues`
  - Short bullet list of notable issue activity (opened/closed)
- `### Pull Requests`
  - Short bullet list of notable pull request activity (opened/merged/closed)

No-activity behavior:
- If there is no activity in the window, still create the issue.
- State clearly: `No activity occurred in the previous seven days.`
- Include zero counts for commits, issues, and pull requests.

Output expectations:
- The issue title should succinctly identify the weekly window in UTC.
- Keep body concise and factual.