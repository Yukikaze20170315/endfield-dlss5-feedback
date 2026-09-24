# Technical review entry point

**Review the two bridge fixes independently; treat per-pass integration as reference code and the residual pacing issue as unresolved.** [BRIDGE_PR.md](BRIDGE_PR.md) provides the minimal change and evidence summary.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit all claims for errors, including AI hallucinations.

## Version scope — 24 September 2026

This reference implementation and its game observations target Generic 5.2.1. The request to introduce independent later-pass model controls is superseded by the upstream 7.0.0 release candidates.

Both 7.0.0-rc1 and rc8 contain explicit pass-2/3/4 settings for Intensity, Transfer, Color, FollowPass1, Style, LocalTone, Structure, Skin, AutoMask and UICorrection. The rc8 UI explanation says that disabling follow-pass-1 gives a pass its own Look settings. This establishes that upstream has adopted the independent-control design; this report does not request that it be introduced again. rc8 also includes a language selector and Simplified/Traditional Chinese entries.

No distinct NRPass2/3/4Preset configuration keys were found in this static inventory. Preset completeness and Endfield runtime compatibility have not been tested on these builds. Those limits do not invalidate the evidence that upstream recognizes and implements per-pass control. No claim is made that this feedback caused the upstream changes. The version-specific 5.2.1 adapter must not be installed against rc1/rc8 without porting.

Release assets: [rc1](https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc1), [rc8](https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc8). Archive and addon hashes, exact configuration keys and UI excerpts are in `GENERIC_VERSION_EVIDENCE.json`. The observed pass-2 Local Tone isolation and creation-time Preset consumption below remain evidence for the tested 5.2.1 integration only.

## Reading order

1. Read the component-specific report: [BRIDGE_PR.md](BRIDGE_PR.md), [GENERIC_PROPOSAL.md](GENERIC_PROPOSAL.md) or [RESHADE_REPORT.md](RESHADE_REPORT.md).
2. Inspect the relevant patch/source and its evidence entries. `EVIDENCE_INDEX.json` supplies provenance; `MANIFEST.json` supplies package hashes.
3. For pacing work, read [PACING_REPORT.md](PACING_REPORT.md) and the schema in [BUILD_AND_DATA.md](BUILD_AND_DATA.md) before interpreting timestamps or frame roles.

## Interpretation constraints

- CPU NR batch completion is not GPU retirement. Resource/descriptor/history reuse requires a valid completion contract.
- Present counters and PCL are not optical FPS or physical input-to-photon measurements.
- Reconstruct resource provenance using recorded command/queue order. A reused handle, nearby timestamp or group of four rows alone does not identify a frame.
- GetState has consumptive count semantics. The observer does not add queries; records without frame tokens remain unpaired unless an independent relation is established.
- The tested FG runtime validates the caller module using the return address. The supplied tail-forwarder preserves that address. Kind 4 version 2 therefore records only a before-call snapshot: `result=4294967295` is unknown, and `qpc_after` is snapshot end.
- The Generic GUI/Preset adapter is hash/RVA-specific. A source-native integration should replace that compatibility machinery with explicit layer/configuration ownership.

## Verification path

Use an isolated checkout for patch application and synthetic tests. Check the actual loaded runtime, NR activity, focus, loss counters and identifier semantics before interpreting runtime records. Reject affected associations when resource copies, queue dependencies or roles are unobserved. Do not infer a root cause solely from a changed file path or a higher FPS counter.

The patches preserve SR/AA, NR image quality and native 4×. Mandatory waits should not be removed without identifying their producer/consumer contract. A single-mechanism change and reversal should test any proposed pacing cause; inaccessible driver/runtime boundaries must remain explicitly unresolved.
