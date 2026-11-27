# Cisco Tech Support Analyzer changelog

All notable changes to the **Cisco Tech Support Analyzer** project are documented here.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and application versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [Unreleased]

### Added
- Ongoing performance and telemetry parsing improvements.

### Changed

### Fixed

## [2.3.2] - 2026-11-27

### Fixed
- Fixed QuestPDF report export failure caused by `Consolas` font family lookup in QuestPDF 2026.9.x by enabling `UseSystemFonts` and disabling `ThrowOnMissingFontFamilies` in `AssessmentDocuments.cs`.

## [2.3.1] - 2026-11-27

### Fixed
- Fixed changelog scrollbar overlap in `AboutPage.xaml` by adding right-side clearance on the release history list and filtering empty/unreleased headings from the offline changelog view.
- Fixed UTF-8 bullet encoding (`&#x2022;`) in the Feature Capabilities section of `AboutPage.xaml`.

## [2.3.0] - 2026-11-27

### Added
- **Auto-Update & Startup Check:** Added non-blocking background update checking 2 seconds after startup (`CheckOnStartup` + `AutoDownload` preferences), a title-bar `Update vX.Y.Z` accent pill in `MainWindow` that navigates straight to the update review panel, and automatic background downloading of SHA-256 verified Windows installers.
- **Buy Me a Coffee Support CTA:** Added a `#FFDD00` **Buy Me a Coffee** button (`https://buymeacoffee.com/yashn0303`) to the Developer Details card in `About & System` (Windows WPF and cross-platform Avalonia Desktop) and `README.md`.

### Changed
- **Refined Update Experience:** Added one-click **Download & install update** (`DownloadAndInstallCommand`), last-checked timestamp tracking (`LastCheckedText`), **Reset default feed** action, and clearer verification/install status messages across `AboutPage` and `UpdatePage`.
- Extended Obfuscar protection to bundled UI assemblies (`CiscoTechSupportAnalyzer.WPF.dll` and `TechSupport.Desktop.dll`) alongside `TechSupport.Core.dll`, and added macOS (`osx-arm64`, `osx-x64`) and Linux (`linux-x64`, `linux-arm64`) release packages.

### Fixed
- Removed duplicate Windows executable copies from `scripts/package.ps1`, reducing the Windows portable `.zip` and `-setup.exe` installer from ~248 MB to ~83 MB.

## [2.2.0] - 2026-11-27

### Added
- Integrated NetHealth-inspired executive health scorecard banner on the Dashboard with dynamic health status grade ("HEALTHY", "ATTENTION REQUIRED", "CRITICAL"), audit snapshot summary, and one-click quick action routes.
- Added three real-time telemetry barometer cards to the Dashboard: Audit Snapshot Barometer (target host & capture timestamp), Buffer Pressure Index (egress queue & DRAM saturation), and Hardware & Reload Integrity (uptime, reload etiology, and chassis state).
- Added an Active Campus Alerts & Warning Signals section on the Dashboard with severity badges, category routing, and direct "CLI Evidence" inspection buttons.
- Upgraded the QuestPDF assessment report styling in `AssessmentDocuments.cs` to match Network Health Reporter: dark `#0F172A` cover title block with health grade badge, color-coded severity distribution strip (`CRITICAL`, `WARNING`, `INFO`, `PARSED SECTIONS`), metadata banner, and dark `#14171A` terminal evidence boxes with `#00E676` green monospace text.

### Changed
- Re-architected the offline capture section in `AssessmentPage.xaml` into NetHealth's two-column intake layout with dedicated file staging on the left and an Air-Gapped Assessment launch card with green readiness indicator on the right.
- Unified card styling across application tabs with subtle holding cards, consistent padding, and cohesive dark/light mode surface fills.
- Updated the custom title bar with author attribution pill ("By Yash Nalawde") matching Bulk Switch Backup branding.

### Fixed
- Assigned an explicit Windows `AppUserModelID` (`YashNalawde.CiscoTechSupportAnalyzer`) at startup and in the Windows installer shortcuts so Windows always treats Cisco Tech Support Analyzer as its own taskbar application rather than grouping its windows with other .NET apps.
- Fixed semi-transparent alert details modal overlay in `AlertsPage.xaml` by replacing alpha-blended card background brushes with `SolidBackgroundFillColorBaseBrush` and rich drop-shadow elevation, preventing DataGrid row bleed-through.
- Resolved WPF markup error in `AssessmentPage.xaml` by converting Avalonia-style string column definitions to native WPF layout definitions.

## [2.1.0] - 2026-11-23

### Added
- Added a Windows Alerts dashboard with severity and keyword filters, summary/CLI clipboard actions, source-line navigation, and verification steps, plus PDF, editable Word, standalone HTML, and structured JSON assessment exports.
- Added enterprise auditing correlations and posture dashboards for control-plane & management security (AAA, VTY Telnet transport, HTTP server, credential encryption, SNMPv1/v2c communities, CoPP, software authenticity, and install commit state), Layer 2 & VLAN consistency (802.1Q trunk native VLANs, suspended/unconfigured access VLANs, and DTP negotiation), PoE inline power faults and module budget pressure, stack member/port link and sync health, crash artifacts, data corruption, facility alarms, buffer allocation failures, and ASIC TCAM utilization pressure.
- Correlated retained link changes with interface state, CRC counters, and observed neighbors, and enriched err-disabled port findings with explicit root-cause reasons and syslog evidence.
- Added an **Updates & Changelog** page to the primary Windows application with manual feed checks, release-note previews, verified package downloads, and explicit installer handoff.
- Bundled the complete changelog in the Windows application for offline review.

### Changed
- Documented 43 alert types and the capture-to-report workflow with screenshots. The Windows About page follows Network Health Reporter's layout with running build details, update controls, developer information, expandable version history and alert coverage.
- Windows single-file Release builds now bundle the protected analysis library. The Windows build script verifies it before copying the executable.
- Refined the Windows interface with a focused capture overview, workflow-ordered navigation, persistent capture status, and Ctrl+1 through Ctrl+6 page shortcuts.
- Adapted overview cards, the alert inspector, and diagnostic tabs for compact windows, and retained alert selection when filtering or returning from evidence.
- Merged **Software Updates & Changelog** directly into **About & System** so application metadata, update checks, release notes, and the bundled changelog live in one unified view.
- Redesigned PDF and editable Word (`.docx`) assessment reports into formal enterprise deliverables with structured executive KPI tables, device hardware/telemetry specification grids, memory and flash storage utilization tables, security/Layer 2 posture tables, consistent Title Case status badges, and bordered finding cards with source evidence and verification CLI.
- Refined the Windows Dashboard UI for a calmer, minimalist engineering layout with sentence-case labels, tabular monospace counters, left semantic severity bars, dynamically justified segmented tab pills across Diagnostics Explorer and Assessment & Ingestion, and direct navigation to Reports, Alerts, Raw Output, and Diagnostics Explorer.
- Unified Dashboard alert counters and clickable signal cards with the prioritized enterprise troubleshooting findings.
- Expanded Live SSH audit command collection and automatic section extraction for monolithic `show tech-support` captures.
- Aligned the syslog and memory cards and labeled snapshot data accurately. Missing memory output now shows an unavailable state.
- Improved empty-dashboard text wrapping, removed the crowded sidebar author footer, and gave Diagnostics Explorer a clearer health-analysis icon.
- Designed a new enterprise geometric application icon and logo with multi-resolution `.ico` and `.png` assets for window title bars, taskbar pins, and Windows shortcuts.
- Removed redundant bottom status bar line to clean up window aesthetics and maximize content viewport space.

### Fixed
- Resolved mouse wheel and scrollbar scrolling issues on Dashboard and child views by enforcing `ScrollViewer.CanContentScroll="False"` across pages (bypassing `DynamicScrollViewer` measuring traps) and adding tunneling preview mouse-wheel handling on `DashboardScrollViewer`, ensuring natural, smooth vertical page navigation even when hovering over clickable finding cards and controls.
- Added desktop shortcut creation option in the Inno Setup Windows installer (`installer.iss`).
- Fixed segmented nav pill tracks on Diagnostics Explorer and Assessment pages by replacing rigid stack panels with dynamically justified uniform grids, eliminating empty dead space on widescreen displays.
- Resolved finding tag spacing issues and severity badge clipping on Dashboard and Alerts by applying padded pill badges with dynamic theme brushes (`DynamicResource`) and clean margins before category and status tags.
- Fixed character capitalization and status casing across PDF, Word, and structured UI tables, ensuring Layer 2 operational states (`Active`, `Trunking`, `Not Trunking`, `Suspended`, `Local Shutdown`) display in consistent Title Case.
- Fixed cross-platform MSBuild core protection execution targets and assembly reflection loading in `protect-core.ps1` and `test-protected-core.ps1` to support both Windows PowerShell 5.1 and PowerShell Core.
- Resolved xUnit analyzer warning `xUnit2031` in link correlation unit tests.
- Kept Dashboard, Alerts and Reports in agreement when a capture produces zero assessment findings.
- Fixed a `XamlParseException` caused by default two-way binding on the read-only `Changelog` property in the update view, which prevented the Updates & Changelog view from opening.
- Fixed severity badge (`WARNING`) clipping in Dashboard finding rows by allowing the badge column to size dynamically with proper padding.
- Fixed `AssessmentPage` tab routing so navigation triggers from Dashboard, Diagnostics, Alerts, and Reports open directly to Live SSH (`0`) or Offline Import (`1`).
- Added missing `NavigateToSsh` and `NavigateToUpload` commands on `DiagnosticsViewModel` and `RawViewerViewModel` so locked placeholder buttons work reliably.
- Attached `ScrollForward.Enabled` to the global `DataGrid` style so nested tables forward boundary mouse-wheel scrolling smoothly to parent pages.
- Fixed `RawViewerPage.ShowEvidence` line calculation and selection timing so jumping from Alerts reliably highlights and scrolls to the exact evidence line.
- Fixed dark/light contrast overrides on `DashboardPage` memory numbers and `INFO` severity badges.

## [2.0.0] - Enterprise C# WPF & .NET Edition

### Added
- **Native Windows 11 Desktop Application**: Full architectural migration to native C# / .NET 8 using [WPF-UI](https://github.com/lepoco/wpfui) with Fluent/Mica materials, vector icons, and dynamic light/dark theming.
- **Dual Ingestion Engine**:
  - **Live SSH Single Switch Connection**: Direct switch profiling over SSHv2 via SSH.NET with password and privileged secret elevation (`enable`), real-time streaming console logs, and cancellation tokens.
  - **Offline Show-Tech & Backup Import**: Fast file ingestion supporting single text files (`.txt`, `.log`, `.cfg`) and compressed `.zip` archives.
- **NOC Operations Dashboard**:
  - Top executive status cards: Device Status pulse, glowing Critical/Warning counters, System Specs & Uptime.
  - Real-time Active Alerts feed categorized by domain (Syslog, Control Plane, POST Diagnostics, Storage, Layer 2, Power).
  - Log Message Frequency visual ranking chart with severity-coded badges.
  - Platform Memory utilization gauge with byte-accurate DRAM and IOS pool calculations.
- **Deep Diagnostics Explorer**:
  - **Interfaces & Layer 2**: Interface metrics, err-disabled root causes, anomaly counters (CRC, drops, runts, resets), dynamic MAC table, MAC flap loop detection, and Spanning Tree VLAN forwarding posture.
  - **Hardware, Power & Sensors**: Modular chassis/stack members, thermal sensor readings, modular power supplies (PSU-A/B), and PoE inline wattage allocations.
  - **CPU, Memory & Storage**: Top 20 CPU processes, top 20 memory-holding processes, memory pool statistics, flash file systems, and CPU policer punt storm drops.
  - **Redundancy & Crashes**: SSO redundancy states, reload history with failure codes, switchover events, and flash core dump/crash artifact scanner.
  - **Logging & Licensing**: Syslog facility/severity breakdown and Cisco Smart Licensing status.
- **Raw Command Output Inspector**: Integrated terminal viewer with monospace font styling, syntax colors, command search, copy to clipboard, and file export.
- **Standardized "About & System" View**:
  - Windows 11 Fluent 3-card layout matching companion network automation tools.
  - Card 1: App specifications, runtime details, and application log folder launcher.
  - Card 2: Developer identity details, timeless network automation profile, and direct links to LinkedIn and GitHub.
  - Card 3: Feature capabilities and technical coverage summary.
- **Troubleshooting Correlations & Findings**:
  - Evidence-backed alerts for err-disabled ports, PSU faults, BPDU guard violations, punt storms, and low storage.
- **Standalone Distribution**: One-click PowerShell build script (`build.ps1`) publishing a self-contained, single-file win-x64 executable (`CiscoTechSupportAnalyzer.exe`).

### Changed
- Replaced the legacy Python/Flask HTML webview architecture with native WPF rendering, eliminating external runtime dependencies.
- Replaced browser-based tables with native virtualized DataGrids featuring independent scroll forwarding and instant column sorting.
- Enhanced parser regexes to support both dash separators (`--- show ... ---`) and equal-bar separators (`=== show ... ===`), as well as prompt-based CLI captures (`Switch# show ...`).
- Improved memory pool calculations with platform DRAM detection and zero-division safeguards.

### Fixed
- Fixed table scroll interference where inner DataGrid scrolling propagated awkwardly to parent page scroll viewers.
- Fixed theme-dependent text color and badge styling in dark mode for diagnostic tags and syslog severity labels.
- Fixed single-file upload selection flow to prevent accidental multi-switch aggregation.
- Fixed cross-platform line ending normalization (CRLF / lone CR).
- Handled plural process command aliases (`show processes cpu sorted`, `show processes memory sorted`).

---

## [1.0.0] - Legacy Python Prototype

### Added
- Initial Python prototype using Flask and PyInstaller.
- Offline parsing of basic `show tech-support` text captures.
- HTML report rendering with Chart.js visualization.
