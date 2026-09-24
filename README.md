# Two DLSS5 Bridge fixes and a per-pass NR implementation

**The included NIGos Bridge patches fix a present-thread configuration-lock cycle associated with approximately 300 ms stalls, and a crash-report accumulation bug that can fail on the next launch.** A separate reference implementation adds per-pass NR controls inside the existing Generic panel. The reported frame-rate/smoothness mismatch remains unresolved.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the code and evidence for errors, including AI hallucinations.

## Review targets

| Component | Deliverable | Verification status |
|---|---|---|
| NIGos/dlss5-bridge | [Two independent fix patches](docs/BRIDGE_PR.md) | Offline regressions and a finite Endfield game run passed |
| RenoDX DLSS5 Generic | [Native per-pass integration proposal](docs/GENERIC_PROPOSAL.md) | LocalTone isolation and creation-time Preset consumption observed |
| ReShade | [Add-on integration report](docs/RESHADE_REPORT.md) | No ReShade-core defect or core patch established |
| FG/input/presentation pipeline | [Pacing investigation](docs/PACING_REPORT.md) | Measurements available; causal attribution incomplete |

The tested configuration is Endfield Vulkan at 5120×2160, two experimental NR passes and native DLSS MFG 4×. The bridge exposes a private D3D12 path to Generic; Generic invokes NVIDIA NR. ReShade hosts the add-ons. These components have separate ownership.

## Attachments

The following directory paths are inside the downloadable source archive.

- `patches/`: bounded-report and nonblocking-config fixes; the optional CPU batch channel is a separate proposal.
- `workspace/`: matching source and offline tests.
- `evidence/`: selected measurements with provenance in `EVIDENCE_INDEX.json`.
- `deferred/`: undeployed inheritance/localization research code, described in [FEATURE_STATUS.md](docs/FEATURE_STATUS.md).
- [BUILD_AND_DATA.md](docs/BUILD_AND_DATA.md): build instructions, dependencies and trace schema.
- [AGENT_GUIDE.md](docs/AGENT_GUIDE.md): a short technical review path.

An optional precompiled package supplies a minimal fixed bridge and a matched per-pass/diagnostic pair. Source review remains the basis for merging; compiled artifacts are for reproduction. Each artifact has source provenance, hashes and a stated validation scope.

## Current upstream scope

The independent-control request is superseded by Generic 7.0.0-rc1/rc8. The packaged adapter and recorded tests target 5.2.1; do not install that adapter on the newer builds. See [GENERIC_PROPOSAL.md](docs/GENERIC_PROPOSAL.md) and [release sources](docs/RHI_AND_RHI_REPO_SCOPE.md).

## Downloads and feedback

[Source and optional binaries](https://github.com/Yukikaze20170315/endfield-dlss5-feedback/releases/tag/2026.09.24). [NIGos PR #49](https://github.com/NIGos/dlss5-bridge/pull/49) contains the two Bridge fixes. [Generic Issue #1](https://github.com/RankFTW/rhi-repo/issues/1) records the independent-control request superseded by 7.0.0-rc1/rc8. The ReShade integration report is publicly available here; delivery to ReShade maintainers has not been confirmed.
