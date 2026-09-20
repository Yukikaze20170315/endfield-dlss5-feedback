# Endfield MFG: measured throughput, latency and unresolved visual pacing

**The investigation has measured approximately 20 real-input updates and 80 present records per second, and associated 378 complete generated/generated/generated/real resource groups. It has not established why perceived smoothness is substantially lower than the counter suggests.** No optical frame-rate measurement or pacing fix is claimed.

Prepared and reported by **Codex Astra**. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the measurements and causal interpretations for errors, including AI hallucinations.

## Test configuration

Endfield Vulkan; RTX 5090; driver 616.92; 5120×2160 at 120 Hz; two Generic NR passes; native MFG 4×; VSync and G-SYNC disabled. ReShade 6.8.0.2155, Generic 5.2.1, pre8-based Bridge, NR 310.8.0.0 and Streamline 2.10.3 were used. Actual FG is the signed NVIDIA cache runtime 310.9.0.0; the game-folder DLL is 310.5.2.0.

The target is better motion continuity and response without reducing the configured NR image quality. The visual symptom is a subjective report, not a measured 30 FPS output.

## Workload controls

| Condition | Present records/s | New simulation markers/s | PCL median / P95, ms |
|---|---:|---:|---:|
| Two NR passes and bridge |80.963|20.211|79.40 / 90.63|
| NR disabled, bridge active |156.918|39.219|46.48 / 52.34|
| Bridge transport retained, private evaluate/copyback skipped |214.039|53.530|35.09 / 40.54|
| Mirror bypassed |228.004|57.011|34.65 / 40.39|

Disabling NR improved reported smoothness, but also changed input rate and latency. These controls therefore do not isolate an NR/FG defect at equal real-input rate. The approximately 6.82 ms period difference between the middle conditions belongs to private evaluation and related work, not an isolated GPU kernel or proven losslessly removable cost. Mirror bypass still loads ReShade and add-ons.

## Frame-cap control: 60 → 30 → 60

| Metric | First 60 |30| Restored 60 |
|---|---:|---:|---:|
| Present records/s |78.12|79.50|78.81|
| New simulation markers/s |19.55|19.87|19.74|
| Software display interval P95, ms |41.92|42.02|42.57|
| PCL median, ms |82.95|80.94|77.04|

This sequence did not establish a repeatable large pacing improvement or latency regression at the 30 cap. Both caps exceed the approximately 20 Hz real supply. The two 60 observations also differ. Retained windows were foreground, with the first 5 seconds excluded.

## Output provenance and stable runtime baseline

A 20-second record contains 18,292 observations without ring loss or read faults: 379 private FG identifiers, each with generated indices 1/2/3. Target/copy/submission/backbuffer metadata associates 1,514 presents, including 378 complete G1/G2/G3/real groups.

A confirmed menu transition temporarily disabled FG for 28 presents. Excluding the first 10 seconds yields a stable 310.9 baseline of 81.959 presents/s, 20.515 simulation markers/s, PCL median 79.232 ms and P95 93.686 ms, and software display-interval P95 41.236 ms. Data is under `evidence/pacing-baseline-20260920-231929-116a2126/`; provenance records are under `evidence/latest-identity/`.

## Evidence limits and technical obstacles

- Software present/display events do not measure distinct full-screen optical frames, particularly with tearing and hardware metering. PCL is a PC-side estimate, not physical input-to-photon latency.
- Resource provenance does not establish pixel correctness, full copy coverage, absence of subsequent shader writes, or GPU completion. No valid NR-on pixel-content result is supplied.
- FG private IDs and public SL FrameTokens are separate identifiers. GetState has no FrameToken; query viewport 0 must not be silently equated with tagged viewport 3. Kind 4 version 2 has an unobserved return value, not a success result.
- An equal-real-rate NR-on/off comparison needs a verified pre-FG limiter; a reliable 20 Hz control is not available. Final-present limiting is not equivalent.
- Complete Generic source is unavailable. Endfield's interposer exports `HGSetupCustomVulkan`, absent from the inspected stock newer package. Wholesale Streamline replacement has not been validated.
- Earlier scheduling targets and complete GPU dependencies are not observed. A necessary wait for slow upstream input is not itself a downstream defect. There is no ReShade-free baseline on this display.

## Planned discrimination and acceptance

1. Compare actual FG runtimes using 310.9 → 310.5.2 → 310.9. Change only the per-game model override; verify the loaded module changes while NR quality and native 4× remain fixed. This experiment has not been performed.
2. Capture bounded lossless images associated with observed real/generated roles. Compare generated content with its actual endpoint images, reject ambiguous/occluded regions, and quantify collection overhead; uniform manual camera speed is not assumed.
3. Establish an equal-real-input-rate control and correlate resource generations, producer completion, consumer waits and observable presentation timing. Attribute only the earliest demonstrated broken contract.
4. Change one implicated mechanism and reverse the change to test causality. Reducing duplicate SR work is a separate potential throughput/latency optimization requiring preservation of SR/AA and Generic insertion semantics.

Acceptance requires matched-condition improvement in visible motion and comparable latency, unchanged target image quality and 4×, and no new flashing, black output or long timeout cycles. A higher counter alone is insufficient. Investigation is paused; these tests remain pending.
