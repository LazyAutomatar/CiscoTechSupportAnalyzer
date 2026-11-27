# Cisco Tech Support Analyzer migration

- Integration merged to main in PR #3; UI/update follow-up: `feat/network-reporter-ui-fixes`.
- Includes all four remote branch histories; retain .NET/Avalonia when resolving the migration revert.
- Runtime: .NET 10 LTS; native C# core, CLI and Avalonia desktop.
- Preserve the original implementation for parity/reference; it is not required by the new desktop.
- Run `pwsh -File scripts/test-all.ps1` for offline core tests and CLI/desktop builds.
- Use `scripts/test-core.ps1 -Filter ... -NoRestore` for focused checks. Logs/TRX: ignored `artifacts/`.
- Run `scripts/package.ps1 -Runtime win-x64` (or an advertised RID) for local self-contained packaging.
- No production release, installer execution or live network validation is performed.
- Native Windows/macOS rendering requires manual review.

## Implementation and validation

- Baseline: `73c4f0362a5d0485c33985cbd0975a3a7c5dfef5`; branch created from main.
- Ported every legacy diagnostic family into the native parser; 31 captured Python outputs matched across 28 synthetic command sections.
- Added process-command aliases, duplicate-section notices, cancellation and safe zero-total memory handling.
- Native desktop: file import, section filtering, structured tables, raw evidence, HTML/JSON export, theme switching, keyboard scaling and cancellation.
- CLI and desktop build without warnings. Core gate: 41 passed; headless UI checks cover window/theme resources and import/export.
- Original Python source is reference-only; no Python subprocess is used by the desktop/core.
- Manual review: native rendering on Windows/macOS, representative field captures, local self-contained packages. No live devices or releases touched.
- Troubleshooting additions: native correlation engine, eight context dashboards, exact source-line navigation, severity filtering and HTML/JSON exports.
- New regressions use synthetic data only. Reference capture validation reads the original path and keeps results in memory without saving copies or reports.
- Integration validation: Release solution build with zero warnings/errors; 57 core tests and 4 headless Avalonia UI tests pass. A read-only reference check parsed 210 command sections and verified every finding's source-line reference.

- Follow-up: Windows CI exposed a test that doubled CR characters after autocrlf checkout. Canonicalize the fixture before producing CRLF input; normalize lone CR captures in the parser. The full local gate was rerun before pushing.

- UI/update follow-up: shared Network Health Reporter presentation fixes, native C# update services, explicit install/restart, configurable future public feed and package/manifest tooling. See `docs/network-reporter-ui.md` and `docs/updates.md`; no public release or installer execution is part of this work.
- Follow-up local gate: 83 core tests and 13 headless UI/update tests pass, with zero build warnings/errors and passing PowerShell manifest checks. Synthetic light/dark and compact screenshots reviewed; native package builds are checked in CI.
