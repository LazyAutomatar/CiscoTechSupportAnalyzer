# Preparing releases

The WPF and Avalonia applications share the same manifest contract and explicit
check/download/install flow. The WPF application stores only its update feed and
startup-check preference in `wpf-update-preferences.json`; capture paths, capture
contents and reports are never stored there. Startup checks are disabled by default.

The default public release repository is
`LazyAutomatar/CiscoTechSupportAnalyzer`, exactly as supplied. The application
does not create it or upload releases. Until a release with `update_manifest.json`
exists, an update check reports that the installed version could not be checked.

## Build and publish later

Use the **Build update packages** manual GitHub Actions workflow with a stable
`X.Y.Z` version and the release repository. It builds self-contained win-x64,
linux-x64, linux-arm64, osx-x64 and osx-arm64 packages. The Windows runner needs
Inno Setup 6 (included on GitHub's Windows image). The final
`release-assets-and-manifest` artifact contains all assets and their manifest.
The workflow has read-only repository permissions and does not publish a release.
The release repository input is optional: it overrides the `RELEASE_REPOSITORY`
Actions variable, which otherwise falls back to the default above. See the
[workflow configuration guide](workflows.md) for repository settings and commands.

Alternatively, use PowerShell 7 and .NET 10 on a suitable build machine:

```powershell
./scripts/package.ps1 -Runtime win-x64 -Version 1.1.0 -Installer
./scripts/package.ps1 -Runtime linux-x64 -Version 1.1.0
./scripts/package.ps1 -Runtime osx-arm64 -Version 1.1.0
./scripts/create-update-manifest.ps1 -Version 1.1.0 -AllowUnreleased
```

Windows installer compilation requires Inno Setup 6 on Windows; omit `-Installer`
to produce a portable ZIP. Other platforms use tar.gz to preserve executable
permissions. The manifest includes only the runtime packages actually built and
prefers the Windows installer over its ZIP when both are present. Use a clean
`artifacts/release` directory for each release to avoid including stale assets.
The build version is embedded in the desktop and used in comparisons.
The example above prepares an unpublished build. For a public release, add a
matching `## [X.Y.Z] - YYYY-MM-DD` entry to `CHANGELOG.md` and omit
`-AllowUnreleased`. Only that version's notes are embedded in the manifest and
written to `release-notes.md`; the full changelog remains bundled separately.
An explicit `-ReleaseNotesPath` may supply a standalone notes file instead.
Do not pass the full changelog through that override.
Every native package includes the README, EULA, license summary, changelog and
documentation. The interactive Windows installer displays `EULA.txt`; silent
updates do not display the license page. These documents are also included in
the final workflow artifact for publication alongside the binaries.

Create a public GitHub release tagged `v1.1.0` (substitute the chosen version),
and upload the files from `artifacts/release/`, including `update_manifest.json`.
Publish the manifest only alongside its complete packages. A newer stable version
is needed to exercise an update from the current 1.1.0 build. Keep the Inno AppId
and installation directory stable across releases. The installer uses the current
user's local application directory and relaunches the installed executable after
the explicit install action; portable installations migrate to that directory.

## Manifest contract

The JSON shape is below. Values in angle brackets are documentation placeholders,
not a usable feed. `sizeBytes` must be the actual positive file length and `sha256`
must be the actual 64-character hash. The generator fills both from the assets.

```json
{
  "version": "1.2.0",
  "title": "Cisco Tech Support Analyzer 1.2.0",
  "releaseNotes": "Changes in this release",
  "packages": {
    "win-x64": {
      "url": "https://github.com/LazyAutomatar/CiscoTechSupportAnalyzer/releases/download/v1.2.0/CiscoTechSupportAnalyzer-1.2.0-win-x64-setup.exe",
      "sha256": "<64 hexadecimal characters>",
      "sizeBytes": 123456,
      "kind": "windows-installer"
    }
  }
}
```

Supported kinds are `windows-installer`, `zip` and `tar.gz`. The client understands
semantic prerelease versions, but the packaging scripts deliberately produce
stable X.Y.Z releases. Missing platform packages leave the release notes visible
without offering a download. Equal and older versions never enable an update.

## Trust and behavior

- Choose a trusted feed. Hashes detect corruption or mismatched assets; they are
  not publisher signatures. No Authenticode signing/notarization is configured.
- HTTPS feeds require HTTPS packages on the same host. GitHub feeds require the
  same owner/repository release-download path. Redirects must stay on the original
  host, except github.com downloads may use release-assets.githubusercontent.com
  or objects.githubusercontent.com. HTTP downgrade, embedded credentials and
  redirect loops are rejected.
- An absolute local JSON manifest is also supported; UNC/device paths are rejected. Its package URLs must use
  HTTPS; it does not enable loading or executing a local binary.
- The manifest is limited to 1 MB, downloads to 3 GB; checks time out after one
  minute and downloads after ten minutes. OS/default proxy settings are used.
- Cancellation or a failed download removes that operation's partial directory.
  Verified packages are kept under the user's local application data directory,
  `CiscoTechSupportAnalyzer/updates`. Changing the feed clears the install action.
  Completed downloads remain available on disk and may be deleted manually.
- Check/download/install are separate actions. The Windows install action checks
  size, SHA-256 and PE format again while holding the verified file open through
  launch on Windows. Other packages are installed
  manually after closing the app. No update package is executed by the tests.
- Preferences are stored only when Save is selected, in the same application data
  area as `preferences.json`; capture paths, contents and reports are excluded.

Automated tests cover version ordering, missing feeds/platforms, local manifests,
transport/repository restrictions, corruption, cancellation cleanup, executable
headers and UI state transitions. Interactive Windows installer/restart and
native macOS/Linux archive launches remain release acceptance checks.
