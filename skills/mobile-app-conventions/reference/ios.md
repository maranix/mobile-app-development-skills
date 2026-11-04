# iOS conventions

Swift, SwiftUI, and the Observation framework. Pair with the universal rules in [`../SKILL.md`](../SKILL.md).

## Structure

Feature folders, each with its own layers:

```
App/                     composition root: @main, environment, deep-link routing
Feature/<Name>/
  UI/                    views, view state
  Domain/                models, use cases, repository protocols
  Data/                  API clients, persistence, repository implementations
Core/                    design system, networking, analytics
```

`App` is the only place that constructs concrete services. A feature depends on protocols declared in its own `Domain`.

## UI and state

- Views are structs and stay stateless; an `@Observable` model owns screen state and is held with `@State` by the screen that creates it. Pass `@Bindable` only where two-way binding is genuinely needed.
- Mark UI-facing models `@MainActor` and let `async` work inherit that isolation instead of hopping back with `DispatchQueue.main`.
- Model screen state as one enum with associated values (`.loading`, `.loaded(Content)`, `.failed(Error)`).
- Use `.task` for work tied to view lifetime; it cancels on disappear for free.

## Navigation

- `NavigationStack(path:)` with a typed route enum. Deep links parse into the same enum the UI pushes, so there is one route vocabulary.
- Put the path in an `@Observable` router when several screens drive it; keep it out of individual views.
- Do not present a screen and also push it; pick one presentation per state.

## Dependencies and data

- Composition root in `App`: constructor injection for feature dependencies, `@Environment` for cross-cutting services such as analytics or a clock.
- SwiftData for new persistence; keep Core Data where it already exists and migrate deliberately. Keychain for secrets, `UserDefaults` for small preferences, files for large payloads.
- `URLSession` with `Codable`.
- Structured concurrency: `async let` and task groups for parallel work. `Task.detached` escapes actor isolation and is rarely what you want.

## Testing

- Swift Testing for new unit tests; XCTest for UI and performance tests.
- Snapshot tests for design-system components; do not snapshot whole screens that change weekly.

## Gotchas

- `@Observable` requires iOS 17+. Below that, use `ObservableObject` with `@StateObject`, and do not mix the two models in one feature.
- Fixed frames and single-line text break under Dynamic Type; use `ViewThatFits` or a layout that reflows.
- The keyboard covers content unless the scroll view handles it; test with the keyboard up, not just dismissed.
- Background work is scheduled, not guaranteed; a `BGTaskScheduler` request can be dropped, so the app must reconcile on foreground.
