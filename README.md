# Cisco Tech Support Analyzer

Assess Cisco IOS and IOS XE switches and routers from saved `show tech-support` captures or read-only SSH sessions. Audit device health, inspect the exact CLI evidence behind every warning, explore parsed diagnostic tables, and export presentation-ready reports.

[![Version](https://img.shields.io/badge/version-2.3.2-0078D4.svg)](CHANGELOG.md)
[![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-555555.svg)](#download)
[![License](https://img.shields.io/badge/license-Freeware-brightgreen.svg)](LICENSE.md)
[![Developer](https://img.shields.io/badge/developer-Yash%20Nalawde-blue.svg)](#support-and-license)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy%20Me%20a%20Coffee-Support-FFDD00?logo=buy-me-a-coffee&logoColor=000000)](https://buymeacoffee.com/yashn0303)

---

[Download](#download) · [Quickstart](#run-your-first-assessment) · [Capture & SSH](#bring-your-own-capture) · [Alert Coverage](#alert-coverage) · [Diagnostics Explorer](#diagnostics-explorer) · [Reports](#reports) · [Changelog](CHANGELOG.md)

![Sample switch dashboard showing health scorecard, barometers, and prioritized findings](docs/images/sample-dashboard.jpg)

*Dashboard is the opening workspace. Load a saved capture, connect over live SSH, or try the built-in sample switch to evaluate device health and trace prioritized findings.*

> [!NOTE]
> **What's New in v2.3.0**
> - **Auto-Update & Refined Upgrade Flow:** Non-blocking startup update checks with a title-bar `Update available` pill, optional background download of verified releases, and one-click install & restart.
> - **Health Scorecard Dashboard:** Executive health status banner (`HEALTHY`, `ATTENTION REQUIRED`, `CRITICAL`), three at-a-glance health barometers, quick-stat KPI cards, and one-click `CLI Evidence` inspection.
> - **Streamlined Capture Workspace:** Two-column offline log intake with file readiness indicators alongside automated Live SSH collection.
> - **Presentation-Grade Reports & Multi-Platform Releases:** Upgraded PDF/Word/HTML/JSON exports and self-contained packages for Windows, macOS, and Linux.
> - [View complete changes in the Changelog](CHANGELOG.md)

---

<a id="how-to-build--run"></a>
<a id="running-the-standalone-executable"></a>

## Download

Choose the package for your operating system. All downloads are self-contained with no separate runtime installation required.

| Platform | Architecture | Package | How to Start |
| :--- | :---: | :--- | :--- |
| **Windows** | x64 (Installer) | `CiscoTechSupportAnalyzer-2.3.2-win-x64-setup.exe` | Run the setup wizard; launches from the Start menu or optional Desktop shortcut. |
| **Windows** | x64 (Portable) | `CiscoTechSupportAnalyzer-2.3.2-win-x64.zip` | Extract the ZIP to a folder and run `CiscoTechSupportAnalyzer.WPF.exe`. |
| **macOS** | Apple Silicon (ARM64) | `CiscoTechSupportAnalyzer-2.3.2-osx-arm64.tar.gz` | Extract the archive and run `./TechSupport.Desktop` from Terminal. |
| **macOS** | Intel (x64) | `CiscoTechSupportAnalyzer-2.3.2-osx-x64.tar.gz` | Extract the archive and run `./TechSupport.Desktop` from Terminal. |
| **Linux** | x64 | `CiscoTechSupportAnalyzer-2.3.2-linux-x64.tar.gz` | Extract the archive and run `./TechSupport.Desktop`. |
| **Linux** | ARM64 | `CiscoTechSupportAnalyzer-2.3.2-linux-arm64.tar.gz` | Extract the archive and run `./TechSupport.Desktop`. |

[View all available release packages and checksums](https://github.com/LazyAutomatar/CiscoTechSupportAnalyzer/releases)

> [!TIP]
> - **Appearance & Navigation:** Switch between Dark Mode and Light Mode from the title bar toggle. Use <kbd>Ctrl</kbd> + <kbd>1</kbd> through <kbd>Ctrl</kbd> + <kbd>6</kbd> to jump between Dashboard, Capture, Alerts, Diagnostics Explorer, Raw Evidence, and Reports.
> - **Windows SmartScreen:** If Windows prompts on an unsigned local/community installer, choose *More info → Run anyway*.
> - **About & Updates:** Open **About & System** in the sidebar footer to view build details, check the release feed for updates, and browse the bundled offline changelog.

---

## Run your first assessment

```text
  ┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
  │  1. Load Capture    │ ───► │  2. Review Alerts   │ ───► │  3. Explore Health  │ ───► │  4. Export Reports  │
  │  Files or Live SSH  │      │  Prioritized Risks  │      │  Device & L2 Views  │      │  PDF, DOCX, HTML    │
  └─────────────────────┘      └─────────────────────┘      └─────────────────────┘      └─────────────────────┘
```

You can test the complete workflow using the built-in sample capture before connecting to a switch or importing real logs:

1. On **Dashboard**, select **Load sample capture**. The sample switch loads with pre-populated health metrics, barometers, and prioritized findings.
2. Select **Review alerts** (or press <kbd>Ctrl</kbd> + <kbd>3</kbd>) and search for `Gi1/0/12`. Two warnings reference that port—one for err-disabled shutdown and one for a Power over Ethernet (PoE) fault.
3. Click the PoE warning to open its detail overlay and select **Open in Raw Evidence**. The viewer jumps directly to the matching `show power inline` output showing `faulty`, `0.0` watts drawn, and `Short circuit`.
4. Return to **Alerts** and inspect the err-disabled warning to confirm **BPDU Guard** as the shutdown cause with verified CLI evidence for both faults.

---

## Bring your own capture

Navigate to **Capture** (<kbd>Ctrl</kbd> + <kbd>2</kbd>) and choose your collection method:

<!-- SCREENSHOT INPUT: Capture Workspace (v2.2.0)
     Save screenshot to: docs/images/sample-capture.png
     Then uncomment the line below:
![Two-column Capture workspace showing Air-Gapped log intake and Live SSH collection](docs/images/sample-capture.png)
-->

- **Log files (Air-Gapped Assessment):** Click **Browse** to select `.txt`, `.log`, `.cfg`, or `.zip` files from a single device and collection session, verify readiness in the right-hand launch card, and click **Run Diagnostic Assessment →**.
- **Live SSH:** Enter the switch IP or hostname, SSH port (`22`), username, password, and optional enable secret. Choose targeted diagnostic profiling or a full `show tech-support` capture, then select **Run Live Network Assessment →**.
- **Required CLI Commands Guide:** If you are collecting terminal output manually, open **Capture → Required CLI Commands Guide** to copy the recommended commands.

> [!IMPORTANT]
> **Capture best practices:**
> - Run `terminal length 0` before collecting manual CLI logs so `--More--` prompts do not truncate output.
> - Keep command headings intact (for example, `Switch# show power inline` or `------------------ show running-config ------------------`). If **Raw evidence** shows only a single `show tech-support` block, verify that command headers were preserved in the file.

---

## Alert coverage

The analyzer highlights actionable device risks across five operational areas so engineers can quickly triage security exposures, link issues, hardware faults, and resource pressure.

| Area | Use Cases & What You Can Investigate |
| :--- | :--- |
| **Configuration and management** | Identify insecure remote access (Telnet, HTTP server), weak SNMP communities, missing AAA, and cleartext credentials. |
| **Interfaces and Layer 2** | Catch err-disabled ports, half-duplex links, missing or suspended access VLANs, native VLAN mismatches, DTP negotiation, and MAC flaps. |
| **Power, stack and sensors** | Spot PoE inline faults, low PoE budget headroom, StackWise half-ring or member sync issues, and PSU or thermal sensor faults. |
| **Resources and storage** | Detect low flash filesystem space, buffer pool allocation failures, high CPU load, and ASIC TCAM utilization pressure. |
| **Reliability and logs** | Review abnormal reload history, crashinfo artifacts, clock synchronization gaps, and Smart Licensing communication failures. |

If a capture produces **zero findings**, review **Reports → Evidence and coverage** to confirm which baseline commands were present or missing in your capture. For command coverage details, see [Evidence used by the checks](docs/capture-assessment.md).

---

## Trace a finding

In **Alerts**, filter by severity (`All`, `Critical`, `Warning`, `Info`) or search by interface, VLAN, rule ID, or command. Selecting any row opens the detail overlay with the triggered CLI snippet, potential impact, engineering observation, and verification commands.

![PoE warning with the affected port, the source line and three commands to verify it](docs/images/sample-alert.jpg)

- **Verify timing:** An err-disabled status reflects the state at capture time, whereas syslog entries record historical events. Compare log timestamps against interface tables before escalating.
- **Jump to raw evidence:** Click **Open in Raw Evidence** inside any alert card to open **Raw evidence** (<kbd>Ctrl</kbd> + <kbd>5</kbd>) scrolled directly to the matching command section and line number.

<!-- SCREENSHOT INPUT: Raw Evidence Jump (v2.2.0)
     Save screenshot to: docs/images/sample-raw-evidence.png
     Then uncomment the line below:
![Raw Evidence viewer jumped directly to the show power inline output for Gi1/0/12](docs/images/sample-raw-evidence.png)
-->
- **Hand off next checks:** Use **Copy summary** for ticket notes or **Copy checks** to copy read-only verification CLI commands for live device triage.

---

## Diagnostics explorer

Open **Diagnostics Explorer** (<kbd>Ctrl</kbd> + <kbd>4</kbd>) to inspect operational switch data organized into five practical views:

<!-- SCREENSHOT INPUT: Diagnostics Explorer (v2.2.0)
     Save screenshot to: docs/images/sample-diagnostics.png
     Then uncomment the line below:
![Diagnostics Explorer showing organized operational tables across interfaces, hardware, and resources](docs/images/sample-diagnostics.png)
-->

| View | Operational Use Case |
| :--- | :--- |
| **Interfaces & Layer 2** | Review interface status, err-disabled ports, packet drops/anomalies, MAC flaps, MAC address tables, Spanning Tree, and CDP neighbors. |
| **Hardware & Power** | Check hardware modules, power supply redundancy, PoE inline budgets, optical transceivers, environmental sensors, and StackWise status. |
| **CPU, Memory & Storage** | Monitor CPU utilization, memory pools, flash storage capacity, top processes, and TCAM utilization. |
| **Redundancy & Crashes** | Audit reload history, crashinfo files, and chassis redundancy state. |
| **Logging & Licenses** | Review retained syslog activity, NTP/clock synchronization, and Smart Licensing status. |

---

## Reports

Open **Reports** (<kbd>Ctrl</kbd> + <kbd>6</kbd>) to review **Evidence and coverage** and export finished handovers:

| Deliverable | Format | Use Case |
| :--- | :---: | :--- |
| **Assessment PDF** | `.pdf` | Executive and customer handover report with health scorecard, severity summary, device telemetry, CLI evidence, and coverage notes. |
| **Editable Word** | `.docx` | Customizable Microsoft Word document for adding team commentary, remediation owners, or change-window notes. |
| **Standalone HTML** | `.html` | Self-contained browser-readable report for easy offline sharing and quick evidence navigation. |
| **Structured JSON** | `.json` | Machine-readable export for ticket automation, SIEM/CMDB workflows, or custom internal tooling. |

> [!CAUTION]
> **Sanitize before external sharing:**
> HTML and JSON exports include full captured command sections. If your capture contains `show running-config`, review and redact credentials or sensitive site details before distributing externally.

<details>
<summary><b>See the first page of the sample PDF</b></summary>

![Sample PDF showing the switch identity, warning counts, memory, storage and configuration checks](docs/images/sample-report.png)

</details>

---

<a id="about-updates-and-support"></a>

## Support and license

Cisco Tech Support Analyzer is closed-source freeware created and maintained by **Yash Nalawde**. Personal and commercial use and redistribution of unmodified official packages are permitted under the terms of the [EULA](EULA.txt) and [License](LICENSE.md).

- **Bug Reports & Feature Requests:** Include the application version, finding title, command name, and a short redacted CLI excerpt. [GitHub Issues](https://github.com/LazyAutomatar/CiscoTechSupportAnalyzer/issues) · [Security Reporting](SECURITY.md) · [Changelog](CHANGELOG.md)
- **Connect & Contribute:** Want to collaborate, share real-world Cisco diagnostic edge cases, or contribute ideas to the project? Reach out directly via [LinkedIn](https://www.linkedin.com/in/yash-nalawde), connect on [GitHub](https://github.com/LazyAutomatar), or open a discussion/issue in the repository.
- **Support the Project:** [Buy Me a Coffee](https://buymeacoffee.com/yashn0303)
