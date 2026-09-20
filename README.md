# Endfield Vulkan DLSS5 / MFG integration feedback

**This repository is the central feedback entry for three separate projects: NIGos DLSS5 Bridge, ReShade integration, and the RenoDX Generic NR consumer.** It contains links to the source review bundle and optional precompiled reproduction bundle. The remaining frame-rate/smoothness mismatch is explicitly unresolved.

Prepared and reported by **Codex Astra**. The human reporter has limited low-level programming knowledge and relies mainly on AI-assisted maintenance. Please independently audit all code, evidence and attribution.

## What is already fixed and submitted

The NIGos Bridge PR addresses two defects observed with Endfield Vulkan + native DLSS MFG 4×:

- a present/FG-side configuration-lock cycle associated with repeated waits of roughly 300 ms;
- an oversized carried crash report that could trigger the CRT invalid-parameter handler on the next launch.

PR: https://github.com/NIGos/dlss5-bridge/pull/49

The PR contains only the Bridge source fixes and Windows offline regressions. It does not change ReShade core, the NVIDIA runtime, Generic, the game or image quality settings.

## Downloadable bundles

The latest release assets are attached to the [2026-09-21 release](https://github.com/Yukikaze20170315/endfield-dlss5-feedback/releases/tag/2026.09.21):

- [`upstream-review-20260921-v7.zip`](https://github.com/Yukikaze20170315/endfield-dlss5-feedback/releases/download/2026.09.21/upstream-review-20260921-v7.zip): source review package, patches, selected evidence, build data and component-specific reports.
- [`upstream-optional-binaries-20260921-v8.zip`](https://github.com/Yukikaze20170315/endfield-dlss5-feedback/releases/download/2026.09.21/upstream-optional-binaries-20260921-v8.zip): optional reproduction artifacts. Profile A is the minimal fixed Bridge; Profile B is the matched per-pass/diagnostic Bridge plus companion add-on. The two Bridge profiles are alternatives and must not be installed together.

Compiled artifacts are provided for reproduction only. Source review remains the basis for merging. No game executable, NVIDIA model/driver binary or automatic installer is included.

## Component routing

- **NIGos Bridge:** review [PR #49](https://github.com/NIGos/dlss5-bridge/pull/49) and `docs/BRIDGE_PR.md`.
- **ReShade:** `docs/RESHADE_REPORT.md` describes add-on integration questions and evidence. The crosire/reshade GitHub repository currently has no public Issues or Discussions channel enabled; no core patch is claimed.
- **RenoDX Generic:** review [RankFTW/rhi-repo Issue #1](https://github.com/RankFTW/rhi-repo/issues/1) and `docs/GENERIC_PROPOSAL.md`. The release repository contains the Generic distribution but not the full Generic source implementation.
- **Frame pacing:** `docs/PACING_REPORT.md` records the measurements, limits and pending A/B/A experiments. It is not a completed frame-pacing fix.

The projects are separate: ReShade hosts add-ons, the Bridge supplies a private path, Generic consumes NR calls, and NVIDIA owns the runtime/model/driver layer. A cache FG override or an `.bin` runtime file is not by itself proof of a defect.

## Current measurement boundary

The latest stable runtime-A sample measured 81.959 present records/s, 20.515 simulation markers/s, PCL median 79.232 ms / P95 93.686 ms, and software display-interval P95 41.236 ms. Resource metadata associated 378 complete generated-1/generated-2/generated-3/real groups. These are software/resource observations, not optical FPS or physical input-to-photon measurements.

The next proposed test is actual FG-runtime A/B/A (310.9 cache override versus game-folder 310.5.2, then restore A), while retaining native 4×, two NR passes and image quality. It has not been run. Optimization is currently paused.
