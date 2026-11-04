# Kotlin Multiplatform conventions

Shared Kotlin with either Compose Multiplatform or native UI. Pair with the universal rules in [`../SKILL.md`](../SKILL.md).

## Structure

```
shared/
  commonMain/            domain models, use cases, repository interfaces, networking, persistence
  androidMain/           actuals, Android-only clients
  iosMain/               actuals, Darwin clients
composeApp/              Compose Multiplatform UI, if shared UI is chosen
iosApp/                  SwiftUI shell, if native UI is chosen
```

`commonMain` is the contract; platform source sets are adapters. A file in `commonMain` that imports `android.*`, `java.*`, or `platform.*` is in the wrong source set.

## What to share

- Share domain models, validation, use cases, repository interfaces, serialization, and data clients.
- Choose shared UI or native UI once, record it in the README, and do not drift screen by screen. A half-shared UI carries both costs.
- Keep UI, navigation, permissions, and platform services on the platform, unless the product has chosen Compose Multiplatform for shared UI.
- Expose state and intent across the boundary; never expose widgets, view controllers, or platform types.

## expect/actual and interop

- Keep `expect`/`actual` to one capability per declaration. When more than one implementation is plausible, declare an interface in `commonMain` and bind it with DI instead.
- The Swift-facing API is the public API. Keep it small, name it for Swift call sites, and annotate throwing functions with `@Throws`.
- Suspend functions surface to Swift as completion-handler calls; wrap them in a Swift `async` adapter rather than leaking Kotlin coroutine types.
- `Flow` does not cross into Swift cleanly; expose a callback or a wrapper that a Swift `AsyncSequence` can consume.

## Concurrency and data

- `kotlinx.coroutines` in `commonMain`; `Dispatchers.Default` for CPU work, `Dispatchers.Main` from the platform for UI-bound state.
- State exposed to UI is a `StateFlow`; the platform layer collects it on the main thread.
- Ktor client with platform engines, `kotlinx.serialization` for payloads, SQLDelight or Room KMP for persistence.

## Testing

- `commonTest` with `kotlin.test`; it runs on every target, so it catches platform divergence early.
- Test `actual` implementations in their own source sets; run `iosSimulatorArm64Test` in CI, not only `testDebugUnitTest`.
- Keep one end-to-end test per platform for the shared flow; that is where linkage and threading bugs appear.

## Gotchas

- Old freezing and `@ThreadLocal` advice predates the current memory model; do not reintroduce it.
- `commonMain` cannot see JVM-only libraries, including much of the Java ecosystem. Check the KMP support of every dependency before adding it.
- Framework export settings decide what Swift sees; a missing `export` produces "unresolved identifier" at the Swift call site, not a Kotlin build error.
- Shared code compiles per target; a change that builds on Android can still fail `iosMain` in CI.
