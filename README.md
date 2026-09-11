# longrun

An operating manual for running **Claude Code** on long, largely
unsupervised development sessions.

The core artifact is [`claude/LONGRUN-fable-claudecode.md`](claude/LONGRUN-fable-claudecode.md) —
a `CLAUDE.md`-style playbook you drop into a project so the agent can:

- **Survive compaction** by treating disk as memory: `TASKS.md` (working
  queue), `FEATURES.json` (frozen acceptance list), `LOG.md`
  (append-only decision/progress log).
- **Run a disciplined per-task loop**: implement → verify (tests, lint,
  typecheck, build) → commit → log, with rules against skipping
  verification, silently retrying the same failure, or scope-creeping
  mid-run.
- **Route work to the right model** instead of always using the most
  expensive one — a scored table (cost / intelligence / taste) plus
  rules for when to delegate vs. keep judgment calls in the main
  session.
- **Bridge to other model families from inside Claude Code**: GPT via
  OpenAI's official Codex plugin, and Gemini via a community plugin
  that wraps Google's `agy` CLI — so bulk/mechanical work can run on a
  cheaper model while Claude stays the conductor, verifying and
  reviewing the result. Neither bridge plugin is maintained by this
  repo; see their own repos/licenses linked below.
- **Stay reversible**: feature branches, small commits, no destructive
  git operations or unattended secrets/deploys without explicit human
  approval.

Before you adopt it, edit the playbook to fit your setup: the model
roster table and its cost/intelligence/taste scores are the author's
opinion as of a point in time and will go stale as models change, and
a couple of paths (`~/.codex/config.toml`, `~/bin/codex-usage`,
`codex-companion.mjs`) are personal-environment references you'll want
to replace or remove. `TASKS.md`, `FEATURES.json`, and `LOG.md` also
aren't bootstrapped for you — create them in your project before the
first session (`FEATURES.json` is meant to be human-authored and
frozen; the agent only ever flips its `passes` values).

### Variants

Two cut-down variants live in [`generic/`](generic/) for other
models and harnesses. Both are injected at launch (system prompt or
kickoff-prompt head) rather than `@`-imported, and carry a Run intent
block to fill in per run:

- [`LONGRUN-universal.md`](generic/LONGRUN-universal.md) — the same
  state files, verification loop, and retro loop, with no Claude Code
  specifics or plugin mechanics. For any model in any harness.
- [`LONGRUN-lite.md`](generic/LONGRUN-lite.md) — one page for tasks
  that fit one or two sessions: no `FEATURES.json`, no phase
  verifiers.

## Usage

Clone this repo somewhere stable (e.g. `~/.claude/longrun`) and
`@`-import the playbook from your project's `CLAUDE.md`:

```
@~/.claude/longrun/claude/LONGRUN-fable-claudecode.md
```

`~` in an `@`-import expands to your home directory, so any project
that adds this line picks up the playbook without copying it. (Older
Claude Code versions read this kind of import from `CLAUDE.local.md`
instead — check which your installed version uses.) Then install
whichever model bridges you plan to route work to below.

## Installing the model bridges

### Codex plugin (GPT / OpenAI)

Lets Claude Code call GPT models for reviews and delegated
implementation/rescue work via `/codex:*` commands.

```
/plugin marketplace add openai/codex-plugin-cc
/plugin install codex@openai-codex
/reload-plugins
/codex:setup
```

Requires the Codex CLI, installed and authenticated separately:

```bash
npm install -g @openai/codex
codex login
```

Repo: [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc)

### Antigravity plugin (Gemini / `agy`)

Lets Claude Code delegate bulk work, cross-model review, and
Google-grounded research to Gemini via the `agy` CLI, through
`/antigravity:*` commands.

```
/plugin marketplace add yuting0624/antigravity-for-claude-code
/plugin install antigravity@antigravity-for-claude-code
/reload-plugins
/antigravity:setup
```

Requires the Antigravity CLI (`agy`), installed and authenticated
separately — `/antigravity:setup` checks this for you, and `agy
models` should list Gemini models once auth is done:

```bash
curl -fsSL https://antigravity.google/cli/install.sh | bash
agy   # first run opens a browser to sign in and save credentials
```

Supported for headless delegation on macOS, Linux, and WSL (native
Windows/Git Bash is not recommended).

Repo: [yuting0624/antigravity-for-claude-code](https://github.com/yuting0624/antigravity-for-claude-code)
— a community project, not affiliated with Google or Anthropic.

---

Install command names (`plugin@marketplace`) are confirmed against
each repo's `.claude-plugin/marketplace.json` as of this writing, but
`/plugin` subcommands themselves are part of Claude Code and may
change between versions — if a command above doesn't work, check
`/plugin --help` and each plugin's own README for the current syntax.
