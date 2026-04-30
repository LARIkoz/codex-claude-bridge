# Installation and Setup

This guide is written for an AI agent or maintainer setting up the Codex to Claude workflow bridge in an existing repository.

The bridge does not install a runtime daemon. It installs durable instructions and templates so Codex can correctly inspect and follow Claude workflow files when needed.

## First Codex Launch

If this is a fresh Codex install or the first Codex chat in a Claude Code workspace:

1. Use stable paths. Do not rely on the generated chat working directory staying alive.
2. Back up `~/.codex/AGENTS.md` before editing it.
3. Merge, do not replace, the bridge section from `examples/global-codex-AGENTS.md`.
4. Add or update the workspace `AGENTS.md` from `examples/workspace-AGENTS.md`.
5. Leave Claude skills and commands where they are. This bridge does not delete or sync skills.

## Prerequisites

- A repository that uses, or plans to use, Codex.
- Optional but supported Claude Code files:
  - `CLAUDE.md`
  - `.claude/skills/*/SKILL.md`
  - `.claude/commands/*.md`
  - handoff folders such as `.handoff/` or `.claude/handoffs/`

## Step 1. Add Global Codex Bridge Rules

Before editing global Codex instructions, make a backup and inspect the existing file:

```bash
mkdir -p ~/.codex
test -f ~/.codex/AGENTS.md && cp ~/.codex/AGENTS.md ~/.codex/AGENTS.md.bak.$(date +%Y%m%d-%H%M%S)
sed -n '1,220p' ~/.codex/AGENTS.md 2>/dev/null || true
```

Copy the relevant parts of:

```text
examples/global-codex-AGENTS.md
```

into:

```text
~/.codex/AGENTS.md
```

If `~/.codex/AGENTS.md` already exists, merge the `Claude Workflow Bridge` section instead of replacing the file.

Important rules to preserve:

- Claude files are workflow documents, not automatically loaded Codex-native skills.
- Project-local `.claude/skills` and `.claude/commands` outrank global Claude copies.
- Slash aliases should be normalized before exact lookup.
- Degraded research artifacts must not be shipped as final output.
- Secrets must never be printed.

After editing, review the diff before continuing:

```bash
diff -u ~/.codex/AGENTS.md.bak.* ~/.codex/AGENTS.md 2>/dev/null || true
```

## Step 2. Add Workspace Rules

At the root of the workspace or repository, create or update:

```text
AGENTS.md
```

Use:

```text
examples/workspace-AGENTS.md
```

as the starting point.

Replace:

```text
<absolute path to workspace>
```

with the actual workspace path.

Keep this file focused on workspace behavior:

- where project roots are;
- when to read `CLAUDE.md`;
- how to prefer project-local Claude skills and commands;
- handoff reading order;
- safety and secret-handling rules.

## Step 3. Keep Claude Files In Place

Do not blindly copy Claude files into Codex-native skill directories.

Keep:

```text
CLAUDE.md
.claude/skills/*/SKILL.md
.claude/commands/*.md
```

where Claude Code expects them.

Codex should inspect these files through the bridge when a route points there.

## Step 4. Configure Alias Routing

Add only aliases your team actually uses.

Example:

```text
orch, orchestration -> orchestration
review, code review -> review-workflow
research, deep research -> research workflow
simple, light, quick -> tier modifier
```

For multilingual teams, store common aliases in `AGENTS.md` using the native spelling. Normalize whitespace and obvious abbreviations.

Example:

```text
/s орк рев симпл -> orchestrated review, light tier
```

## Step 5. Add Handoff Rules

If your team uses handoffs, standardize the read order:

```text
state.md -> session_report.md -> findings.md -> plan_next_session.md -> todo_next_session.md
```

Tell agents to continue from these files instead of reconstructing context from scratch.

## Step 6. Run Smoke Checks

Use:

```text
docs/smoke-checks.md
```

Recommended checks:

1. stable working directory;
2. slash alias routing;
3. research routing without firing tools;
4. project-local skill and command precedence;
5. handoff resume order;
6. skill discovery precedence.

## Step 7. Review Failure Modes

Read:

```text
docs/known-failure-modes.md
```

Before adding heavy orchestration or research pipelines, make sure your agent knows:

- not to trust degraded `SYNTHESIS.md` files;
- not to let global Claude files shadow project-local files;
- not to bloat global `AGENTS.md`;
- not to frame the setup as one agent replacing the other.

## Minimal Agent Task

If you want to ask an agent to install the bridge, use this prompt:

```text
Install the Codex to Claude workflow bridge in this repository.

Read README.md and INSTALL.md first.
Merge examples/global-codex-AGENTS.md into ~/.codex/AGENTS.md without deleting existing user rules.
Merge examples/workspace-AGENTS.md into the repository AGENTS.md.
Preserve existing CLAUDE.md, .claude/skills, .claude/commands, and handoff files.
Run the smoke checks from docs/smoke-checks.md.
Do not print secrets.
Report changed files and any unresolved conflicts.
```
