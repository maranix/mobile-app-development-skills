---
name: mobile-app-conventions
description: Mobile app conventions for Android (Kotlin/Compose), iOS (Swift/SwiftUI), Flutter, and Kotlin Multiplatform, covering project structure, layering, state, navigation, dependency injection, persistence, error handling, testing, and accessibility. Use when scaffolding a mobile app or feature, choosing what to share between platforms, or reviewing mobile code for platform fit.
---

# Mobile app conventions

Four stacks share one spine: Android (Kotlin + Compose), iOS (Swift + SwiftUI), Flutter (Dart), and Kotlin Multiplatform (shared Kotlin, platform UI). The rules below hold on all four; each stack reference supplies the idiom that implements them.

The organising idea is the **seam**: every boundary between UI, logic, data, and platform code is a cut you can make and substitute. Keep each seam narrow, and test at the seam instead of through the whole app. Most mobile design mistakes are a missing seam (UI reaching into the database) or a leaky one (a DTO surfacing in a composable).

## Identify the stack

Read the reference for the stack in play before proposing structure or APIs:

| Stack | Signals | Read |
| --- | --- | --- |
| Android | `build.gradle.kts`, `AndroidManifest.xml`, `@Composable` | [`reference/android.md`](reference/android.md) |
| iOS | `Package.swift`, `*.xcodeproj`, `import SwiftUI` | [`reference/ios.md`](reference/ios.md) |
| Flutter | `pubspec.yaml`, `lib/main.dart`, `package:flutter` imports | [`reference/flutter.md`](reference/flutter.md) |
| Kotlin Multiplatform | `commonMain`, `expect`/`actual`, `kotlin { }` | [`reference/kmp.md`](reference/kmp.md) |

A KMP project has a platform UI too: read the KMP reference first, then the Android or iOS reference for the UI layer. When the stack is ambiguous, the build file decides.

## Universal rules

1. **Layer by knowledge, not file type.** UI renders and forwards intent. Logic owns rules and state transitions. Data owns I/O. A type that imports both a UI framework and a network client marks a crossed seam. Transport shapes (DTOs, rows, JSON) stop at the data seam and become domain models.
2. **One source of truth per piece of state.** State lives in the logic layer; UI renders a snapshot. Model exclusive states as one type, not parallel flags, and derive values at read time instead of storing a second copy that can drift.
3. **Flow one way.** Events go down (UI to logic), state comes up (logic to UI). Siblings never call each other; route through the owner.
4. **Inject at the edge.** Build dependencies at the app entry point and pass them in. Logic depends on interfaces, not globals; a static accessor hides the seam and leaks state between tests.
5. **Model failure as a value at the boundary.** Data returns a result or a typed error; logic maps it to UI state; UI renders it. Reserve exceptions for programmer error.
6. **Navigation is data.** Routes are typed values carrying identifiers, not strings or object graphs assembled at call sites, so deep links and back stacks stay testable.
7. **The platform owns platform UX.** Back gesture, permissions, keyboard, safe areas, and share sheets follow the host platform even when a shared layer could imitate them. A foreign back button is noticed faster than a shared abstraction saves code.
8. **Accessibility is part of done.** Every interactive element has a label, a target of at least 44–48 dp/pt, and survives the largest text size. Verify with the platform's inspector, not by eye.
9. **Test at the seam.** Unit-test logic with fakes; contract-test data against a fake server or database; keep end-to-end UI tests to one or two flows per feature. Snapshot tests belong to the design system, not to every screen.

## Shared or platform code

For KMP and Flutter, decide what crosses the platform boundary before writing:

- Share domain models, validation, use cases, repository interfaces, and data contracts: the parts with no UI.
- Keep UI, navigation, permissions, and platform services native unless the product explicitly chooses shared UI (Compose Multiplatform, Flutter).
- Share a feature only when two platforms ship it. A single-platform app gains nothing from an abstraction built for a second platform that does not exist.
- Expose state and intent across the boundary, never widgets or view controllers.

## Applying

1. **Name the stack.** Done when you have read the matching reference above.
2. **Place the change.** Name the layer it belongs to and the seam it crosses. If you cannot name the layer, the design is not settled.
3. **Check the seam.** Ask whether UI imports data, logic imports UI, or shared code imports platform APIs. Any yes means the dependency moves behind an interface.
4. **Verify with the stack's tools.** Run its build, lint, and test tasks; a green build is the floor, not the proof.
