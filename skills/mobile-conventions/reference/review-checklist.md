# Review checklist

Walk every item. A "no" is a finding, not a style preference. Read the stack reference first, then this list.

## Seams

- [ ] UI imports no data-layer types: DTOs, database rows, HTTP clients.
- [ ] Logic imports no UI framework.
- [ ] Shared code imports no platform APIs.
- [ ] Dependencies are injected at the entry point; logic holds no global singletons.
- [ ] Transport types become domain models at the repository boundary.

## State

- [ ] Each piece of state has one owner; no second copy that can drift.
- [ ] Exclusive states are one type, not parallel flags.
- [ ] UI renders a snapshot; business rules live in the logic layer.
- [ ] One-shot effects are not stored as booleans in state.

## Navigation

- [ ] Routes are typed and carry identifiers, not object graphs.
- [ ] Deep links parse into the same route types the UI pushes.
- [ ] Back behavior is expressed in the back stack, not by relaunching the app.

## Failure

- [ ] Expected failures are values at the boundary; exceptions are for programmer error.
- [ ] Every failure maps to a UI state the user can act on.

## Platform fit

- [ ] Back gesture, permissions, keyboard, and safe areas follow the host platform.
- [ ] Shared UI is a recorded decision, not drift from screen to screen.

## Accessibility

- [ ] Interactive elements have labels.
- [ ] Targets meet 44–48 dp/pt.
- [ ] Layout survives the largest text size.

## Quality gates

- [ ] Formatter and linter run in CI and pass.
- [ ] Logic has unit tests at the seams; one end-to-end flow per feature.
- [ ] No suppression without a stated reason.

## Documentation

- [ ] The feature, bug, or refactor has a `docs/plan.md` entry, and its status and sub-steps match the code.
- [ ] `docs/architecture.md` matches the current modules, seams, dependencies, and commands.
- [ ] `README.md` matches what the app does and how to build, run, and test it.
- [ ] No document describes a feature or command that no longer exists.

## Per stack

Run the checks in the matching reference: [android](android.md) · [ios](ios.md) · [flutter](flutter.md) · [kmp](kmp.md).
