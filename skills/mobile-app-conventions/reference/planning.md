# Planning and project documents

Three documents record a project. Each answers a different question; link between them instead of copying.

| File | Answers | Holds |
| --- | --- | --- |
| `docs/decisions.md` | Why is it like this? | The request, the agreed choices, the alternatives rejected, and the date. A short architecture summary, not the detail. |
| `docs/architecture.md` | How is it built? | Module and feature tree, layer seams, dependency direction, libraries and versions, platform split, and the build, run, lint, and test commands. |
| `docs/plan.md` | What are we building next? | Release or goal checklists, and one entry per feature, bug, or refactor with requirements, sub-steps, and status. |

## Decisions

- One entry per decision, newest last: date, status (proposed, agreed, superseded), the request in a sentence, the choice, and the reason.
- Record the alternatives that were rejected and why, so the next person does not reopen them.
- When a choice changes, add a new entry and mark the old one superseded; do not rewrite history.
- Keep the architecture to a paragraph and link to `docs/architecture.md` for the detail.

## Architecture

- Describe the current shape, not the history: feature and module tree, the seam each layer crosses, dependency direction, and where the composition root sits.
- List libraries with versions and the reason each is present. A dependency without a reason is a candidate for removal.
- For KMP and Flutter, show what is shared and what stays native.
- Record the build, run, lint, and test commands.
- Update it in the same change that moves the code.

## Plan

Organize by release (`## 1.2`, `## Next`) or by goal when the project does not version. Keep completed entries for history; do not delete them.

Each entry:

```markdown
### Add offline workout sync

- **Status:** proposed | agreed | in progress | done
- **Release:** 1.2
- **Goal:** Sync completed workouts when the device is online.
- **Requirements:** what it must do, what must not change, and any constraint.
- **Approach:** the change, in a sentence or two.
- **Alternatives:** what else was considered and why it was rejected.
- **Sub-steps:**
  - [ ] ...
- **Acceptance:** how to verify it is done.
- **Links:** the decisions entry and the architecture section it touches.
```

## Feature and bug intake

Before writing an entry or code:

1. **Use a requirements skill if one is available.** Invoke it for the feature or bug. Otherwise ask the questions below.
2. **Understand the request.** What problem does it solve, who hits it, what happens today, what must not change, and how will success be measured?
3. **Look for a smaller path.** If an existing feature, a configuration change, or a different approach meets the need, propose it and say what it saves. If the user agrees, take that path and record it; otherwise keep gathering requirements and continue with the requested work.
4. **Agree before building.** Put the entry in `docs/plan.md` with requirements, approach, alternatives, sub-steps, and acceptance. A bug entry also records the reproduction and the expected behavior.
5. **Break it down.** Each sub-step is a change that can land and be verified on its own. If a step needs a decision, record it in `docs/decisions.md`.
6. **Work the list.** Update status and check off sub-steps as they land; update `docs/architecture.md` when the shape changes.

Do not start implementation while the goal or acceptance is unclear; ask instead of guessing.
