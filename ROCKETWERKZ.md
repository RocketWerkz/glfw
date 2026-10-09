# RocketWerkz fork of GLFW

This is RocketWerkz's fork of [GLFW](https://github.com/glfw/glfw). It is the
GLFW that [Brutal.Glfw](https://github.com/RocketWerkz/brutal.glfw) builds and
ships. It carries a small stack of patches on top of an upstream release, and
nothing else. Issues are disabled here; fork work is tracked in Brutal.Glfw.

## Branches and tags

- `rocketwerkz` is the patch stack: an upstream release tag plus the patches
  listed below, one logical change per commit. It is rebuilt (and force-pushed)
  when moving to a new upstream release, and never otherwise.
- `master` mirrors upstream and is not used.
- `rw/*` tags mark every commit a Brutal.Glfw build has pinned. A ruleset stops
  them being moved or deleted, so every published package can be rebuilt even
  after `rocketwerkz` has been rewritten. Tag `rocketwerkz` as
  `rw/<upstream version>.<n>` before Brutal.Glfw pins it.

## Patch stack

Base: upstream `3.5.1`.

| Patch | Platforms | Provenance |
|---|---|---|
| Cocoa: Add trackpad zoom and rotate events | macOS | [glfw/glfw#2419](https://github.com/glfw/glfw/pull/2419) by pfg, macOS part, squashed from `17d9c90d` |
| Fix trackpad callback setters, assertions and docs | all | RocketWerkz, review fixes to the patch above |
| Add scroll detail callback with source and gesture phase | macOS (flags), all (callback) | RocketWerkz extension |
| Wayland: Add trackpad zoom and rotate events | Wayland | glfw/glfw#2419 by pfg, Wayland part, ported to 3.5.1 and fixed |
| Add ROCKETWERKZ.md | - | RocketWerkz |
| Wayland: Report scroll detail flags | Wayland | RocketWerkz extension |
| X11: Add trackpad zoom and rotate events | X11 (XInput 2.4) | RocketWerkz |
| Cocoa: Keep the desktop video mode in full screen | macOS | RocketWerkz |

The API they add:

- `glfwSetTrackpadZoomCallback`: per-event scale factor (macOS, Wayland,
  X11 with XInput 2.4)
- `glfwSetTrackpadRotateCallback`: per-event degrees, positive
  counter-clockwise (macOS, Wayland, X11 with XInput 2.4)
- `glfwSetScrollDetailCallback` and the `GLFW_SCROLL_*` flags: scroll offsets
  with their source and gesture phase (flags on macOS and Wayland; the
  callback works everywhere)

The README, news and contributor list are deliberately left untouched, because
upstream edits them every release and they would conflict on each rebase. This
file is where the fork's changes and their authors are recorded.

## Naming of fork-only API

Fork additions use GLFW-style names, matching the upstream proposal where there
is one (the trackpad callbacks keep glfw/glfw#2419's names). If upstream later
adds a function with the same name, the rebase fails to compile rather than
silently changing behaviour; adopt upstream's version at that point and adapt
Brutal.Glfw.

## Updating to a new upstream release

1. Fetch upstream tags and branch from the new release tag.
2. Cherry-pick the patches in order and resolve conflicts. A patch whose change
   upstream now has (for example glfw/glfw#2419 merged) is dropped instead;
   remove its row above.
3. Build GLFW and `tests/events` on every platform you can, and let CI build
   the rest (Brutal.Glfw's CI builds Windows, macOS and Linux with X11 and
   Wayland).
4. Force-push the result to `rocketwerkz` and tag it `rw/<version>.<n>`.
5. In Brutal.Glfw: pin the submodule to the tag, regenerate the bindings, and
   update the third-party notices (version line and the Wayland protocol list).

## Watching upstream

At each Brutal upstream audit, check for:

- a new GLFW release
- movement on [glfw/glfw#2419](https://github.com/glfw/glfw/pull/2419); if it
  merges, drop its patches in favour of upstream's
