---
name: mobile-app-scaffold
description: Scaffold a new mobile app on Android, iOS, Flutter, or Kotlin Multiplatform. Invoke before creating the project; it reads the request, infers the stack, scope, dependencies, and defaults, asks only for the gaps, records them in docs/architecture.md, and sets up as much or as little as you choose.
disable-model-invocation: true
---

# Scaffold a mobile app

One-time recipe. The output is a skeleton that builds; the steps below add structure, tests, documentation, and one navigable screen, plus lint if chosen.

**Generate, don't hand-write.** Start every project from the stack's recommended CLI or canonical wizard, then modify its output. The generated files are the baseline, so the project keeps the official template's structure and picks up upstream fixes.

Run step 1 first, then read [`../mobile-app-conventions/SKILL.md`](../mobile-app-conventions/SKILL.md) and the stack reference it routes you to. Every step ends with a check; do not start the next step until it passes.

## 1. Read the request and fill the gaps

The prompt is the brief. Extract what it states, infer the rest, and ask only for what remains. Do not re-ask what the prompt already answers.

**Extract.** The purpose and audience; the framework or SDK when named ("an Android app", "an iOS app", "a Flutter app", "KMP", "hybrid"); the app name, application or bundle ID, and organization when present; the features; and any stated constraints such as offline use, permissions, or target devices.

**Infer.** Choose these without asking, and say what you chose:

- **Scope:** a request for an app means scaffold; "template", "starter", or "boilerplate" means vanilla, which stops after step 2.
- **Dependencies:** from the features. Offline means local persistence; camera and object detection mean a camera permission and an on-device inference library; sync means networking. Take the smallest set from the table below that the first feature needs.
- **Defaults:** platform state, navigation, and DI, plus standard lint. The table lists the recommendation first for each concern.
- **Identity:** when the prompt has no name, propose 3–5 with lowercase reverse-DNS IDs derived from the organization, and let the user pick. Name availability is unverified unless the user asks you to check it.

**Ask for the gaps**, in one batch:

- **Identity:** whichever of app name, application or bundle ID, organization, target platforms, and minimum OS versions the prompt does not give. For KMP, also shared UI or native UI, and Gradle or the Kotlin Toolchain.
- **Framework**, only when the prompt neither names one nor makes it clear.
- **License**, from a short menu (MIT, Apache-2.0, GPL-3.0, proprietary), with one recommendation and a one-line reason.
- **Extras**, only when a feature needs a library the default does not cover, or the user asks to override a default.
- **Lint**, only when the user wants something other than standard.

| Concern | Android | iOS | Flutter | KMP |
| --- | --- | --- | --- | --- |
| State | ViewModel + StateFlow (default) | `@Observable` (default), TCA (The Composable Architecture) | Riverpod (default), Bloc, Provider | StateFlow (default) |
| Navigation | Navigation 3 (default), type-safe Navigation Compose | NavigationStack (default) | go_router (default) | platform navigation (default) |
| DI | constructor (default), Hilt, Koin | constructor (default), Factory, Swinject | constructor (default), Riverpod, get_it | constructor (default), Koin, kotlin-inject |
| Persistence | Room (default), DataStore (prefs) | SwiftData (default), Core Data | drift (default), sqflite; shared_preferences (prefs) | SQLDelight (default), Room KMP |
| Networking | Retrofit/OkHttp (default), Ktor | URLSession (default), Alamofire | http (default), dio | Ktor (default) |

Lint levels: **standard** (the stack's formatter and linter with the config named in the stack reference, warnings failing CI), **strict** (`very_good_analysis`, SwiftLint `--strict`, a larger detekt config), or **none** (skip step 5). Android and Xcode templates ship without a lint config; Flutter ships `analysis_options.yaml` with `flutter_lints`; KMP templates usually ship none.

**Record.** Write `docs/architecture.md` before generating: purpose, stack, UI strategy, build, name, IDs, organization, platforms, minimum OS versions, features and the dependencies they imply, state, navigation, DI, persistence, lint level, license, and the README outline that step 9 will fill in.

For example, "Create a new app for workout tracking which works offline and uses camera and object detection along with inference support to recognize my workouts and track them automatically" gives the purpose, so what remains is the stack, the name and ID, the organization, the platforms, and the license; offline means local persistence, and the camera and inference mean the camera permission and an on-device model, so state, navigation, and DI take the platform defaults.

**Check:** every gap has an answer, and `docs/architecture.md` names each choice.

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

## 9. Add the README and license

Write `README.md` from `docs/architecture.md` and the original description: what the app does, the platforms it targets, prerequisites, configuration, the commands to build, run, and test, the folder layout, and the license. Add the chosen `LICENSE` file at the repository root, or an "All rights reserved" notice in the README when the user chose proprietary.

**Check:** a newcomer can build and run from the README alone, and the license file or notice matches `docs/architecture.md`.

## 10. Update the decisions

Update `docs/architecture.md` from step 1 with anything the build changed: the actual package name, the dependencies that were added, and the lint config. Future work reads this before proposing a different shape.

**Check:** a new contributor can name every choice from the document alone.

## Keep the skeleton small

- Ship one navigation example, not a feature.
- Recommend the platform's built-in option first; a package earns its place when the first feature needs it.
- Delete generator sample code, counter state, and placeholder TODOs before the first commit.
