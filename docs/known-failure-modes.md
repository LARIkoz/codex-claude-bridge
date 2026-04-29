# Known Failure Modes

These are common problems when bridging Codex and Claude workflow files.

## 1. Project-Local Workflows Routed Too Late

If global Claude skills or commands are checked before project-local `.claude/skills` or `.claude/commands`, a global workflow can shadow a project-specific override.

Mitigation: project-local Claude files should win before global Claude files.

## 2. Deep Research Artifacts Treated As Valid Too Early

A research pipeline can create `SYNTHESIS.md` even when the synthesis is empty, degraded, or contradicted by audit/redteam phases.

Mitigation: require artifact gates:

- degraded tool outputs -> caveat or re-run;
- `AUDIT_VERDICT.md=INVALID` -> do not ship synthesis;
- `REDTEAM_VERDICT.md=REFUTED` -> do not ship synthesis;
- empty/truncated synthesis -> manual re-synthesis or pipeline fix.

## 3. Instruction Bloat

Putting every smoke command and detailed workflow into global `AGENTS.md` makes every session heavier and easier to confuse.

Mitigation: keep global `AGENTS.md` focused on routing and safety. Put runnable smoke checks in a separate doc.

## 4. Replacement Framing

Saying "Codex replaces Claude" creates bad routing behavior. The practical model is coexistence.

Mitigation: describe the setup as a bridge:

```text
Codex owns routing/local execution.
Claude files remain authoritative workflow context when routed there.
```

## 5. Generated Working Directory Disappears

Projectless chats may start in generated folders that later disappear.

Mitigation: instructions and smoke commands should use stable paths such as the user's home, workspace root, or checked-out repo path.

