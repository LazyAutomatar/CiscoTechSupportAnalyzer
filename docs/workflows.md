# GitHub Actions workflow configuration

The source repository is `Majordean0303/CiscoTechSupportAnalyzer`. The planned
public binary release repository is `LazyAutomatar/CiscoTechSupportAnalyzer`.
The source workflows build artifacts; they do not create that repository or
publish releases to it.

## Repository settings

1. In the source repository, enable **Settings → Actions → General** and allow
   the `actions/checkout`, `actions/setup-dotnet`, `actions/upload-artifact` and
   `actions/download-artifact` actions used by the workflow files.
2. Keep **Workflow permissions** set to read repository contents. The workflow
   files explicitly request `contents: read`.
3. Under **Settings → Secrets and variables → Actions → Variables**, optionally
   add `RELEASE_REPOSITORY` with an `owner/repository` value. Use
   `LazyAutomatar/CiscoTechSupportAnalyzer` for the planned release location.
4. Ensure GitHub-hosted Windows, macOS and Ubuntu runners are available to the
   repository. Private-repository Actions usage is subject to the account's
   billing limits and artifact quota.

No secrets, deployment environments, release token or cloud credentials are
needed by these workflows. Publishing later uses an authorized maintainer's
GitHub account; the source repository's `GITHUB_TOKEN` is not a cross-repository
publication credential.

## Application validation

File: [`.github/workflows/validation.yml`](https://github.com/Majordean0303/CiscoTechSupportAnalyzer/blob/main/.github/workflows/validation.yml).

| Setting | Value |
| --- | --- |
| Events | Pull requests; pushes to `main` and `migration/avalonia/**` |
| Runners | `ubuntu-latest`, `windows-latest`, `macos-15-intel` |
| SDK | .NET `10.0.x` |
| Shell | PowerShell 7 (`pwsh`) |
| Test command | `./scripts/test-all.ps1` |
| Package checks | Linux x64, Windows x64 installer/ZIP, macOS x64 archive |
| Outputs | `validation-ubuntu-latest`, `validation-windows-latest`, `validation-macos-15-intel` |

Each job runs the core and UI tests, builds CLI/desktop projects, verifies the
manifest generator and release helpers, builds a protected native package and
verifies its update manifest and archive contents. Core tests also run against
the obfuscated assembly before packaging can succeed.
The Windows runner supplies Inno Setup 6. Artifacts include logs, TRX results and
packages; failed jobs still attempt to upload their diagnostic artifacts.
Protection maps/configuration are excluded from uploaded validation artifacts.
Actions are pinned to commit IDs and dependency restores use committed lock files.
macOS builds use `macos-15-intel` because the ARM64 hosted pool was failing to
acquire jobs; the five-platform packaging workflow cross-compiles both macOS
architectures on that runner. ARM64 launch acceptance remains a separate check.

This workflow validates the three representative platform packages; it does
not claim runtime testing of all five release architectures or execute an
installer. Duplicate validation runs for the same branch/PR are canceled when a
new revision arrives. If using branch protection, select all three
`core-and-desktop (...)` checks as required checks.

## Build update packages

File: [`.github/workflows/package.yml`](https://github.com/Majordean0303/CiscoTechSupportAnalyzer/blob/main/.github/workflows/package.yml).

On GitHub, open **Actions → Build update packages → Run workflow**, choose the
source branch or tag, and supply:

| Input / variable | Required | Meaning |
| --- | --- | --- |
| `version` | Yes | Stable semantic version `X.Y.Z`, without a leading `v`; embedded in the binaries and filenames |
| `release_repository` | No | `owner/repository` used for manifest download URLs; overrides the repository variable |
| `RELEASE_REPOSITORY` | No | Actions repository variable used when the input is blank |
| `use_unreleased_notes` | No | Defaults to true for unpublished preview builds; disable for release preparation so the version must exist in the changelog |

Resolution order: manual `release_repository` input, then repository variable,
then `LazyAutomatar/CiscoTechSupportAnalyzer`. Enter the actual destination
before building; changing a repository variable does not rewrite an existing
manifest. This configuration also does not change `UpdateViewModel.DefaultFeed`
in the application; users can edit the feed in About, or maintainers can update
the default in C# for a future build.

The workflow builds `win-x64`, `linux-x64`, `linux-arm64`, `osx-x64` and
`osx-arm64`, then generates a manifest using the actual package sizes and hashes.
The Windows installer is preferred over its portable ZIP in the manifest.
Each package contains the README, EULA, changelog and documentation folder.
The final artifact also includes those documents beside the release assets.
Missing package files fail artifact upload instead of producing a successful
empty release bundle. Each build obfuscates the core and validates the transformed
assembly; source, PDBs and name maps are excluded from release packages. The final
manifest job verifies hashes, required documents and archive paths. See
[SECURITY.md](../SECURITY.md) for the limits of offline code protection.

Download the **release-assets-and-manifest** artifact from the completed run and
extract it to an empty folder. Individual `package-<runtime>` artifacts are also
available. Workflow artifacts expire according to repository retention settings;
they are not durable public downloads and cannot be used directly as update-feed
URLs. The manifest and `release-notes.md` contain only the selected version's
entry from `CHANGELOG.md`. The complete changelog remains in the packages and
the final artifact. If preview mode falls back to Unreleased, its notes are
explicitly labeled as an unpublished build.

Equivalent invocation using GitHub CLI:

```sh
gh workflow run package.yml --repo Majordean0303/CiscoTechSupportAnalyzer \
  --ref main -f version=1.2.0 \
  -f release_repository=LazyAutomatar/CiscoTechSupportAnalyzer \
  -f use_unreleased_notes=false
```

Run this only when ready to build the chosen version. It starts packaging; it
does not publish anything.

## Publish the completed assets later

Create the public release repository when ready and initialize its default
branch (for example with a README). Download the final artifact
and verify the package list, version and manifest URLs. Review the bundled EULA
and release notes. Use the compiled Windows installer and launch checks as
release acceptance checks; a successful compile does not validate installation.

Use the [Reporter-style scripts](scripts.md) from the private source checkout.
The publication helper verifies local hashes, URLs, archive contents and required
documents before showing the exact upload list:

```powershell
./scripts/publish-release.ps1 -Version 1.2.0 -AssetDirectory /path/to/extracted/artifact
# After reviewing assets and completing native acceptance checks:
./scripts/publish-release.ps1 -Version 1.2.0 -AssetDirectory /path/to/extracted/artifact -ConfirmPublish
```

The second command uses the authenticated maintainer's GitHub CLI account. It
creates a draft, uploads only selected binaries/documents, verifies uploaded
sizes and SHA-256 digests, and publishes the verified draft. It refuses existing
releases and unpublished preview notes. A failure leaves any created draft
unpublished for inspection; it never pushes the private source repository.

The tag must exactly match the manifest's `releases/download/vX.Y.Z/` paths.
Upload every package referenced by the manifest. Keep the EULA available with
portable packages and the release. The interactive Windows installer displays
the EULA; silent updates do not display its page. If a future release changes
terms requiring fresh acceptance, distribute an interactive installer rather
than relying on the current silent update flow.

Until a public release containing `update_manifest.json` is published, the
application reports the default feed as unavailable. A release older than or
equal to the installed version does not offer an update.

## Maintaining the changelog

Follow Network Health Reporter's Keep a Changelog / Semantic Versioning style.
Add user-visible changes to `[Unreleased]` in every relevant pull request, under
`Added`, `Changed`, `Deprecated`, `Removed`, `Fixed` or `Security` as applicable.
Use short descriptive labels and explain the resulting behavior; do not turn
the changelog into a commit log. Keep prior version entries intact.

Before preparing a release:

1. Choose `X.Y.Z` according to the scope of the changes. Keep the application
   project version, packaging defaults and workflow version defaults consistent
   when moving the baseline forward; a workflow input overrides the package
   build version without editing source defaults.
2. Move that release's entries into `## [X.Y.Z] - YYYY-MM-DD`, retain an
   Unreleased section for subsequent changes, and accurately state publication
   status until the release is public. Do not invent earlier releases.
3. Run **Build update packages** for that version with
   `use_unreleased_notes=false`. Missing, empty or duplicate version sections
   fail before compilation. A matching version is always preferred to
   Unreleased, even when preview fallback is enabled.
4. Review `release-notes.md` and the manifest's `releaseNotes`. They should
   contain the chosen version's entries, not older changes or future work.
   The full `CHANGELOG.md` stays in every package and the About page.
5. Publish the same notes with the release and update the changelog's
   publication status accurately. Preview builds using `-AllowUnreleased`
   are labeled unpublished and should not be promoted as final release notes.

`AGENTS.md` records this maintenance rule for future repository work.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| Workflow is missing | Enable Actions and ensure the workflow file exists on the default branch |
| Runner is queued | Check runner availability and the account's Actions usage limits |
| Version input is rejected | Use three numeric components without leading zeroes, for example `1.2.0` |
| Changelog preflight fails | Add the requested version with change entries, remove duplicate headings, or enable Unreleased notes for an unpublished preview |
| Installer compilation fails | Check the Windows logs and Inno Setup 6 installation/path |
| Feed says unavailable | Confirm the repository and release are public and the manifest asset is named `update_manifest.json` |
| Package URL returns 404 | Match destination repository, release tag and asset filenames to the generated manifest |
| Checksum verification fails | Regenerate the manifest after any package change and publish matching assets together |
| No package for this platform | Include that runtime in the release build and regenerate the manifest |
| Release upload is denied | Use an account with write access to the public release repository; source-repository access alone is insufficient |

For local packaging commands and the manifest schema, see [updates.md](updates.md).
