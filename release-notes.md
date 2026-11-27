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