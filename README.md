# Mobile app development skills

Agent skills that keep mobile code consistent across Android, iOS, Flutter, and Kotlin Multiplatform.

Skills here are user-invoked: the body loads only when you name one. Claude Code and Codex enforce explicit invocation; Gemini CLI, Antigravity, and OpenCode can also activate a skill from its description, so name it when you want to be sure. Each skill is a folder with a `SKILL.md` and reference files the agent reaches only when the task calls for them.

## Skills

### [`mobile-app-conventions`](skills/mobile-app-conventions/SKILL.md)

The shared reference: project structure, layering, state ownership, navigation, dependency injection, persistence, errors, formatting, lint, testing, accessibility, and project documents. A per-stack reference adds idiom and gotchas, and a [review checklist](skills/mobile-app-conventions/reference/review-checklist.md) turns the rules into pass/fail items. A [planning reference](skills/mobile-app-conventions/reference/planning.md) covers `docs/decisions.md`, `docs/architecture.md`, and `docs/plan.md`, plus the feature and bug intake.

| Stack | Reference |
| --- | --- |
| Android (Kotlin, Compose) | [`reference/android.md`](skills/mobile-app-conventions/reference/android.md) |
| iOS (Swift, SwiftUI) | [`reference/ios.md`](skills/mobile-app-conventions/reference/ios.md) |
| Flutter (Dart) | [`reference/flutter.md`](skills/mobile-app-conventions/reference/flutter.md) |
| Kotlin Multiplatform | [`reference/kmp.md`](skills/mobile-app-conventions/reference/kmp.md) |

**Examples**

- *Review:* "Review this checkout screen for seam violations and tell me what to move."
- *Plan a feature:* "Add offline sync to the workout app. Write the plan entry and requirements first."
- *Decide:* "We are adding offline support. Which layer owns the cache, and what belongs in shared code?"
- *Configure:* "Set up ktlint, detekt, and a CI gate for this Android module."
- *Check a shared change:* "Is this file safe to move into `commonMain`?"

### [`mobile-app-scaffold`](skills/mobile-app-scaffold/SKILL.md)

A one-time setup recipe. It reads the request, infers the stack, scope, dependencies, and defaults, and asks only for what is missing. It records the request and agreed choices in `docs/decisions.md`, generates the project with the official CLI or wizard, and applies the scaffold steps: folder structure, dependency injection, lint and CI, a navigation skeleton, a test harness, the accessibility baseline, the README and license, the detailed `docs/architecture.md`, a seeded `docs/plan.md`, and optional `AGENTS.md` instructions.

**Examples**

- *Description-first, gaps asked:* "Create a new app for workout tracking which works offline and uses camera and object detection to recognize my workouts and track them automatically."
- *Framework named:* "Scaffold a Flutter app for tracking expenses. Bundle ID `com.acme.expenses`."
- *Agent support:* "Scaffold a Flutter app for tracking expenses and set up agent instructions for Claude."
- *Overrides:* "Scaffold a KMP app with Compose Multiplatform, Koin, SQLDelight, and strict lint."
- *Vanilla:* "Scaffold an iOS starter in Xcode. Just the template, no extras."
- *Bare prompt:* "Scaffold a field-notes app; suggest names, a license, and a README."

## Install

The skills are plain folders. Install them globally so every project sees them, or into a project's `.agents/skills/` or `.claude/skills/` to share them with a team.

### One command

The [`skills` CLI](https://www.skills.sh/) knows each agent's directory. It installs both skills for the agents you name:

```sh
npx skills add maranix/mobile-app-development-skills -g -y \
  -a claude-code -a codex -a gemini-cli -a antigravity -a opencode -a cursor -a github-copilot
```

Requires Node.js 22.20 or later. Drop `-g` for a project install, add `--copy` if symlinks are not supported, and set `DISABLE_TELEMETRY=1` to opt out of the CLI's anonymous telemetry. Use `npx skills update` to pull later changes.

### Manual install

Clone the repo and link the two folders into the path your agent reads:

```sh
git clone https://github.com/maranix/mobile-app-development-skills.git
cd mobile-app-development-skills

# Claude Code
mkdir -p ~/.claude/skills
ln -sfn "$PWD/skills/mobile-app-conventions" ~/.claude/skills/mobile-app-conventions
ln -sfn "$PWD/skills/mobile-app-scaffold" ~/.claude/skills/mobile-app-scaffold

# Codex, Gemini CLI, OpenCode, and other .agents/skills readers
mkdir -p ~/.agents/skills
ln -sfn "$PWD/skills/mobile-app-conventions" ~/.agents/skills/mobile-app-conventions
ln -sfn "$PWD/skills/mobile-app-scaffold" ~/.agents/skills/mobile-app-scaffold
```

For a project install, use `.claude/skills/` and `.agents/skills/` in the repository instead of the home directory. For Antigravity, Cursor, GitHub Copilot, and other agents, use the path in the table below. Copy the folders instead of symlinking if symlinks are not supported.

## Invoke

Name the skill, then give the request. Each agent discovers skills in its own directory:

| Agent | Project path | Global path | Invoke |
| --- | --- | --- | --- |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` | `/mobile-app-conventions` |
| Codex | `.agents/skills/` | `~/.agents/skills/` | `$mobile-app-conventions` or `/skills` |
| Gemini CLI | `.agents/skills/` or `.gemini/skills/` | `~/.agents/skills/` or `~/.gemini/skills/` | mention the name; `/skills list` confirms it is loaded |
| Antigravity | `.agents/skills/` | `~/.gemini/config/skills/` | `/mobile-app-conventions` |
| OpenCode | `.opencode/skills/` or `.agents/skills/` | `~/.config/opencode/skills/` or `~/.agents/skills/` | mention the name; the agent calls its `skill` tool |
| Cursor | `.agents/skills/` | `~/.cursor/skills/` | mention the name |
| GitHub Copilot | `.agents/skills/` | `~/.copilot/skills/` | mention the name |

Claude Code honors `disable-model-invocation: true`, and `agents/openai.yaml` gives Codex the same explicit-only behavior. Gemini CLI, Antigravity, and OpenCode also choose skills from their descriptions, so naming the skill is the reliable way to invoke it. The examples under each skill show the request that follows the name.

## Layout

```
skills/<skill-name>/
  SKILL.md            # name and description frontmatter, plus the agent's instructions
  agents/openai.yaml  # optional: explicit-only invocation in Codex
  reference/          # material reached only through a pointer in SKILL.md
```

## Adding a skill

1. Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter and `disable-model-invocation: true`, so the skill loads only when invoked. The description is human-facing: one line on what it covers.
2. Add `agents/openai.yaml` with `policy.allow_implicit_invocation: false`, which gives Codex the same explicit-only behavior; `disable-model-invocation` is Claude Code's field.
3. Keep the main file to the process and the rules every run needs. Push branch-specific detail into `reference/` and point at it from `SKILL.md`.
4. Add the skill to the list above.

## License

MIT. See [LICENSE](LICENSE).
