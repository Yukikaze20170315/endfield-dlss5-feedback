# Fix contended present-side config polling and oversized carried crash reports

**These two patches address a configuration-lock cycle that produced approximately 300 ms stalls with Vulkan MFG, and a launch-time failure when reading an oversized previous crash report.** Both changes belong to NIGos/dlss5-bridge; neither changes ReShade core or reduces NR image quality.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the patch and evidence for errors, including AI hallucinations.

## 1. Defer contended optional config polling

`OnSynthBeginEffects` blocks on `g_bridge_cs` to call `CfgReload`. In the observed Endfield configuration, the render thread holds that lock while waiting for the mirror worker; the worker's GPU input depends on the FG/present path making progress. Six ETW observations showed approximately 292–306 ms waits and worker → renderer → FG wake order. The lock site was matched to the recorded binary's machine-code RVA and lock address.

The patch uses `TryEnterCriticalSection`. If the lock is busy, this add-on's optional callback returns and config polling can resume later. Mandatory GPU synchronization, native NR and MFG 4× remain unchanged. Sustained contention may delay config reload; that is the behavior to review.

## 2. Bound carried crash-report accumulation

The pre8 code appends report lines into a 2048-byte buffer using `strcat_s`. An oversized report can invoke the CRT invalid-parameter handler on the next launch. The patch retains a NUL-terminated prefix and reports truncation.

## Validation

- A three-thread regression reproduces the blocking dependency; the nonblocking variant completed 20/20 runs without timeout. Deferred reload and exception-safe unlocking are covered.
- Report tests cover full buffers, oversized input, subsequent appends, empty input and a one-byte buffer.
- A preserved fixed-build game run recorded 9600 NR deliveries with no input timeout; the reporter confirmed that full-screen flashes and half-second stalls stopped in the tested runs. This is finite validation, not a guarantee for every configuration.
- The minimal release leaves `vkmirror.inc` unchanged. The remaining perceived-smoothness problem is not claimed fixed.

## Patch base and scope

The patches target pre8 commit `ecd1b00674020a1e8c76a9cb653a1a21d11676a0`. Application checks also passed against the affected files at `d1cc508a7097534c5c0e01a868ebe3b6657b932b`; this is not a full build or game validation of that commit. Local version branding is excluded.

Review `0001-bounded-crash-report.patch` and `0002-nonblocking-present-config.patch` independently. Evidence is under `evidence/bridge-lock/`; tests are in `workspace/tools/`.

`0003-optional-nr-cpu-batch-channel.patch` is a separate integration proposal. It identifies CPU evaluation scopes for per-pass parameter views, does not signal GPU completion, and is not required for either fix. Its listener lifetime, load order and public interface naming require maintainer review.
