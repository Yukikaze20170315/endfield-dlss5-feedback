# Generic 5.2.1 per-pass reference and upstream 7.0 status

**The reference implementation adds independent Style, Local Tone, Structure, Skin Structure, AutoMask, UI Correction and creation-time Preset controls to stacks 2–4 inside the existing Generic panel.** Pass 1 retains its controls; the existing per-pass Intensity, HDR Transfer and Color controls are reused rather than duplicated.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the implementation and evidence for errors, including AI hallucinations.

## Version scope — 24 September 2026

This reference implementation and its game observations target Generic 5.2.1. The request to introduce independent later-pass model controls is superseded by the upstream 7.0.0 release candidates.

Both 7.0.0-rc1 and rc8 contain explicit pass-2/3/4 settings for Intensity, Transfer, Color, FollowPass1, Style, LocalTone, Structure, Skin, AutoMask and UICorrection. The rc8 UI explanation says that disabling follow-pass-1 gives a pass its own Look settings. This establishes that upstream has adopted the independent-control design; this report does not request that it be introduced again. rc8 also includes a language selector and Simplified/Traditional Chinese entries.

No distinct NRPass2/3/4Preset configuration keys were found in this static inventory. Preset completeness and Endfield runtime compatibility have not been tested on these builds. Those limits do not invalidate the evidence that upstream recognizes and implements per-pass control. No claim is made that this feedback caused the upstream changes. The version-specific 5.2.1 adapter must not be installed against rc1/rc8 without porting.

Release assets: [rc1](https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc1), [rc8](https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc8). Archive and addon hashes, exact configuration keys and UI excerpts are in `GENERIC_VERSION_EVIDENCE.json`. The observed pass-2 Local Tone isolation and creation-time Preset consumption below remain evidence for the tested 5.2.1 integration only.

## Behavior and verification

Each enabled stack has a clearly numbered, initially expanded group. Model overrides are opt-in; disabling them forwards the original settings. Preset independently selects inheritance or a creation-time value. Global resolution remains outside stack groups.

In a controlled test, pass 1 Local Tone stayed at 0.5 while pass 2 was 0.25. A separate test observed pass 2 Preset 1 being read at feature creation, followed by successful creation; restoring inheritance also succeeded. Preset edits currently use Generic's overall rebuild path, not isolated single-layer rebuilding.

CPU tests cover 1–4 UI groups, parameter routing, re-registration, downstream history-reset bookkeeping and preserving other add-ons' GUI tables. This is not exhaustive validation of all four-pass GPU configurations or all visual parameter combinations.

## Native integration

The local adapter targets Generic 5.2.1 SHA256 `A1B78052B58FC285F018362AC8652DF8A01D31BE9F3ECBB9E31776866EAD5887`. Complete source for this consumer was unavailable, so the adapter uses verified RVAs and ImGui table layouts. It is reference code, not an applicable source patch against Generic.

A source-owned implementation should resolve per-layer configuration at its creation/evaluation sites and draw controls inside the existing stack loop. Use stable UI identifiers and actual GPU completion for resource retirement. The bridge CPU batch channel identifies evaluation scope only.

Parameter inheritance must remain separate from image chaining: inheriting stack 1's values must not make stack 3 read stack 1's image. Complete encode/NR/composite bindings between layers still require verification.

## Additional findings and open work

- For Generic 5.2.1 with NR 310.8.0.0, `GlobalToneStrength` is written but is not consumed by the observed runtime path; its slider still triggers history reset. Capability-aware labeling and reset behavior warrant review. Native Global Tone restoration is unresolved; another tone operation is not supplied as a substitute.
- Reducing only pass 2 HDR Transfer from 1 to 0 produced a reported reduction in facial mottling. It may also reduce desired effects; this is a mitigation observation, not a lossless rendering fix.
- Per-layer Codec/white-point/paper-white processing, spatial protection inputs and complete localization are not implemented in the deployed integration. Their status is listed in [FEATURE_STATUS.md](FEATURE_STATUS.md).

Source entry points are `per_pass.h`, `preset_view.h`, `inline_overlay.h` and `overlay_merge.h` under `workspace/tools/nr-parameter-probe/`.
