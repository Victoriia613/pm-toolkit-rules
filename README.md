# pm-toolkit-rules

This is a **public** repo holding the mutable rules consumed by the `pm-toolkit` Cowork plugin's skills: quality standards, formatting/house-style rules, and per-project overrides. It is deliberately separate from the plugin package so it can be updated — by anyone with write access — without reinstalling the plugin on every PM's machine.

## Why this is separate from the plugin

The plugin (`pm-toolkit`) contains **stable orchestration logic**: how to fetch data, what order to do things in, what tools to call. That changes rarely.

This repo contains **judgment calls and standards that change often**: banned words, templates, formatting thresholds, brand details, per-project overrides, classification heuristics. Centralizing these here means:
- One edit here updates the behavior for every PM who has the plugin installed (next time their skill runs, it pulls latest).
- No `.plugin` repackaging/reinstall needed for a rules change.
- Project-specific knowledge (like SGD B2B's brand colors) doesn't have to live inside generic plugin code.

## Structure

```
jira/
  task-quality-principles.md      — Scandiweb's 8 good-task principles, banned words, templates
weekly-report/
  formatting-rules.md             — org-wide formatting/content rules, blocker status calc, inclusion rules
  project-overrides/
    sgd-b2b.md                    — SGD B2B-specific defaults (template, brand colors, lane mapping)
    <add-new-project>.md          — add one file per project that needs overrides
inbox-sync/
  classification-rules.md         — what counts as an actionable email vs. noise
```

## How skills consume this repo

Each skill in the plugin clones this repo at the start of a run (read-only) and reads the specific file(s) it needs — see each skill's `SKILL.md` for which files. Because the repo is public, no credentials are required. The fetch pattern used by every skill:

```bash
REPO="${PM_TOOLKIT_RULES_REPO:-https://github.com/Victoriia613/pm-toolkit-rules.git}"
rm -rf /tmp/pm-toolkit-rules
git clone --depth 1 "$REPO" /tmp/pm-toolkit-rules -q
```

`PM_TOOLKIT_RULES_REPO` is an optional env var — only needed if you fork this repo and want skills to pull from your fork instead of the default URL baked into the plugin. Most PMs installing the plugin don't need to set anything.

## Adding a new project override (weekly-report)

1. Copy `weekly-report/project-overrides/sgd-b2b.md` as a template.
2. Fill in the project's template path/name, brand colors, font, roadmap lane → slide mapping, and any contact-slide details.
3. Name the file after the project's Jira/PMAI key in lowercase-kebab-case (e.g. `acme-replatform.md`).
4. Commit and push — no plugin changes needed.

## Editing org-wide rules

`jira/task-quality-principles.md`, `weekly-report/formatting-rules.md`, and `inbox-sync/classification-rules.md` apply to *all* projects and all PMs using the plugin. Edit with that blast radius in mind — these are house standards, not one project's preference.
