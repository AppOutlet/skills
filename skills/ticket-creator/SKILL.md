---
name: ticket-creator
description: Create, review, refine, de-duplicate, and break down GitHub issue tickets for bugs, tasks, spikes, features, backlog items, and project planning. Use whenever the user asks to draft, create, review, improve, or plan tickets/GitHub issues, shares a GH issue for feedback, asks to break down a feature, or provides a Sentry issue/link that should become a ticket.
compatibility: Requires GitHub CLI (`gh`), network access to GitHub issues, and a URL opener after issue creation.
---

# Ticket Creator

Turn user requests into actionable GitHub issues through investigation, draft review, and explicit approval.

## Modes

- New ticket: follow the full workflow below.
- Existing ticket review: use [Review Existing Ticket](#review-existing-ticket).
- Feature planning or breakdown: use [Feature Breakdown](#feature-breakdown).

## Preflight

1. Run `gh --version` before ticket work.
2. If missing, stop and tell the user to install GitHub CLI, e.g. `brew install gh`, `winget install GitHub.cli`, or <https://cli.github.com/>.
3. If `gh` issue commands fail for auth, stop and ask the user to run `gh auth login`.

Do not draft or create a ticket until `gh` is available.

## New Ticket Workflow

1. If the request includes a Sentry ID/link, fetch Sentry context first.
2. Confirm you can answer: what problem is solved, who benefits, and what done looks like. Ask focused questions if any answer is unclear.
3. Infer ticket type: `bug`, `task`, `spike`, or `feature`. Ask one concise question if ambiguous.
4. Search existing GitHub issues for duplicates.
5. Investigate relevant code/files/config locally before drafting.
6. Open only the chosen template and preserve its heading structure and formatting (Bold, checklist, indentation, line breaks, etc.):
   - Bug: `assets/bug.md`
   - Task: `assets/task.md`
   - Spike: `assets/spike.md`
   - Feature: `assets/feature.md`
7. Draft title and body, self-review, then show the draft to the user.
8. Iterate until the user explicitly approves creation (`approved`, `create it`, `yes, create the issue`, or equivalent).
9. After approval, create the issue using a temporary body file and open the resulting URL.

Never skip duplicate check, codebase investigation, self-review, or explicit approval.

## Type Rules

- `bug`: broken behavior, regression, crash, incorrect result, inconsistency.
- `task`: maintenance, cleanup, upgrade, migration, docs, routine implementation.
- `spike`: investigation, research, unknown scope. Title starts with `Spike: `.
- `feature`: new user-facing or system-facing capability. Acceptance criteria use `GIVEN` / `WHEN` / `THEN` checklist items, with those words stacked vertically so `GIVEN`, `WHEN`, and `THEN` appear directly below each other for easier scanning.

## Sentry Context

If the request contains a Sentry short ID (`DISCORKIE-FT`), numeric ID (`111242862`), or issue URL:

1. Run `sentry auth status`. If missing or unauthenticated, stop and tell the user.
2. Extract the issue ID from the short ID, numeric ID, or URL path after `issues`.
3. Run `sentry issue view <issue_id>`.
4. Run `sentry event list <issue_id> --json`, take an event `id`, then run `sentry event view <event_id>`.
5. Use the findings in the bug draft, especially description, reproduction, and additional information.

## Duplicate Check

Use `gh issue list --state all --limit 50 --search "<keywords>"` with keywords from the request and likely title/theme.

If likely duplicates exist, show issue numbers/titles, explain overlap, and ask whether to stop, update an existing issue, or continue. Stop if the user chooses not to continue.

## Codebase Investigation

Before drafting, search/read relevant files, modules, screens, endpoints, config, classes, or docs. Extract useful implementation context, constraints, suspected causes, related files, and current behavior. Reflect this in the draft.

## Draft Rules

- Present inferred type, duplicate findings, codebase findings, proposed title, and proposed body.
- Put the body in a fenced Markdown block for review.
- Do not create the issue before approval.
- Do not include the ticket title anywhere in the body.
- Use the selected template exactly; remove optional sections only when useless.
- Treat template formatting as part of the template: preserve heading levels, blank lines, bold text, checklist markers, indentation, and any intentional alignment or line breaks. Do not reflow or restyle the template.
- Keep titles concise and outcome-oriented.
- `bug`: include concrete repro, expected/actual behavior, and technical findings.
- `task`: acceptance criteria describe observable outcomes, not vague activity.
- `spike`: `### Current Knowledge` captures investigation findings.
- `feature`: description covers problem, user benefit, technical/design context, and edge cases.
- `feature`: in each acceptance-criteria checklist item, keep `GIVEN`, `WHEN`, and `THEN` on separate lines and vertically aligned; do not collapse them into a single paragraph or sentence.

## Self-Review

Silently fix the draft before showing it:

- Title states the outcome without opening the issue.
- Description explains why, scope, and context for someone new.
- Acceptance criteria are concrete and QA-verifiable.
- Remove vague terms like "properly", "correctly", and "as expected".
- Surface hidden scope/dependencies such as APIs, migrations, or platform coverage.
- Consider error, empty, loading, permission, analytics, and compatibility cases.

## Issue Creation

Only after explicit approval:

1. Slugify the title: lowercase, replace consecutive non-alphanumeric chars with `_`, trim `_`. Example: `Fix image parsing crash` -> `fix_image_parsing_crash`.
2. Use a temp issue-body directory in a temporary folder outside the repo. Create it if needed.
3. Generate a local timestamp in `YYYYMMDDHHMMSS` format.
4. Save the approved body as `<temp-dir>/<timestamp>-<slug>.md`. File content is raw Markdown only, without code-fence delimiters.
5. Run `gh issue create --title "..." --body-file <temp-dir>/<timestamp>-<slug>.md` and capture the printed URL.
6. Open the URL: `open <url>` on macOS, `xdg-open <url>` on Linux, `start <url>` on Windows.

Never use inline `--body` for multiline content; use `--body-file` to avoid shell escaping bugs.

After creation, report the final title, temporary body path, issue URL, and browser-open confirmation.

## Review Existing Ticket

When the user shares a ticket by paste, link, or file:

1. Read it as a developer with no prior context and identify assumptions, missing scope, contradictions, vague wording, and unverifiable acceptance criteria.
2. Provide an improved complete ticket in a fenced Markdown block.
3. Then provide feedback grouped as `Blockers`, `Improvements`, and `Suggestions`, explaining why each point matters.
4. Ask whether to create the improved version as a new issue or update the existing one.

## Feature Breakdown

When the user describes a feature/goal rather than one ticket:

1. Clarify user, problem, and minimum viable scope if unclear.
2. Think through backend, frontend platforms, UX/design, data/migrations, edge cases, permissions, loading/error/empty states, analytics, and dependencies.
3. Propose ticket titles grouped by area, with sequencing/dependencies and explicit scope boundaries.
4. Iterate with the user, then offer to create tickets one by one through the standard workflow.

Keep tickets small. Split work that mixes concerns, such as endpoint plus UI, or crosses multiple platforms.

## Boundaries

- Do not write code snippets in tickets; describe outcomes and constraints.
- Do not make architectural decisions; surface questions for the team.
- Do not assign people unless asked.
- Do not estimate with false precision; suggest a range or spike when uncertain.
