---
name: mobile-app-scaffold
description: Scaffold a new mobile app on Android, iOS, Flutter, or Kotlin Multiplatform. Invoke before creating the project so structure, dependency injection, lint, navigation, and tests are in place from the first commit.
disable-model-invocation: true
---

# Scaffold a mobile app

One-time recipe. The output is a skeleton that builds, lints, tests, and has one navigable screen; features come later.

Read [`../mobile-app-conventions/SKILL.md`](../mobile-app-conventions/SKILL.md) first, then the stack reference it routes you to. Every step ends with a check; do not start the next step until it passes.

## 1. Name the stack and the UI strategy

Choose Android, iOS, Flutter, or KMP. For KMP, also choose shared UI (Compose Multiplatform) or native UI. Record the stack, the UI strategy, and the state-management library in one sentence each.

**Check:** the three choices fit in a short paragraph you could paste into the README.

## 2. Create the project

Use the canonical generator: Android Studio or `gradle init`, Xcode, `flutter create`, or the KMP wizard. Generate the smallest app the tool offers.

**Check:** the empty app builds and launches on a device or simulator.

## 3. Apply the folder structure

Create the feature-first tree from the stack reference, with one shared core module and no features yet. The composition root is the only place that constructs services.

**Check:** the tree matches the reference and each module declares its dependency direction.

## 4. Wire dependency injection

Construct the graph at the entry point and pass dependencies down. Start with constructor injection; add the framework (Hilt, SwiftUI environment, Riverpod, Koin) only for what the skeleton needs.

**Check:** one repository interface has a real implementation and a fake, both constructed at the edge.

## 5. Add formatting, lint, and CI

Add the stack's formatter and linter with config at the repository root, then a CI job that runs format-check, lint, and tests on every push, failing on warnings.

**Check:** CI is green on the empty app, and a deliberately misformatted line turns it red.

## 6. Add the navigation skeleton

Create two typed routes and one screen that moves between them. Make a deep link resolve to the second route.

**Check:** the deep link opens the right screen and back returns to the first.

## 7. Add the test harness

One unit test for logic, one UI test for the screen, and both running in CI. Use fakes at the seams; no live network in tests.

**Check:** a failing assertion fails CI.

## 8. Add the accessibility baseline

Label the interactive elements, set the minimum target size, and check the screen at the largest text size.

**Check:** the accessibility inspector reports no missing labels, and the layout does not clip at the largest scale.

## 9. Record the decisions

Write a short `docs/decisions.md`: stack, UI strategy, state management, dependency injection, lint, minimum OS versions. Future work reads this before proposing a different shape.

**Check:** a new contributor can name every choice from the document alone.

## Keep the skeleton small

- Ship one navigation example, not a feature.
- Add a library when the first feature needs it.
- Delete generator sample code, counter state, and placeholder TODOs before the first commit.
