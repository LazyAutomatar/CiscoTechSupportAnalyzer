### Added
- **Auto-Update & Startup Check:** Added non-blocking background update checking 2 seconds after startup (`CheckOnStartup` + `AutoDownload` preferences), a title-bar `Update vX.Y.Z` accent pill in `MainWindow` that navigates straight to the update review panel, and automatic background downloading of SHA-256 verified Windows installers.
- **Buy Me a Coffee Support CTA:** Added a `#FFDD00` **Buy Me a Coffee** button (`https://buymeacoffee.com/yashn0303`) to the Developer Details card in `About & System` (Windows WPF and cross-platform Avalonia Desktop) and `README.md`.

### Changed
- **Refined Update Experience:** Added one-click **Download & install update** (`DownloadAndInstallCommand`), last-checked timestamp tracking (`LastCheckedText`), **Reset default feed** action, and clearer verification/install status messages across `AboutPage` and `UpdatePage`.
- Extended Obfuscar protection to bundled UI assemblies (`CiscoTechSupportAnalyzer.WPF.dll` and `TechSupport.Desktop.dll`) alongside `TechSupport.Core.dll`, and added macOS (`osx-arm64`, `osx-x64`) and Linux (`linux-x64`, `linux-arm64`) release packages.

### Fixed
- Removed duplicate Windows executable copies from `scripts/package.ps1`, reducing the Windows portable `.zip` and `-setup.exe` installer from ~248 MB to ~83 MB.