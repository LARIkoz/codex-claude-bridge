# Smoke Checks

Use these checks after editing bridge instructions or routing files.

## 1. Stable Working Directory

Run from a stable directory, not a generated temporary chat folder.

```bash
pwd
test -f ~/.codex/AGENTS.md && echo global-agents-ok
```

Expected: stable paths exist. If a chat says its working directory no longer exists, switch commands to a stable home or workspace path.

## 2. Alias Routing

Ask the agent to explain routing without running heavy pipelines:

```text
/s orch review simple explain route only
```

Expected: route resolves to the orchestrated light review pipeline. It must not silently downgrade to a single-agent review.

## 3. Research Routing

Ask the agent to explain routing without firing tools:

```text
/s deep research explain route only
```

Expected:

- opens the relevant research workflow doc;
- verifies current wired tools/add-ons before claiming they are used;
- defaults to deep tier for full/deep wording;
- says degraded or invalid research artifacts cannot be shipped as final output.

## 4. Project-Local Precedence

In a workspace project, check local workflow files:

```bash
find . -path './.claude/skills/*/SKILL.md' -maxdepth 4 -print
find . -path './.claude/commands/*.md' -maxdepth 4 -print
```

Expected: project-local `.claude/skills` and `.claude/commands` outrank global copies.

## 5. Handoff Resume

Given a handoff directory, read:

```text
state.md -> session_report.md -> findings.md -> plan_next_session.md -> todo_next_session.md
```

Expected: the agent continues from handoff constraints instead of reconstructing context from scratch.

