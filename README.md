# Mobile App Development Skills

[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/format-Agent%20Skills-5B5BD6)](https://agentskills.io)
[![Platforms](https://img.shields.io/badge/platforms-Android%20%7C%20iOS%20%7C%20Flutter%20%7C%20KMP-3DDC84)](#skills)
[![Agents](https://img.shields.io/badge/agents-7%20supported-111827)](#supported-agents)

Agent skills that give coding assistants a shared engineering baseline for Android, iOS, Flutter, and Kotlin Multiplatform. They cover how a mobile app is structured and layered, where state belongs, how work is planned and recorded, and how a new project is scaffolded from the platform's official generator.

The skills are user-invoked: nothing is loaded until you name one. They are plain Markdown folders, so they work with any agent that supports the [Agent Skills](https://agentskills.io) format.

## Contents

- [Why these skills](#why-these-skills)
- [Skills](#skills)
- [Install](#install)
- [Usage](#usage)
- [Supported agents](#supported-agents)
- [How it works](#how-it-works)
- [Repository layout](#repository-layout)
- [Contributing](#contributing)
- [License](#license)

## Why these skills

- **One spine, four stacks.** The same rules apply everywhere; each stack reference supplies the idiom, the recommended libraries, and the platform-specific gotchas.
- **Seams over folder conventions.** Every boundary between UI, logic, data, and platform code is a cut you can substitute. The skills keep those seams narrow and testable instead of enforcing a directory shape.
- **Official generators first.** New projects start from the stack's recommended CLI or canonical wizard, never a hand-built tree, so the result keeps upstream structure and fixes.
- **Documentation is part of the deliverable.** Decisions, architecture, and planned work live in `docs/decisions.md`, `docs/architecture.md`, and `docs/plan.md`, with an optional `AGENTS.md` entry point for the next agent.
- **Quiet by default.** A skill's body loads only when invoked. Claude Code and Codex can enforce explicit invocation; other agents may also match on the description.

## Skills

### [`mobile-app-conventions`](skills/mobile-app-conventions/SKILL.md)

The shared reference for changing or reviewing a mobile codebase: structure, layering, state ownership, navigation, dependency injection, persistence, error handling, formatting, lint, testing, accessibility, and project documents.

A per-stack reference adds the idiom and gotchas. A [review checklist](skills/mobile-app-conventions/reference/review-checklist.md) turns the rules into pass/fail items, and a [planning reference](skills/mobile-app-conventions/reference/planning.md) covers the project documents and the feature and bug intake.

| Stack | Reference |
| --- | --- |
| Android (Kotlin, Compose) | [`reference/android.md`](skills/mobile-app-conventions/reference/android.md) |
| iOS (Swift, SwiftUI) | [`reference/ios.md`](skills/mobile-app-conventions/reference/ios.md) |
| Flutter (Dart) | [`reference/flutter.md`](skills/mobile-app-conventions/reference/flutter.md) |
| Kotlin Multiplatform | [`reference/kmp.md`](skills/mobile-app-conventions/reference/kmp.md) |

**Example prompts**

| Task | Prompt |
| --- | --- |
| Review | "Review this checkout screen for seam violations and tell me what to move." |
| Plan a feature | "Add offline sync to the workout app. Write the plan entry and requirements first." |
| Make a decision | "We are adding offline support. Which layer owns the cache, and what belongs in shared code?" |
| Configure tooling | "Set up ktlint, detekt, and a CI gate for this Android module." |
| Check shared code | "Is this file safe to move into `commonMain`?" |

### [`mobile-app-scaffold`](skills/mobile-app-scaffold/SKILL.md)

A one-time setup workflow. It reads the request, infers the stack, scope, dependencies, and defaults, and asks only for what is missing. It records the request and agreed choices in `docs/decisions.md`, generates the project with the official CLI or wizard, and applies the scaffold steps: folder structure, dependency injection, lint and CI, a navigation skeleton, a test harness, the accessibility baseline, the README and license, the detailed `docs/architecture.md`, a seeded `docs/plan.md`, and optional `AGENTS.md` instructions.

**Example prompts**

| Scenario | Prompt |
| --- | --- |
| Description first | "Create a new app for workout tracking which works offline and uses camera and object detection to recognize my workouts and track them automatically." |
| Framework named | "Scaffold a Flutter app for tracking expenses. Bundle ID `com.acme.expenses`." |
| Agent support | "Scaffold a Flutter app for tracking expenses and set up agent instructions for Claude." |
| Overrides | "Scaffold a KMP app with Compose Multiplatform, Koin, SQLDelight, and strict lint." |
| Vanilla template | "Scaffold an iOS starter in Xcode. Just the template, no extras." |
| Bare prompt | "Scaffold a field-notes app; suggest names, a license, and a README." |

## Install

Install globally to make the skills available in every project, or into a project's `.agents/skills/` or `.claude/skills/` directory to share them with a team.

### With the skills CLI (recommended)

The [`skills` CLI](https://www.skills.sh/) resolves each agent's skill directory and installs both skills for the agents you name:

```sh
npx skills add maranix/mobile-app-development-skills -g -y \
  -a claude-code -a codex -a gemini-cli -a antigravity -a opencode -a cursor -a github-copilot
```

Requires Node.js 22.20 or later. Omit `-g` for a project install, add `--copy` if symlinks are unsupported, and set `DISABLE_TELEMETRY=1` to opt out of the CLI's anonymous telemetry. Update later with `npx skills update`.

### Manual installation

Clone the repository and link the skill folders into the directory your agent discovers:

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

For a project install, use `.claude/skills/` and `.agents/skills/` inside the repository instead of the home directory. For Antigravity, Cursor, GitHub Copilot, and other agents, use the path in [Supported agents](#supported-agents). Copy the folders instead of symlinking if symlinks are unsupported.

## Usage

Name the skill, then describe the task. Agents with slash commands take the skill name as a command; agents without one take the name at the start of the request.

```text
/mobile-app-conventions Review this checkout screen for seam violations.
```

```text
mobile-app-scaffold: Scaffold a Flutter app for tracking expenses.
```

The conventions skill selects the stack reference from the build files, reads it, and applies the rules. The scaffold skill runs its discovery step first, then generates and layers the project. Neither skill is a runtime dependency: they are instructions the agent follows in the conversation.

## Supported agents

| Agent | Project path | Global path | Invoke |
| --- | --- | --- | --- |
| Claude Code | `.claude/skills/` | `~/.claude/skills/` | `/mobile-app-conventions` |
| Codex | `.agents/skills/` | `~/.agents/skills/` | `$mobile-app-conventions` or `/skills` |
| Gemini CLI | `.agents/skills/` or `.gemini/skills/` | `~/.agents/skills/` or `~/.gemini/skills/` | Mention the name; `/skills list` confirms it is loaded |
| Antigravity | `.agents/skills/` | `~/.gemini/config/skills/` | `/mobile-app-conventions` |
| OpenCode | `.opencode/skills/` or `.agents/skills/` | `~/.config/opencode/skills/` or `~/.agents/skills/` | Mention the name; the agent calls its `skill` tool |
| Cursor | `.agents/skills/` | `~/.cursor/skills/` | Mention the name |
| GitHub Copilot | `.agents/skills/` | `~/.copilot/skills/` | Mention the name |

Claude Code honors `disable-model-invocation: true`, and `agents/openai.yaml` gives Codex the same explicit-only behavior. Gemini CLI, Antigravity, and OpenCode can also activate a skill from its description, so naming the skill is the reliable way to invoke it.

## How it works

The skills follow the Agent Skills progressive-disclosure model:

1. **Discovery.** The agent sees only each skill's name and description.
2. **Activation.** Naming the skill loads its `SKILL.md`.
3. **References.** The skill reads a stack reference, checklist, or planning guide only when the task needs it.

This keeps the default context small while leaving the full rules available on demand. There is no build step, package manager, or generated artifact: the repository is the distributable.

## Repository layout

```text
skills/<skill-name>/
  SKILL.md            # name and description frontmatter, plus the agent's instructions
  agents/openai.yaml  # optional: explicit-only invocation in Codex
  reference/          # material reached only through a pointer in SKILL.md
```

## Contributing

Contributions are welcome. To add a skill:

1. Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter and `disable-model-invocation: true`, so the skill loads only when invoked.
2. Add `agents/openai.yaml` with `policy.allow_implicit_invocation: false` for the same explicit-only behavior in Codex.
3. Keep the main file to the process and the rules every run needs. Move branch-specific detail into `reference/` and point at it from `SKILL.md`.
4. Add the skill to [Skills](#skills) and open a pull request.

Keep skills stack-specific where the idiom differs, and keep the shared rules consistent across stacks. If a change alters a convention, update the matching reference and the review checklist together.

## License

Released under the [MIT License](LICENSE).
