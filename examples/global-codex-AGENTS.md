# Global Codex Rules

When working in a workspace that also contains Claude Code workflow files, use this bridge pattern.

## Claude Workflow Bridge

Codex-native instructions live in `AGENTS.md`. Claude-native artifacts such as `CLAUDE.md`, `.claude/skills/*/SKILL.md`, `.claude/commands/*.md`, and handoff folders are workflow documents that Codex should inspect and follow when routed there.

Do not treat Claude files as automatically loaded Codex-native skills.

Do not rely on a generated chat working directory existing forever. Use stable workspace paths when running commands.

## Workspace Entry

When working under a configured workspace root:

1. Read the workspace `AGENTS.md`.
2. Read the project `CLAUDE.md` when present.
3. Prefer project-local `.claude/skills/*/SKILL.md` and `.claude/commands/*.md` over global Claude copies.
4. Use global `~/.claude/skills/*/SKILL.md` and `~/.claude/commands/*.md` only when no project-local workflow exists.
5. Continue handoff directories from `state.md`, then `session_report.md`, `findings.md`, `plan_next_session.md`, and `todo_next_session.md`.

## Slash Skill Routing

When the user starts a message with `/s <skill-name>`, treat it as an explicit request to use that skill or workflow for the rest of the message.

Before exact skill lookup, normalize obvious aliases, abbreviations, and transliterations. Do not fail just because the user used a shortened command name or another language.

Example aliases:

- `orch`, `orchestration` -> `orchestration`
- `consilium`, `council` -> `consilium`
- `review`, `code review` -> `review-workflow`
- `research`, `deep research`, `full research` -> route through the research workflow by intent
- `simple`, `light`, `quick` -> tier modifier, not a standalone skill

## Orchestration Routing

- `/s orch review simple` means your configured orchestrated review workflow in light/simple mode, not a single-agent review.
- `/s orch review` means your configured standard orchestrated review workflow unless the user explicitly asks for a non-orchestrated review.
- `/s orch research` means the research workflow.
- `/s deep research` means deep-tier research by default. Verify current script/tool wiring before claiming add-ons or sources were used.

If research artifacts are degraded, `AUDIT_VERDICT.md` says `INVALID`, or `REDTEAM_VERDICT.md` says `REFUTED`, do not present `SYNTHESIS.md` as final research output. Report the degraded pipeline status and, only if useful, manually re-synthesize from raw tool outputs with explicit caveats.

## Resolution Order

1. If `<skill-name>` matches an available Codex skill, open that skill's `SKILL.md` and follow it.
2. Otherwise, if a user Codex skill exists, open and follow it.
3. Otherwise, in the active workspace, if project-local `.claude/skills/<skill-name>/SKILL.md` exists, open and follow it as a Claude workflow skill.
4. Otherwise, in the active workspace, if project-local `.claude/commands/<skill-name>.md` exists, open and follow it as the slash procedure.
5. Otherwise, if a global Claude skill exists, open and follow it as a Claude workflow skill.
6. Otherwise, if a global Claude command exists, open and follow it as the slash procedure.

## Secret Safety

Never print raw API keys, tokens, private keys, or secret candidate lines in chat.
