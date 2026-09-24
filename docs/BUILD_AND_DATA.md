# Build and verify the bridge fixes and diagnostic reference

**The two bridge patches pass synthetic regressions and application checks. The diagnostic reference reproduces recorded metadata; these checks do not establish a fix for visual pacing.**

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit build assumptions and evidence for errors, including AI hallucinations.

## Build and patch inputs

Apply `0001-bounded-crash-report.patch` and `0002-nonblocking-present-config.patch` to NIGos commit `ecd1b00674020a1e8c76a9cb653a1a21d11676a0`. The optional batch-channel patch is separate. `PATCH_VALIDATION.json` records checks against that baseline and the affected files at `d1cc508a7097534c5c0e01a868ebe3b6657b932b`.

Run `workspace/tools/run-bridge-review-tests.cmd` for the two standalone regressions. Bridge source is in `workspace/src/bridge-release/`; the channel-enabled variant is in `workspace/src/bridge-per-pass/`. Each has `build.cmd`.

The companion source and mock tests are in `workspace/tools/nr-parameter-probe/`. Build there with `build.cmd`, then run `build/test-host.exe` with the companion directory as the working directory. `test-fg-identity.cmd` verifies caller-preserving forwarding with a separate mock calling DLL.

The recorded compiler is MSVC 19.44.35228.0 x64 with C++17, `/O2 /MT /EHsc`; the companion also uses `/W4 /WX /utf-8` and MASM64. Adjust toolchain paths as needed. NGX SDK headers are separately obtained dependencies listed in `DEPENDENCIES.json`; no model is needed for mock tests. Deferred modules additionally require nlohmann/json.

## Trace interpretation

`fg-calls.csv` uses QPC at 10,000,000 ticks/s in the supplied samples. Pointer columns are decimal uint64 values. Addresses must not be reused across process runs.

| Kind | Meaning | Fields |
|---|---|---|
|0|SL constants|Actual frame/viewport; u0 reset, u1 depth convention, u2 camera motion, u3/4 MV flags; f0/1 jitter, f2/3 MV scale, f4–6 near/far/aspect, f7–15 camera basis|
|1|Resource tag|u0 type, u1 lifecycle, u2 index, u3 count, u4–7 extent, u8 state, u9/10 size, u11 format, u12 usage; p0 command, p1 resource, p2 view|
|2|Existing GetState result|Interpret only result 0; u0 status, u1 presents since prior query, u2 minimum dimension, u3 maximum generated; p0 fence, p1 value; frame unknown|
|3|FG options|u0 mode, u1 generated count, u2 flags, u3–6 optional sizes, u7 queue mode; frame unknown|
|4|FG before-call snapshot, version 2|p0 command, p1 ULL private-ID read, p2 opaque private-ID read, p3 feature, p4 params; u0 valid mask, u1 count, u2 index, u3 reset, u4 enable, u5 not-rendering, u15 exception; result UINT_MAX unknown|
|5|FG resource target|u0: 0 backbuffer, 1 HUDless, 2 depth, 3 MV, 4 real output, 5 interpolated output; u1 valid, u2/3 size, u4 format, u5 type, u6 index, u7 read/write; p0 command, p1 descriptor, p2 image, p3 view, p4 params|
|6|Tracked-source copy|p0 command, p1 source, p2 destination; copy-region coverage not recorded|
|7|Backbuffer at present|p0 image, p1 swapchain, p2 queue; u0 call sequence, u1 image index, u2 batch size, u3 raw feedback, u4 exception, u5 flags|
|8|Command submission|p0 command, p1 queue; CPU observation, not GPU completion|

Kind 4 mask bits 0/1 indicate ULL/opaque ID reads; bits 2–6 indicate the five integer fields. Mask 126 contains only the opaque ID read. The public SL frame field is intentionally not filled from this private value. Kind 4 `qpc_after` is snapshot completion, not Evaluate completion. FrameType=Application in PresentMon is not used to classify generated output.

## Recompute supplied measurements

```text
python analyze_pacing.py <presentmon.csv> --qpc-frequency 10000000 --trim-start-ms 10000 --output <analysis.json>
python analyze_fg_identity.py <fg-calls.csv> --out <output-directory>
```

Scripts are under `workspace/tools/pacing-analysis/`. The 23:19 stable runtime sample excludes the first 10 seconds because of a confirmed menu transition; cap-control samples exclude 5 seconds. Their trimming rules are not interchangeable. The initial cap-60 API and PresentMon windows do not overlap and are not paired frame by frame.

Source/copy metadata does not verify pixels or GPU completion. `analyze_pixels.py` requires NumPy/Pillow and supplies image matching metrics, not a smoothness verdict. `EVIDENCE_INDEX.json` records selected evidence and transformations; `MANIFEST.json` identifies packaged content. Game/model binaries and systemwide ETLs are not required to run the supplied synthetic tests.
