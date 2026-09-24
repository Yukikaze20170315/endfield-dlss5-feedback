# Per-pass NR controls and bounded runtime observation

**This companion implements per-pass model overrides and original-panel controls for Generic 5.2.1, with bounded SL/FG/presentation observation.** It is a version-specific reference implementation; it does not resolve the remaining visual-pacing issue.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the code and evidence for errors, including AI hallucinations.

## Components

| Files | Responsibility |
|---|---|
| `per_pass.h`, `preset_view.h` | Per-layer parameter views, inheritance and creation-time Preset |
| `inline_overlay.h`, `overlay_merge.h`, `generic_identity.h` | Original-panel integration with binary identity checks |
| `forward.asm`, `fg_forward.asm` | Forward calls while preserving the original return address |
| `fg_audit.h`, `fg_identity.h` | Existing SL calls, actual loaded FG module discovery and output-resource provenance |
| `present_trace.h`, `present_snapshot.h`, `present_context.h` | Bounded Vulkan present metadata and callback context |
| `pixel_capture.h` | Optional bounded readback; default inactive; not a validated NR-on content result |
| `hdr_experiment.h` | Time-bounded pass-2 transfer/color experiment with restoration and edit-conflict handling |

The matched bridge exposes CPU evaluation batches through `nr-observer-channel.h`. This is not a GPU completion notification. The Generic adapter validates the tested binary identity; changing Generic versions requires revalidation.

## Build and tests

Run `build.cmd` from this directory after supplying the NGX SDK dependencies. Run `build/test-host.exe` from the same working directory. `test-fg-identity.cmd` checks cross-module caller identity; `test-fg-audit.cmd` checks transparent SL observation; `test-present.cmd` checks present forwarding and metadata parsing. These are synthetic tests, not universal game compatibility tests.

The FG observer uses an assembly tail jump because the runtime checks the caller's module. Its before-call row does not contain an observed return result. Detailed record requests are bounded in duration; ring overflow, exceptions or incomplete resource associations must be treated as missing evidence.

The optional pixel path records copies in ReShade's existing effects command stream and observes retirement using its completion mechanism. A CPU callback return is not a retirement signal. Capture must be rejected when NR is inactive or format/coverage checks fail.

For deployment, use the matched channel-enabled bridge and companion, with only one bridge active. Close the game and preserve its current files/configuration before replacing binaries. Full API/field interpretation and validation limits are in the package-level build and pacing reports.
