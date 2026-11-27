# Build and release scripts

These helpers follow Network Health Reporter's build, validate and explicitly
publish workflow, adapted to this C#/.NET application. Use PowerShell **7.4 or
newer**, .NET 10 SDK and `tar` on the build machine. Windows installers also need
Inno Setup 6 on Windows. Only publication needs GitHub CLI (`gh auth login`).

| Helper | Purpose |
| --- | --- |
| `scripts/run-dev.ps1` | Run the desktop, or `-App cli -CapturePath /path/capture.txt` (JSON to stdout) |
| `scripts/test-all.ps1` | Core/UI tests, builds, manifest and release-script rejection checks |
| `publish-all.ps1` | Build selected platforms, generate notes/manifest and verify release assets |
| `publish-all.bat`, `publish-all.sh` | Forward arguments and exit status to the PowerShell build wrapper |
| `scripts/package.ps1` | Build one protected runtime archive; optional Windows installer |
| `scripts/protect-core.ps1` | Transform the core with the pinned local Obfuscar tool before bundling |
| `scripts/test-protected-core.ps1` | Check transformed metadata and run core tests against that assembly |
| `scripts/get-release-notes.ps1` | Extract an exact changelog version; preview fallback is explicit |
| `scripts/create-update-manifest.ps1` | Generate package hashes, sizes, URLs and matching release notes |
| `scripts/copy-release-docs.ps1` | Copy the EULA, README, changelog, security notes and guides |
| `scripts/verify-release-assets.ps1` | Verify manifest binding, hashes/sizes, archive paths, docs and source/symbol exclusion |
| `scripts/publish-release.ps1` | Preview verified uploads; `-ConfirmPublish` creates and publishes a checked draft |

## Local builds

Run from the source checkout; scripts locate the project relative to themselves.

```powershell
./scripts/test-all.ps1
./publish-all.ps1 -Target linux-x64 -Version 1.1.0 -AllowUnreleased
# Windows with Inno Setup:
./publish-all.ps1 -Target win -Version 1.1.0 -Installer -AllowUnreleased
# All five portable runtimes (can cross-compile; still test on target machines):
./publish-all.ps1 -Target all -Version 1.1.0 -AllowUnreleased
```

Targets: `all`, `win`, `mac`, `linux`, or one of `win-x64`, `osx-arm64`,
`osx-x64`, `linux-x64`, `linux-arm64`. `-Installer` is explicit and only valid on
Windows with a target including win-x64. A fresh Windows ZIP build removes that
version's older installer so the manifest cannot select a stale setup file.
Use an empty `artifacts/release` for each release. Builds retain other platform
packages already in that folder so separately built runtimes can be combined.

`publish-all` is a local build command despite its Reporter-compatible name.
Outputs are under `artifacts/release`. Cross-platform validation does not execute
the generated application or installer. Protection regression tests run on the
build host against the transformed managed core, including cross-compiled builds.
Protection maps/configuration stay in `artifacts/protected` and must remain private.

Normal CI uses committed NuGet lock files and fails on dependency drift or audit
errors. For an intentional dependency upgrade, run `dotnet restore
CiscoTechSupportAnalyzer.sln --force-evaluate` locally, review and commit the lock
changes, then rerun validation. Do not disable locked restore to fix CI.

## Publish later

First create/initialize the planned public repository, prepare a versioned
changelog entry as described in [workflows.md](workflows.md), and build without
`-AllowUnreleased`. No public repository or release is created by build scripts.

```powershell
./scripts/publish-release.ps1 -Version 1.2.0 # inspect the local upload list
# Only when the reviewed release is ready:
./scripts/publish-release.ps1 -Version 1.2.0 -ConfirmPublish
```

Both commands accept `-AssetDirectory /path/to/extracted/artifact` and
`-ReleaseRepository owner/repository`. The repository must match the manifest
URLs. Preview performs only local checks; it needs no authentication or network.
Publication rejects unpublished preview notes, private destinations and existing
releases. It creates a draft with an explicit binary/document allowlist, verifies
the uploaded names, sizes and GitHub SHA-256 digests, then marks that draft public
and latest. Failures leave any created draft unpublished for inspection. It does
not stage/commit source, push source branches, force tags or overwrite releases.

The public repository's generated source archives contain only what is in that
public repository. Keep application source and protection maps in the private
source repository. Verify the actual Windows install/restart and native desktop
launches before publication; script checks cannot establish those behaviors.
