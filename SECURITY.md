# Security and distribution

Analysis runs locally in C#/.NET. Capture contents are not uploaded by the app.
Only an explicit update check/download, or a saved startup-check preference,
contacts the configured update feed. Reports can contain sensitive device data;
review exported files before sharing them.

## Protecting the offline core

Keep the source repository private. Share only the files selected by the release
scripts from `artifacts/release`, never a source checkout or the entire build tree.
Release packaging uses pinned Obfuscar 2.2.50 to rename internal implementation
and hide string literals before bundling the core into the desktop executable.
Public contracts and property names remain compatible with the UI and JSON.
The transformed core must pass the core regression suite before an archive is
created. Packaging fails if protection fails; there is no unprotected fallback.

Source, PDB symbols, protection configuration and name maps are excluded from
public packages. Name maps stay in the private build tree under
`artifacts/protected`; retain them privately if needed for diagnostics.
Normal development builds and the development CLI are not obfuscated.

This raises the effort required for casual decompilation. It does **not** make
offline code non-extractable, prevent determined reverse engineering or encrypt
all runtime data. Single-file .NET bundles can be unpacked. Strong secrecy of an
algorithm would require keeping it off the user's machine. Do not embed secrets
or private keys in either public or obfuscated code.

## Input, exports and updates

- Capture reads reject files over 64 MiB, decoded text over 32 Mi characters,
  lines over 128 Ki characters, over 500,000 lines or over 4,096 sections.
  Parser regular expressions have time limits. These bounds reduce resource
  exhaustion risk; this is not a sandbox for hostile native dependencies.
- HTML exports escape capture text and restrict active content with a CSP.
- Updates require HTTPS, size/hash/type checks and restricted redirect hosts.
  Local manifests must use absolute local paths; UNC/device paths are rejected.
  The Windows installer is reverified while holding a file handle through launch.
- SHA-256 proves consistency with the selected manifest, **not publisher
  identity**. Someone controlling that feed can replace both hashes and files.
  Authenticode signing and macOS notarization are not configured. Use a trusted
  feed; future signing keys belong in a protected signing service or secret store.
- NuGet dependencies have committed lock files and CI uses locked restore.
  Advisory audit failures stop restore; Dependabot checks dependencies weekly.
  GitHub Actions are pinned to commits and use read-only repository permissions.

## Reporting an issue

Use the private source repository's security reporting channel if enabled, or
contact the maintainer privately. Provide the version, platform and minimal
redacted reproduction. Do not post credentials or customer captures publicly.
