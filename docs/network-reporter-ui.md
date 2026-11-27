# Network Health Reporter UI transfer

Reference: `Majordean0303/Network-Health-Reporter` main at
`3e1549f6870f59de3ad5287fe03dd6053ef2647b` (including the v2.2.0 UI fixes).
This application remains C#/.NET 10 and Avalonia; no Python UI is introduced.

| Reference fix | Analyzer implementation |
| --- | --- |
| Title-bar spacing and native window controls | 38px title bar; macOS leading and Windows trailing clearance; compact 28px actions |
| Zoom controls and drag conflicts | Dedicated drag/double-tap title region, isolated zoom actions, Ctrl +/-/0, 70–150% scale |
| Small/high-DPI display sizing | Physical working area converted to logical units, window fitted to screen; panels stack based on available width after zoom |
| Theme contrast and bundled typography | Dynamic light/dark brushes, explicit hover/pressed accent foregrounds, Inter font, System/Light/Dark selection |
| Rounded badges and grouped actions | Theme-aware badges, responsive toolbars and finding status labels |
| Table readability and long names | Star-sized resizable columns with minimum widths, automatic row heights, wrapped text and horizontal scrolling |
| Full empty state | Entire finding layout hidden when no findings match; guidance points to coverage |
| Finding inspector | Separate observation, affected context and verification cards; selectable text; copy checks / inspection; raw output retained in dedicated source viewer |
| Larger useful viewport | Collapsible capture context and section summary; responsive sidebars; scrollable inspector and About page |
| About, changelog and update details | Build/platform details, embedded changelog, feed settings, release-note preview, explicit check/download/install in both native desktop editions |
| Update resilience | Proxy-aware HTTPS, bounded transfers, cancellation, semantic version ordering, checksums, file-type validation and explicit installer handoff |

Topology canvas routing, port bubbles, WLC/AP diagrams, live discovery controls,
Excel audit layout and Network Health Reporter's domain-specific tabs do not
exist in this analyzer. Their fixes have no matching UI here. The analyzer keeps
its troubleshooting dashboards and exact command-line evidence navigation.

Validation uses synthetic captures: light/dark rendering, compact panels at
150% zoom, wide tables, title-bar actions, empty-state behavior and evidence
navigation. Native system chrome, monitor transitions, clipboard integration and
an end-to-end Windows install/restart still require interactive platform checks.
