# Mobile app development skills

Agent skills that keep mobile code consistent across Android, iOS, Flutter, and Kotlin Multiplatform.

Skills here are user-invoked: they stay out of the agent's context until you name one. Each is a folder with a `SKILL.md` and reference files the agent reaches only when the task calls for them.

## Skills

### [`mobile-app-conventions`](skills/mobile-app-conventions/SKILL.md)

The shared reference: project structure, layering, state ownership, navigation, dependency injection, persistence, errors, formatting, lint, testing, and accessibility. A per-stack reference adds idiom and gotchas, and a [review checklist](skills/mobile-app-conventions/reference/review-checklist.md) turns the rules into pass/fail items.

| Stack | Reference |
| --- | --- |
| Android (Kotlin, Compose) | [`reference/android.md`](skills/mobile-app-conventions/reference/android.md) |
| iOS (Swift, SwiftUI) | [`reference/ios.md`](skills/mobile-app-conventions/reference/ios.md) |
| Flutter (Dart) | [`reference/flutter.md`](skills/mobile-app-conventions/reference/flutter.md) |
| Kotlin Multiplatform | [`reference/kmp.md`](skills/mobile-app-conventions/reference/kmp.md) |

### [`mobile-app-scaffold`](skills/mobile-app-scaffold/SKILL.md)

A one-time setup recipe: it asks for the app identity, scope (vanilla, scaffold, or scaffold with extras), optional libraries, and lint level, then generates the project with the official CLI or wizard and applies the chosen steps: folder structure, dependency injection, lint and CI, a navigation skeleton, a test harness, the accessibility baseline, and recorded decisions.

## Invoke

Type the skill name with your agent's invocation command, for example `mobile-app-conventions` or `mobile-app-scaffold`. Nothing is loaded until you do.

Skills are plain folders. From the repository root, link them into the directory your agent discovers skills from, for example `~/.agents/skills`:

```sh
ln -s "$PWD/skills/mobile-app-conventions" ~/.agents/skills/mobile-app-conventions
ln -s "$PWD/skills/mobile-app-scaffold" ~/.agents/skills/mobile-app-scaffold
```

Copy the folders instead if symlinks are not supported.

## Layout

```
skills/<skill-name>/
  SKILL.md        # name and description frontmatter, plus the agent's instructions
  reference/      # material reached only through a pointer in SKILL.md
```

## Adding a skill

1. Create `skills/<skill-name>/SKILL.md` with `name` and `description` frontmatter and `disable-model-invocation: true`, so the skill loads only when invoked. The description is human-facing: one line on what it covers.
2. Keep the main file to the process and the rules every run needs. Push branch-specific detail into `reference/` and point at it from `SKILL.md`.
3. Add the skill to the list above.

## License

MIT. See [LICENSE](LICENSE).
