# Delivered controls and remaining rendering work

**Per-pass model controls are implemented locally; complete per-pass color processing, native Global Tone restoration and full localization remain incomplete.** The table separates observed behavior from proposed capabilities.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the status and evidence for errors, including AI hallucinations.

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
