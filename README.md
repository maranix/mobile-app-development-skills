# Mobile app development skills

Agent skills that keep mobile code consistent across Android, iOS, Flutter, and Kotlin Multiplatform.

Each skill is a folder with a `SKILL.md` the agent loads on demand, plus reference files it reaches only when the task calls for them. Rules that hold on every stack live in the main file; idioms that differ live in per-stack references.

## Skills

### [`mobile-app-conventions`](skills/mobile-app-conventions/SKILL.md)

Conventions for building and reviewing mobile apps on four stacks. One shared spine covers project structure, layering, state ownership, dependency injection, persistence, error handling, navigation, testing, and accessibility; each stack reference adds its idiom and its gotchas.

| Stack | Reference |
| --- | --- |
| Android (Kotlin, Compose) | [`reference/android.md`](skills/mobile-app-conventions/reference/android.md) |
| iOS (Swift, SwiftUI) | [`reference/ios.md`](skills/mobile-app-conventions/reference/ios.md) |
| Flutter (Dart) | [`reference/flutter.md`](skills/mobile-app-conventions/reference/flutter.md) |
| Kotlin Multiplatform | [`reference/kmp.md`](skills/mobile-app-conventions/reference/kmp.md) |

Reach for it when scaffolding an app or a feature, deciding what belongs in shared code, or reviewing mobile code for platform fit.

## Getting Started

### Install

Skills are plain folders. From the repository root, link one into the directory your agent discovers skills from, for example `~/.agents/skills`:

```sh
ln -s "$PWD/skills/mobile-app-conventions" ~/.agents/skills/mobile-app-conventions
```

Copy the folder instead if symlinks are not supported.

## Contributing

### Layout

```
skills/<skill-name>/
  SKILL.md        # name and description frontmatter, plus the agent's instructions
  reference/      # material reached only through a pointer in SKILL.md
```

### Adding a skill

1. Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter. The description is what the agent sees before loading the skill, so it must name the tasks that should trigger it.
2. Keep the main file to the process and the rules every run needs. Push branch-specific detail into `reference/` and point at it from `SKILL.md`.
3. Add the skill to the list above.

## License

MIT. See [LICENSE](LICENSE).
