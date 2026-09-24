# Undeployed inheritance and localization modules

**These modules provide standalone parameter inheritance and English-fallback localization scaffolding; they are not integrated into the deployed NR panel or rendering chain.**

Prepared with Codex. The human reporter has limited low-level programming knowledge and relies mainly on AI agents for maintenance. Please independently audit the implementation for errors, including AI hallucinations.

`layer_config.hpp` resolves independent settings, inheritance from the first or previous layer, and field overrides. Parameter inheritance is separate from the serial image-input relationship between layers.

`i18n/catalog.hpp` uses English source text as a lookup key and preserves widget identifiers. `zh-CN.json` is an initial Chinese catalog. Missing or empty translations fall back to English. The format-token comparison is incomplete for unsupported printf conversions, including `%n`; it must be corrected before formatted UI integration.

Tests under `deferred/workspace/tools/nr-stack-tests/` require nlohmann/json. Passing those tests does not establish full UI text/font coverage or safe handling of every format string. The modules need native UI integration, full format validation and visual verification before deployment.
