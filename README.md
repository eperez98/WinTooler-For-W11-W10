<div align="center">

# WinToolerV1

**v0.5 BETA RELEASE**

A modern Windows 10/11 optimization, debloat and app management utility  
built with PowerShell 5.1 and a native WPF GUI — no .NET 6+, no runtimes, no bloat.

Made by **[ErickP (Eperez98)](https://github.com/eperez98)**  
Inspired by [ChrisTitusTech/winutil](https://github.com/christitustech/winutil)

![Platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-0078D4?style=flat-square&logo=windows)
![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-blue?style=flat-square&logo=powershell)
![Version](https://img.shields.io/badge/version-v0.5%20BETA-orange?style=flat-square)
![License](https://img.shields.io/badge/license-GPL--3.0-blue?style=flat-square)

</div>

---

## Screenshots

| Startup Screen | Install Apps | Tweaks |
|:-:|:-:|:-:|
| Language + Theme picker on first launch | 111 apps, category filter, live search | Templates: Minimal / Standard / Heavy |

| App Updates | Repair | Dark / Light |
|:-:|:-:|:-:|
| Scanned at boot, shown before GUI opens | Async SFC+DISM, live output | One-click theme toggle in sidebar |

---

## Quick Start

> **Requirements:** Windows 10/11 · PowerShell 5.1+ · Administrator rights

1. Download and extract **WinToolerV1.zip** anywhere
2. Right-click **`Launch.bat`** → **Run as administrator**
3. Click **Yes** on the UAC prompt
4. The CLI window completes the boot sequence — then the **startup screen** appears
5. Pick your **language** (English / Español) and **theme** (Dark / Light)
6. Click **Launch WinToolerV1**

<details>
<summary>Alternative — PowerShell directly</summary>

```powershell
# Open PowerShell as Administrator, cd to the folder, then:
Set-ExecutionPolicy Bypass -Scope Process -Force
.\WinToolerV1.ps1
```

</details>

---

## GUI Boot Sequence

Every run prints a full status table to the CLI window **before** the GUI opens:

| Step | Stage | What Happens |
|:----:|-------|--------------|
| 1 | STA Thread Check | Detects MTA mode — auto-restarts with `-STA` flag if needed (WPF requirement) |
| 2 | Root Detection | 3-tier path fallback so the script works from any launch method |
| 3 | Admin Verification | Confirms Administrator rights — exits cleanly if not |
| 4 | **Restore Point** | Creates a system restore point before touching anything. Bypasses Windows' 24hr cooldown automatically |
| 5 | winget Bootstrap | Locates winget. If missing: downloads VCLibs + UI.Xaml prereqs, fetches latest `.msixbundle` from GitHub |
| 6 | NuGet Provider | Downloads the DLL directly from Microsoft CDN — **no interactive prompt** |
| 7 | PSWindowsUpdate | Installs from PSGallery if absent |
| 8 | Execution Policy | Relaxes to `RemoteSigned` if set to `Restricted` or `AllSigned` |
| 9 | .NET Framework | Reads registry release key to detect version (4.5 → 4.8.1) |
| 10 | winget Sources | Runs `winget source update` to refresh package metadata |
| **11** | **App Update Scan** | Runs `winget upgrade`, parses all available updates, stores in memory for the App Updates tab |
| 12 | Module Loading | Dot-sources `repair.ps1`, `tweaks.ps1`, `updates.ps1` |
| 13 | Config Loading | Loads `apps.json` (111 apps), `tweaks.json` (23 tweaks), `services.json` (18 services) |
| 14 | **Startup Screen** | Language + Theme picker appears. Your choice applies instantly to the full GUI |

---

## Dark Mode / Light Mode

Click the **`☽` / `☀` button** in the sidebar header to toggle themes at any time — no restart needed.

| | Dark Mode `☽` | Light Mode `☀` |
|---|---|---|
| **Background** | `#0F0F0F` | `#F3F3F3` |
| **Surface** | `#161616` / `#1E1E1E` | `#FFFFFF` / `#F7F7F7` |
| **Accent** | `#0078D4` (Windows Blue) | `#0067C0` (Classic Blue) |
| **Best for** | Low-light / night use | Bright environments |

Theme is also selectable on the **startup screen** before the GUI loads.

---

## Language / Idioma

Language is selected on the **startup screen**. The setting applies to all tab labels, button text, tweak descriptions, status messages and the status bar.

| 🇺🇸 English | 🇪🇸 Español |
|---|---|
| Default language | Traduccion completa de la interfaz |
| All UI text in English | Etiquetas, botones y mensajes en espanol |

---

## Features

### Install Apps
- **111 apps** across 10 categories: Browsers, Communications, Development, Document, Gaming, Media, Network, Productivity, Security, Utilities
- Category sidebar with colour-coded dots
- Live search filter
- macOS-style icon tiles with shine overlay
- Per-app status badges: Installing → Installed / Already Present / Not Found / Failed
- Scope retry: auto-retries with `--scope user` on scope errors
- Progress bar with live count

### Uninstall Apps
- Queries `winget list` live — shows **all** winget-managed apps currently installed
- Search filter
- Checkbox multi-select → silent bulk uninstall

### App Updates
- Scanned at **boot time in the CLI** before the GUI opens
- Red badge on the nav item shows update count
- Lists: app ID · current version → available version
- **Update All** / **Update Selected** / **Re-Check** buttons
- Smart retry on `-1978335189` (version mismatch) with `--force`
- Cache-clear retry on `-1978335230` (hash mismatch)

### Tweaks
23 tweaks across four categories with **risk badges** (Low / Medium / High):

| Category | Examples |
|---|---|
| Performance | High Performance Power Plan, Disable SysMain, Reduce Animations, Game Mode + HAGS |
| Privacy | Disable Telemetry, Block Telemetry Hosts, Disable Advertising ID, Disable Activity History |
| UI & Explorer | Dark Mode, Show Extensions, Remove Start Ads, Clean Taskbar, Disable Bing Search |
| Bloatware | Remove MS Bloatware, Remove Xbox Apps, Disable Edge Bloat, Disable OneDrive |

**Preset Templates:**

| Template | Tweaks | Description |
|---|:---:|---|
| None | 0 | Clears all selections |
| Minimal | 6 | Privacy basics only — safe for any system |
| Standard | 13 | Recommended daily-driver set |
| Heavy ⚠️ | 20 | Aggressive — removes bloatware (irreversible) |

### Services
- 18 Windows services with live status badges (Automatic / Manual / Disabled)
- Bulk **Disable** / **Set Manual** / **Re-Enable**

### Repair & Maintenance
- **SFC + DISM** — async background job, streams live output to console, GUI stays fully responsive
- **Clear Temp** — cleans `%TEMP%`, `C:\Windows\Temp`, `C:\Windows\Prefetch`
- **Flush DNS** — clears resolver cache
- **Reset Store** — `wsreset.exe`
- **Restore Point** — create a snapshot on demand
- **Network Reset** — Winsock + TCP/IP stack, clean summary output

### Windows Update
- Check and install all updates via **PSWindowsUpdate**
- Pause updates for 7 days
- Resume updates

---

## Project Structure

```
WinToolerV1\
  WinToolerV1.ps1        <- Main launcher + 14-step boot sequence
  Launch.bat             <- Double-click to run as Admin (-STA flag)
  README.md
  scripts\
    gui.ps1              <- Full WPF GUI + startup screen (2500+ lines)
  functions\
    tweaks.ps1           <- 23 tweak functions
    repair.ps1           <- Async SFC/DISM, DNS, temp, network, restore
    updates.ps1          <- PSWindowsUpdate integration
  config\
    apps.json            <- 111 apps across 10 categories (editable)
    tweaks.json          <- 23 tweaks with risk levels (editable)
    services.json        <- 18 Windows services (editable)
```

---

## Adding Custom Apps

Open `config\apps.json` and append:

```json
{
  "Category": "Utilities",
  "Name":     "My App",
  "Id":       "Publisher.AppName",
  "Description": "Short description",
  "Icon":     "U",
  "Color":    "#0078D4",
  "Selected": false
}
```

Find the winget ID with:

```powershell
winget search "app name"
```

---

## Winget Exit Codes Reference

| Code | Meaning | Handled |
|---|---|:---:|
| `0` | Success | ✅ |
| `-1978335189` | No applicable update (version mismatch) | ✅ Auto-retried with `--force` |
| `-1978335230` | Installer hash mismatch | ✅ Cache cleared + retried |
| `-1978335212` | Hash mismatch variant | ⚠️ Flagged, skip and retry later |
| `-1978335138` | Scope error (machine) | ✅ Auto-retried with `--scope user` |
| `-1978335216` | Package not found in any source | ❌ Reported as not found |

---

## Requirements

| Requirement | Details |
|---|---|
| OS | Windows 10 (build 1809+) or Windows 11 |
| PowerShell | 5.1+ (built into Windows — no install needed) |
| Privileges | Administrator (UAC prompt on launch) |
| Network | Only needed for Install, App Updates, and Windows Update tabs |
| winget | Auto-installed if missing |
| NuGet provider | Auto-installed via direct DLL download (no prompt) |
| PSWindowsUpdate | Auto-installed from PSGallery if missing |

---

## Disclaimer

> **v0.5 BETA** — Features and configurations may change before the stable v1.0 release.  
> The **Heavy** tweak template removes apps permanently. A restore point is always created at boot.  
> Use at your own risk on production systems.

---

## License

[GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html) — You are free to use, modify and distribute this software. Any derivative work must also be released under GPL-3.0 and remain open source.

---

<div align="center">

Made by **[ErickP (Eperez98)](https://github.com/eperez98)**

*If this tool helped you, consider starring the repo!*

</div>
