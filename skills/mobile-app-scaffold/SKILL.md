---
name: mobile-app-scaffold
description: Scaffold a new mobile app on Android, iOS, Flutter, or Kotlin Multiplatform. Invoke before creating the project; it asks for identity, scope, libraries, and lint, then sets up as much or as little as you choose.
disable-model-invocation: true
---

# Scaffold a mobile app

One-time recipe. The output is a skeleton that builds; the steps below add structure, tests, and one navigable screen, plus lint if chosen.

**Generate, don't hand-write.** Start every project from the stack's recommended CLI or canonical wizard, then modify its output. The generated files are the baseline, so the project keeps the official template's structure and picks up upstream fixes.

Run step 1 first, then read [`../mobile-app-conventions/SKILL.md`](../mobile-app-conventions/SKILL.md) and the stack reference it routes you to. Every step ends with a check; do not start the next step until it passes.

## 1. Ask before generating

Ask these in one batch, and do not choose IDs, libraries, or lint rules on the user's behalf.

**Identity.** App name, application ID or bundle ID in reverse-DNS form, organization, target platforms, and minimum OS versions. For KMP, also confirm shared UI or native UI, and Gradle or the Kotlin Toolchain.

**Scope.** One of:

- **Vanilla:** the generator's output only. Stop after step 2.
- **Scaffold:** the full recipe — structure, DI seam, navigation skeleton, tests, accessibility. Lint is the separate choice below.
- **Scaffold + extras:** the recipe plus libraries the user picks below.

For vanilla, skip the extras and lint questions.

**Extras, only for scaffold + extras.** Offer two or three options per concern, with the simplest platform default first. Add only what the first feature needs.

| Concern | Android | iOS | Flutter | KMP |
| --- | --- | --- | --- | --- |
| State | ViewModel + StateFlow (default) | `@Observable` (default), TCA (The Composable Architecture) | Riverpod (default), Bloc, Provider | StateFlow (default) |
| Navigation | Navigation 3 (default), type-safe Navigation Compose | NavigationStack (default) | go_router (default) | platform navigation (default) |
| DI | constructor (default), Hilt, Koin | constructor (default), Factory, Swinject | constructor (default), Riverpod, get_it | constructor (default), Koin, kotlin-inject |
| Persistence | Room (default), DataStore (prefs) | SwiftData (default), Core Data | drift (default), sqflite; shared_preferences (prefs) | SQLDelight (default), Room KMP |
| Networking | Retrofit/OkHttp (default), Ktor | URLSession (default), Alamofire | http (default), dio | Ktor (default) |

The default is the recommendation. Explain a choice only when the user asks.

**Lint and formatting.** Android and Xcode templates ship without a lint config: no ktlint or `lint.xml`, no SwiftLint or SwiftFormat. Flutter ships `analysis_options.yaml` with `flutter_lints`, and KMP templates usually ship none. Ask which level to use:

- **Standard (recommended):** the stack's formatter and linter with the config named in the stack reference, warnings failing CI.
- **Strict:** the stricter rule set (`very_good_analysis`, SwiftLint `--strict`, a larger detekt config).
- **None:** skip step 5 and leave the generator's output alone.

**Check:** every answer is explicit; nothing is assumed.

## 2. Create the project

Use the stack's official generator, in this order, and keep it current (`flutter upgrade`, `kotlin update`, the current Xcode and `android` CLI) so the template carries the latest fixes:

- **Android:** prefer the `android` CLI: `android create empty-activity --name="My App" --application-id=com.example.myapp --output=./my-app`. Check that `android create` is available first. If it is missing, recommend installing it (it covers project creation, devices, screenshots, and SDK management) and ask before installing; install per https://developer.android.com/tools/android-cli. Only if the user explicitly declines, fall back to Android Studio > New Project. `gradle init` does not create an Android app.
- **iOS:** use Xcode > File > New > Project > App. Xcode is the official tool; if the agent cannot drive the GUI, ask the user to run the wizard, then take over. Do not substitute a third-party generator unless the user asks for one.
- **Flutter:** `flutter create --empty --org com.example --platforms=android,ios my_app`. `--empty` skips the counter sample.
- **KMP:** JetBrains recommends the Kotlin Toolchain for new projects: `kotlin init` (install with `curl -fsSL https://kotl.in/install.sh | sh`). If `kotlin init` is unavailable, recommend the Toolchain and ask before installing; if declined, use the Gradle path: the wizard at `kmp.jetbrains.com` or IDE > New Project > Kotlin Multiplatform. The Toolchain is pre-1.0; choose Gradle when the project must stay on the standard build.

Generate the smallest app the tool offers, then layer the scaffold's changes on that baseline. Delete any sample screens the template leaves behind; a project assembled by hand loses the template's structure and the upstream fixes that come with it.

**Check:** the empty app builds and launches on a device or simulator.

## 3. Apply the folder structure

Create the feature-first tree from the stack reference, with one shared core module and no features yet. The composition root is the only place that constructs services.

**Check:** the tree matches the reference and each module declares its dependency direction.

## 4. Wire dependency injection

Construct the graph at the entry point and pass dependencies down. Start with constructor injection; add the framework (Hilt, SwiftUI environment, Riverpod, Koin) only for what the skeleton needs.

**Check:** one repository interface has a real implementation and a fake, both constructed at the edge.

## 5. Add formatting, lint, and CI

Skip this step when the user chose None. Otherwise use the level from step 1: add the stack's formatter and linter with config at the repository root, then a CI job that runs format-check, lint, and tests on every push, failing on warnings.

**Check:** CI is green on the empty app, and a deliberately misformatted line turns it red.

## 6. Add the navigation skeleton

Create two typed routes and one screen that moves between them. Make a deep link resolve to the second route.

**Check:** the deep link opens the right screen and back returns to the first.

## 7. Add the test harness

One unit test for logic, one UI test for the screen, and both running in CI. Add the CI job here if step 5 was skipped. Use fakes at the seams; no live network in tests.

**Check:** a failing assertion fails CI.

## 8. Add the accessibility baseline

Label the interactive elements, set the minimum target size, and check the screen at the largest text size.

**Check:** the accessibility inspector reports no missing labels, and the layout does not clip at the largest scale.

## 9. Record the decisions

Write a short `docs/decisions.md`: stack, scope, UI strategy, build, state management, dependency injection, lint level, and minimum OS versions. Future work reads this before proposing a different shape.

**Check:** a new contributor can name every choice from the document alone.

## Keep the skeleton small

- Ship one navigation example, not a feature.
- Recommend the platform's built-in option first; a package earns its place when the first feature needs it.
- Delete generator sample code, counter state, and placeholder TODOs before the first commit.
