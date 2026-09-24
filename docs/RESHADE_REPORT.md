# Endfield Vulkan MFG: add-on fixes and an unresolved pacing issue

**A third-party bridge's configuration-lock cycle and oversized crash-report handling have been fixed locally. No ReShade-core patch is proposed.** The remaining issue is substantially poorer perceived motion smoothness than the approximately 80 presents/s reported under two NR passes and native MFG 4×.

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the observations for errors, including AI hallucinations.

## Configuration and component boundaries

Endfield uses Vulkan on an RTX 5090 with driver 616.92 and a 5120×2160, 120 Hz display. VSync and G-SYNC are disabled. The tested components are ReShade 6.8.0.2155, NIGos pre8-based Bridge, RenoDX Generic 5.2.1, NR 310.8.0.0 and Streamline 2.10.3. Image-load events identify actual FG as NVIDIA's signed cache override 310.9.0.0; the game-folder FG DLL is 310.5.2.0.

The bridge mirrors the game's DLSS contract into a private D3D12 path so Generic can invoke NR, then returns the result to the game. Native FG and the presentation chain operate downstream. Complete intermediate shader bindings have not been established. The observed final swapchain is RGBA8 UNORM/sRGB nonlinear; the display-link 10-bit setting does not describe that swapchain's precision.

## Relevant observations

- An add-on's present/FG callback can block progress needed by another render/GPU dependency. The attached bridge fix defers an optional config poll; it does not change ReShade callback dispatch or remove GPU waits.
- The traced ReShade present route carries `VkSetPresentConfigNV` with a four-frame batch. CPU callback intervals are not physical display intervals under hardware metering.
- Output-target, copy, submission and backbuffer records associate 378 complete generated-1/generated-2/generated-3/real groups. This is resource provenance, not a pixel-correctness or optical-pacing measurement.
- An observed sample used the same graphics and present queue. No fault in ReShade's secondary-queue/present-batch handling has been demonstrated. OBS was present in the caller chain, without established causal responsibility.

## Questions for ReShade maintainers

1. Is there a supported add-on interface for associating Vulkan MFG callback events with batch/frame roles, beyond resource handles and metering metadata?
2. Are there additional documented constraints for readback work recorded at `reshade_finish_effects` and completion observation after `finish_present`, particularly when graphics and present queues differ?
3. Would guidance on avoiding blocking optional work in present/FG callbacks help add-on authors prevent cross-component dependency cycles?

There is no minimal ReShade-only reproduction or ReShade-free baseline for this display configuration. The supplied Generic binary adapter is not proposed for merging into ReShade. The bridge fixes are reviewed in [BRIDGE_PR.md](BRIDGE_PR.md); the unresolved measurements and planned discriminating tests are in [PACING_REPORT.md](PACING_REPORT.md).
