# Delivered controls and remaining rendering work

**Per-pass model controls are implemented locally; complete per-pass color processing, native Global Tone restoration and full localization remain incomplete.** The table separates observed behavior from proposed capabilities.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the status and evidence for errors, including AI hallucinations.

## Version scope — 24 September 2026

This reference implementation and its game observations target Generic 5.2.1. The request to introduce independent later-pass model controls is superseded by the upstream 7.0.0 release candidates.

Both 7.0.0-rc1 and rc8 contain explicit pass-2/3/4 settings for Intensity, Transfer, Color, FollowPass1, Style, LocalTone, Structure, Skin, AutoMask and UICorrection. The rc8 UI explanation says that disabling follow-pass-1 gives a pass its own Look settings. This establishes that upstream has adopted the independent-control design; this report does not request that it be introduced again. rc8 also includes a language selector and Simplified/Traditional Chinese entries.

No distinct NRPass2/3/4Preset configuration keys were found in this static inventory. Preset completeness and Endfield runtime compatibility have not been tested on these builds. Those limits do not invalidate the evidence that upstream recognizes and implements per-pass control. No claim is made that this feedback caused the upstream changes. The version-specific 5.2.1 adapter must not be installed against rc1/rc8 without porting.

Release assets: [rc1](https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc1), [rc8](https://github.com/RankFTW/rhi-repo/releases/tag/renodx-dlss5-7.0.0-rc8). Archive and addon hashes, exact configuration keys and UI excerpts are in `GENERIC_VERSION_EVIDENCE.json`. The observed pass-2 Local Tone isolation and creation-time Preset consumption below remain evidence for the tested 5.2.1 integration only.

| Area | Confirmed state | Remaining work |
|---|---|---|
| Per-pass model controls | LocalTone isolation and creation-time Preset consumption observed; original-panel groups implemented | Native integration in Generic source; broader GPU/visual validation |
| Per-pass Intensity / HDR Transfer / Color | Existing Generic controls reused with one control owner per setting | Verify complete inter-pass encode/composite resource bindings |
| Facial mottling / menu lighting | A pass-2-only HDR Transfer change from 1 to 0 reduced reported mottling | Determine the responsible stage and a quality-preserving correction; no general lighting fix established |
| Global Tone | Value is written, but not consumed by the observed NR 310.8.0.0 path; slider triggers reset | Review capability labeling/reset behavior and investigate an actually supported native control |
| Codec / white point / paper white per pass | Not implemented independently in the deployed path | Define consistent inter-pass color units and verify shader/resource ownership |
| Spatial protection inputs | Resource interfaces were identified, without validated input textures | Establish correct mask/UI/depth sources and formats |
| Rich inheritance / localization | Standalone code and an initial Chinese catalog exist, not deployed | Integrate UI, verify all text/font coverage, and fix incomplete printf-format validation, including unsupported `%n` handling |
| Post-composition second NR pass | Not implemented | Establish image-stage, color-space, guide-data and cost contracts |
| 6× or additional frame generation | No implementation or acceptance test | Verify supported runtime/game capabilities; observed maximum was three generated frames, not a permanent GPU limit |
| Smoothness / latency | Software baselines and resource provenance available | Complete the tests in [PACING_REPORT.md](PACING_REPORT.md) |

The unintegrated modules under `deferred/` are reference code, not a completed rendering pipeline. Parameter inheritance must not alter the serial image-input relationship between stacks. Changing an image's encoding or reducing output transfer is not automatically a lossless visual improvement.
