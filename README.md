# Codex to Claude Workflow Bridge

A practical pattern for teams that use both OpenAI Codex and Claude Code in the same repository.

The goal is not to make one agent pretend to be the other. The goal is to keep each tool's native instruction system intact, then add a small bridge layer so routing, slash aliases, skills, commands, and handoffs are predictable.

## Why This Exists

Many repositories already have Claude Code files:

- `CLAUDE.md`
- `.claude/skills/*/SKILL.md`
- `.claude/commands/*.md`
- handoff folders or session notes

Codex uses its own native instruction layer:

- `AGENTS.md`
- Codex skills
- local shell/tool execution

If you copy everything blindly from Claude to Codex, instructions drift and precedence becomes unclear. If you ignore Claude files, you lose useful workflow knowledge. This bridge pattern gives each file a clear job.

## Core Idea

Use `AGENTS.md` as the Codex-native routing layer.

Use Claude files as workflow documents that Codex can inspect and follow when a route points there.

```text
Codex
  AGENTS.md
    -> routing, local procedure, safety rules, alias normalization
    -> decides when to inspect Claude workflow docs

Claude Code
  CLAUDE.md
    -> project behavior, architecture notes, standing policies
  .claude/skills/*/SKILL.md
    -> reusable workflow instructions
  .claude/commands/*.md
    -> slash command procedures

Shared
  handoff/
    -> durable session state between agents
```

## Recommended File Responsibilities

### `AGENTS.md`

Put Codex-facing, executable, procedural instructions here:

- how to bootstrap the repo;
- which tests or linters to run;
- how slash aliases route;
- how to read Claude files;
- local safety rules;
- precedence rules for project-local vs global workflows.

### `CLAUDE.md`

Put Claude-facing project context here:

- project behavior expectations;
- architecture notes;
- coding conventions;
- standing constraints;
- things Claude should remember during its own sessions.

Keep it concise. Long command tables and operational recipes usually belong in `AGENTS.md`, repo docs, or skills.

### `.claude/skills`

Use for established multi-step workflows, especially when they already include helper scripts, MCP assumptions, research procedures, or handoff conventions.

Codex should read these files as workflow instructions, not as automatically loaded Codex-native skills.

### `.claude/commands`

Use for slash-command procedures. If both project-local and global commands exist, prefer the project-local command.

### Handoff Files

Use handoff files for continuity between agents and sessions. A simple read order works well:

```text
state.md -> session_report.md -> findings.md -> plan_next_session.md -> todo_next_session.md
```

## Slash Alias Routing

The bridge can normalize natural aliases before exact skill lookup.

Example:

```text
/s orch review simple
/s орк рев симпл
```

Both can map to:

```bash
synthesize_consilium.sh <dir> --mode review --tier light
```

The important rule is that modifiers such as `simple`, `light`, or `quick` are not standalone skills. They modify the selected workflow.

## Research Failure Rule

For research pipelines, do not treat a generated synthesis as valid just because a file exists.

If any of these are true:

- tool outputs are degraded;
- `AUDIT_VERDICT.md` says `INVALID`;
- `REDTEAM_VERDICT.md` says `REFUTED`;
- the synthesis is empty, truncated, or only a degradation marker;

then report the pipeline status and either re-run the pipeline or manually re-synthesize from raw outputs with explicit caveats.

## Included Templates

- [`INSTALL.md`](INSTALL.md)
- [`examples/global-codex-AGENTS.md`](examples/global-codex-AGENTS.md)
- [`examples/workspace-AGENTS.md`](examples/workspace-AGENTS.md)
- [`docs/smoke-checks.md`](docs/smoke-checks.md)
- [`docs/known-failure-modes.md`](docs/known-failure-modes.md)

## Sources

- OpenAI Codex `AGENTS.md` guide: https://developers.openai.com/codex/guides/agents-md
- OpenAI Codex Skills guide: https://developers.openai.com/codex/skills
- Claude Code memory docs: https://docs.anthropic.com/en/docs/claude-code/memory
- Claude Code skills docs: https://docs.anthropic.com/en/docs/claude-code/skills
