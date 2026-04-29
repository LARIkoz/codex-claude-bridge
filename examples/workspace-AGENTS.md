# Workspace Rules

Codex operates in this workspace through a Claude workflow bridge, not as a blind replacement. Treat Claude project files as authoritative workflow context when they exist, while keeping Codex-native routing and local execution rules in `AGENTS.md`.

## Workspace Root

- Main workspace: `<absolute path to workspace>`
- Before working in a project, read its local `CLAUDE.md` if present.
- If a project has `.claude/skills/`, read the relevant `SKILL.md` files before using that workflow.
- If a task points to a handoff directory, continue from the handoff files instead of reconstructing context from scratch.

## Handoff Reading Order

When continuing a Claude handoff, read files in this order when present:

1. `state.md`
2. `session_report.md`
3. `findings.md`
4. `plan_next_session.md`
5. `todo_next_session.md`

Treat explicit handoff constraints, abort conditions, and "do not" notes as active instructions for the resumed session.

## Claude Skills

- Local Claude skills are usable as instruction packs, not as magic runtime state.
- Read only the needed skill files and referenced materials.
- Prefer project-local skills over global copies when both exist.
- Prefer project-local `.claude/commands/*.md` over global command copies when a slash procedure is project-specific.
- Do not assume Claude cloud memory, live Claude session state, or hidden UI context is available unless it exists on disk.

## Research Routing

When the user asks for research, deep research, full research, or similar high-effort investigation:

1. Read the relevant research skill or command document.
2. Treat it as the active research workflow, not as a generic web-search request.
3. For deep/full wording, default to deep tier unless the user asks for a lighter pass.
4. Before claiming a source/tool/add-on was used, verify current script wiring.
5. If research artifacts are degraded, `AUDIT_VERDICT.md` says `INVALID`, or `REDTEAM_VERDICT.md` says `REFUTED`, do not present `SYNTHESIS.md` as final research output.
6. For multi-week, legal, medical, regulatory, manifest-driven, or chain-of-trust work, use the iterative research workflow instead of only a one-shot research pipeline.

## Safety

- Never print raw API keys, tokens, private keys, or secret candidate lines in chat.
- Preserve append-only logs, evidence folders, handoffs, and key archives unless the user explicitly asks for cleanup.
- Keep changes scoped to the active task and avoid unrelated refactors.

