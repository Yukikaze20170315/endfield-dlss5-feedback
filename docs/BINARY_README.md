# Precompiled bridge fixes and per-pass diagnostic reference

**The minimal bridge build contains the two verified bridge fixes. The separate matched bridge/companion pair adds per-pass controls and runtime observation; it is not a completed visual-pacing fix.** Source snapshots, build commands and hashes accompany each artifact.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the artifacts and source for errors, including AI hallucinations.

## Choose one profile

| Profile | Files | Scope |
|---|---|---|
| Minimal bridge | `profiles/A-minimal-bridge/dlss5-bridge.addon64` | Bounded carried-report handling and nonblocking optional config polling; tested against the approximately 300 ms stall/flash issue |
| Per-pass diagnostics | `profiles/B-per-pass-diagnostics/dlss5-bridge.addon64` and `nr-parameter-probe.addon64` | Matched CPU batch-channel bridge and version-specific per-pass/diagnostic companion; output was normal in the recorded FG trace |

The two bridge binaries are alternatives. Do not load both. The diagnostic profile requires its matched pair; neither profile includes Generic or NVIDIA runtime binaries.

## Artifact identity

| Artifact | SHA256 |
|---|---|
| Minimal bridge | `A87933899F18A653BD878E0CA4DA16BB4A474ADF2976A75D13CC362482CDA9FA` |
| Channel-enabled bridge | `3B2C192A540D636FEA648ED31DF3640873C5D18A04130E996A887DFEC19508D6` |
| Diagnostic companion | `6E4CDBCC5BA06A4A24516878BA9E42F2EC2A6C5858382B3524345F23C4D19525` |

`ARTIFACTS.json` maps binaries to source files and build commands. `MANIFEST.json` and `SHA256SUMS.txt` identify packaged content. Source snapshots match the preserved local source; byte-identical rebuilds across toolchain versions or PE timestamps are not claimed.

## Build and runtime requirements

Source directories are `workspace/src/bridge-release/`, `workspace/src/bridge-per-pass/` and `workspace/tools/nr-parameter-probe/`. Run each component's `build.cmd` from its directory. The compiler was MSVC 19.44.35228.0 x64, C++17, `/O2 /MT /EHsc`; the companion also used `/W4 /WX /utf-8` and MASM64. Adjust local toolchain paths. Separately obtained NGX headers are listed in `DEPENDENCIES.json`.

The recorded configuration used ReShade 6.8.0.2155, Generic 5.2.1, NR 310.8.0.0, Streamline 2.10.3 and actual FG override 310.9.0.0. The Generic binary adapter requires SHA256 `A1B78052B58FC285F018362AC8652DF8A01D31BE9F3ECBB9E31776866EAD5887`. Similar filenames are not sufficient compatibility checks.

Close the game and back up current files/configuration outside the add-on search path before manual replacement. Preserve `# dlss5-bridge keep` for an existing bridge configuration. Reference excerpts under `configuration/` describe the recorded 2026-09-20 23:19 baseline; they are not universal defaults and must not overwrite a full ReShade.ini automatically.

The diagnostic companion registers hooks and a status worker even when detailed capture is inactive. It is intended for controlled investigation. No model/driver/Generic replacement or driver-profile change is part of deploying these artifacts.

The minimal bridge had offline regression coverage and a preserved 9600-delivery run without the target input timeout. The diagnostic pair has finite local control/provenance validation. Generated-pixel correctness, optical pacing and physical input-to-photon latency remain unverified. Maintainers can rebuild from source instead of using precompiled binaries.
