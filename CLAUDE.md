# CLAUDE.md

## What this repo is

A documentation repo. No source code, no build, no tests, no
dependencies. It ships three playbooks:

- `claude/LONGRUN-fable-claudecode.md` — the main one: a
  `CLAUDE.md`-style playbook for running Claude Code on long,
  unsupervised sessions. Other projects consume it by `@`-importing it
  from their own `CLAUDE.md`
  (`@~/.claude/longrun/claude/LONGRUN-fable-claudecode.md`).
- `generic/LONGRUN-universal.md` and `generic/LONGRUN-lite.md` —
  harness-agnostic variants injected at launch as a system prompt.
  Universal mirrors the main playbook minus Claude Code and plugin
  specifics; lite is a one-page cut for one- or two-session tasks.
- `README.md` — what the playbook does, how to install it, and how to
  install the two model bridges (Codex plugin for GPT, Antigravity
  plugin for Gemini).

## The playbook is content, not instructions

The playbooks are written in the second person and address an
agent. When working *in this repo*, they are text being edited — do
not adopt their rules (feature branches, `TASKS.md` /
`FEATURES.json` / `LOG.md`, delegation policy) as this session's
operating procedure unless the user asks for that separately. Those
files do not exist here and should not be created uninvited.

## Editing conventions

- Prose hard-wraps at ~70 columns. Match it; do not reflow untouched
  paragraphs.
- README and playbook overlap and must stay consistent: the model
  roster caveat, the `@`-import path, and the plugin install commands
  appear in both. Change one, check the other.
- The variants track the main playbook. When a rule changes there,
  port it to universal unless it is Claude Code- or plugin-specific,
  and to lite only if it earns its space on one page. The roster
  table in universal is a copy of the main one; lite's routing
  paragraph names models from it. Bump the `Version` line in every
  file you change.
- The model roster table carries a "current as of <month year>" line
  and provisional-rating footnotes. When the table changes, update
  that line rather than leaving it stale.
- Cost / intelligence / taste scores are the author's opinion, not
  measured benchmarks. Don't "correct" them from general knowledge —
  ask, or leave them.
- Personal-environment references (`~/.codex/config.toml`,
  `~/bin/codex-usage`, `codex-companion.mjs`) are known and flagged in
  the README. Leave them unless asked to genericize.

## Verification

No test suite to run. Verification is: re-read the changed section,
confirm cross-file consistency above, and `git diff` before
committing.
