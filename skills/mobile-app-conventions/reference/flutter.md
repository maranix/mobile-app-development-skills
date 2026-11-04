# Flutter conventions

Dart, Flutter, and one state-management choice per project. Pair with the universal rules in [`../SKILL.md`](../SKILL.md).

## Structure

Feature-first, with layers inside the feature:

```
lib/
  app/                   composition root: MaterialApp, router, theme
  features/<name>/
    presentation/        screens, widgets, state notifiers
    domain/              models, use cases, repository interfaces
    data/                DTOs, API clients, local storage, repositories
  core/                  design system, networking, analytics
```

## State

- Pick Riverpod or Bloc for the project and enforce it. Mixing the two doubles the ways a screen can be wrong.
- Expose state as an immutable sealed class or `AsyncValue`; the screen renders the state rather than inferring it.
- Keep notifiers and blocs free of `BuildContext`. Widgets translate state to UI and send intents back.

## UI

- The screen widget is the only stateful layer; leaf widgets take plain data and callbacks and can be `const`.
- Use `LayoutBuilder` for layout decisions and `MediaQuery` for device facts (text scale, insets). Choosing a layout from `MediaQuery.size` ignores the constraints of the widget you are building, which matters as soon as a pane is narrower than the window.
- Give list items keys when the list can reorder, or state follows the wrong row.

## Navigation and dependencies

- `go_router` with typed route objects; configure deep links per platform and parse them into the same routes the UI pushes.
- Riverpod providers, or a single `get_it` registration at the edge. No service-locator calls from widgets.

## Data and errors

- `dio` or `http` plus `json_serializable`; `freezed` for unions if the project already uses it.
- Wire `FlutterError.onError` and `PlatformDispatcher.instance.onError` to the crash reporter; an unhandled async error is otherwise invisible.

## Testing

- `flutter test` for domain and notifier logic; `flutter_test` widget tests for screen states; `integration_test` for one or two flows per feature.
- Golden tests for the design system, with a fixed font and surface size, or they fail on every machine.

## Format and lint

- `dart format` owns layout and `flutter analyze` owns the rules. Include `flutter_lints` or `very_good_analysis` in `analysis_options.yaml`, and start new projects on the stricter set.
- CI runs `dart format --output=none --set-exit-if-changed .` and `flutter analyze --fatal-infos --fatal-warnings`.
- Exclude generated files (`*.g.dart`, `*.freezed.dart`, `*.gr.dart`) from formatting and analysis; regenerate them instead of editing them.
- `// ignore:` needs a reason. Fix the code, or narrow the rule in `analysis_options.yaml`, before reaching for it.

## Gotchas

- Check `mounted` (or `context.mounted`) after every `await` before touching `BuildContext`.
- Large JSON parsing on the main isolate drops frames; move it to `compute` or an isolate.
- Text scaling overflows fixed-height rows; test at the largest scale.
- Ship with `--split-debug-info` and `--obfuscate`; the default artifact carries symbol data the store does not need.
