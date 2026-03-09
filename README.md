<div align="center">

<img src="https://img.shields.io/badge/WinToolerV1-v0.6%20BETA%20Build%204.034-0067C0?style=for-the-badge&logo=windows&logoColor=white"/>

**A modern Windows 10/11 optimization and app management utility**
Built with PowerShell 5.1 and a native WPF GUI — no .NET 6+, no runtimes, no dependencies to install.

Made by **[ErickP (Eperez98)](https://github.com/eperez98)**
Inspired by [ChrisTitusTech/winutil](https://github.com/christitustech/winutil)

![Platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-0078D4?style=flat-square&logo=windows)
![PowerShell](https://img.shields.io/badge/PowerShell-5.1%2B-blue?style=flat-square&logo=powershell)
![Version](https://img.shields.io/badge/version-v0.6%20BETA%20Build%204.034-orange?style=flat-square)
![License](https://img.shields.io/badge/license-GPL--3.0-green?style=flat-square)

</div>

---

## Version History

| Version | Status | Summary |
|---|---|---|
| **v0.6 BETA Build 4.034** | ✅ Current | Fluent Design UI overhaul, DNS Changer tab, 3 crash fixes from Build 4.033 |
| v0.6 BETA | 🔶 Superseded — crash bugs | GUI crash on load (trailing comma), double RowDefinitions, CharacterSpacing UWP property |
| v0.5 BETA | 🔴 Outdated — multiple bugs | Button clipping, theme icon entity bug, incomplete light mode, ISO downloader frozen |

> **Always use the latest build.** Each build fixes crashes present in the previous one.

---

## Table of Contents

- [Quick Start](#quick-start)
- [What Changed: v0.5 → v0.6 → Build 4.034](#what-changed-v05--v06--build-4034)
- [Bug Report and Fix Log](#bug-report-and-fix-log)
- [Known Issues in Build 4.034](#known-issues-in-build-4034)
- [Features](#features)
- [Roadmap — v0.6.1 BETA](#roadmap--v061-beta)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Known Limitations](#known-limitations)
- [License](#license)

---

## Quick Start

> **Requirements:** Windows 10/11 · PowerShell 5.1+ · Administrator rights

1. Download and extract **WinToolerV1_v06_BETA_BUILD4034.zip** from [Releases](../../releases)
2. Right-click **`Launch.bat`** and select **Run as administrator**
3. Accept the UAC prompt
4. Wait for the CLI boot sequence (14 steps, roughly 10 seconds)
5. Pick your **language** on the startup screen
6. Click **Launch WinToolerV1**

<details>
<summary>Alternative — run from PowerShell directly</summary>

```powershell
# Open PowerShell as Administrator, then:
Set-ExecutionPolicy Bypass -Scope Process -Force
cd "C:\path\to\WinToolerV1_BUILD4"
.\WinToolerV1.ps1
```

</details>

---

## What Changed: v0.5 → v0.6 → Build 4.034

### v0.5 BETA → v0.6 BETA

| Change | Details |
|---|---|
| Startup screen rebuilt | Language-only picker. No more clipped Launch button. Auto-sizes to content at any DPI. |
| Full light mode | Every control, panel, toolbar, card and label correctly displays in light colors. |
| ISO Downloader removed | Feature removed — unreliable across network environments due to Microsoft API session requirements. |
| Nav active state fixed | Active tab highlight correctly reflects the open tab. Was stuck on dark blue in light mode. |
| Dynamic panel theming | App tiles, tweak cards, service rows repainted correctly on theme change. |
| Dark mode removed | Light mode only. Eliminates an entire class of theme-related bugs permanently. |
| Output consoles | Repair and Update output now shows readable dark text on light background. |

---

### v0.6 BETA → v0.6 BETA Build 4.034

| Change | Details |
|---|---|
| **Fluent Design UI** | New visual language based on Windows 11 Fluent Design — left accent bar on active nav, card drop shadows, hover blue tints, refined input focus rings, slimmer scrollbars, updated color palette (`#F0F2F5` window background). |
| **DNS Changer tab** | New tab with 10 DNS providers (Google, Cloudflare, Cloudflare Malware, OpenDNS, Quad9, AdGuard, NextDNS, Comodo, Level3, DHCP reset). Click any card to apply to all active adapters instantly with live output log. **Work in progress — see Known Issues.** |
| **Sidebar redesigned** | Compact header with app icon, title, and subtitle. Section labels updated. Footer card uses Fluent shadow. |
| **Button styles updated** | Ghost buttons now white with `#D1D1D1` border, turn blue-tinted on hover. Danger and Success button variants added. |
| **ComboBox styled** | Fluent-compatible ComboBox style with proper border and padding. |
| **Version string updated** | Window title, About card, and boot sequence all show `v0.6 BETA Build 4.034`. |
| **CRASH FIX 1** | Trailing comma in `$names` array caused `Missing expression after ','` and crashed the GUI on every launch. |
| **CRASH FIX 2** | Duplicate `<Grid.RowDefinitions>` block in root Grid caused `'RowDefinitions' property has already been set` on XAML load. |
| **CRASH FIX 3** | `CharacterSpacing` is a UWP/WinUI property that does not exist in WPF. Removed from all three nav section labels. |

---

## Bug Report and Fix Log

### BUG-01 · Startup screen: Launch button clipped at the bottom

**Reported in:** v0.5 BETA &nbsp;|&nbsp; **Status:** Fixed in v0.6 BETA

**What happened:**
The **Launch WinToolerV1** button was partially hidden at the bottom of the startup window. Because `ResizeMode="NoResize"` was set, users could not resize the window to reach it.

**Root cause:**
The button was injected via `Add_ContentRendered` after the window had already been measured and sized. WPF does not re-expand a fixed-height window for runtime content additions.

**Fix in v0.6:**
Button moved into the XAML Grid at declaration time. `SizeToContent="Height"` added so the window always auto-fits at any DPI or scale setting.

---

### BUG-02 · Theme toggle icon showing literal `&#x2600;` text

**Reported in:** v0.5 BETA &nbsp;|&nbsp; **Status:** Fixed in v0.6 BETA (feature removed)

**What happened:**
After clicking the Dark/Light toggle, the button label changed from the sun or moon symbol to the raw XML entity string displayed as plain text.

**Root cause:**
XML entities are decoded by the XAML parser at load time only. Assigning the same string via PowerShell at runtime passes it as a literal — no entity decoding occurs.

```powershell
# v0.5 — broken
$ctrl["ThemeIcon"].Text = if ($dark) { "&#x2600;" } else { "&#x263D;" }

# v0.6 — fixed (then feature removed entirely)
$ctrl["ThemeIcon"].Text = if ($dark) { [char]0x2600 } else { [char]0x263D }
```

**Resolution:** Dark mode and the theme toggle were removed in v0.6. Light mode only.

---

### BUG-03 · Light mode incomplete — most panels staying dark

**Reported in:** v0.5 BETA &nbsp;|&nbsp; **Status:** Fixed in v0.6 BETA

**What happened:**
Switching to light mode changed only the window background and sidebar. All tab content — cards, toolbars, search boxes, output consoles, text labels — remained dark.

**Root cause (two layers):**
Dynamic panels were built in PowerShell with hardcoded dark `FromRgb` values. XAML structural surfaces had hardcoded dark `Background` values with no `x:Name`, so `Apply-Theme` could not reach them.

**Fix in v0.6:**
All XAML dark values replaced at source. Dynamic panel builders updated to read from the `$script:T` theme token palette. `Apply-Theme` extended to repaint all panels in-place. Dark mode then removed entirely.

---

### BUG-04 · ISO Downloader frozen indefinitely

**Reported in:** v0.5 BETA &nbsp;|&nbsp; **Status:** Feature removed in v0.6 BETA

**What happened:**
Clicking **Get Download Link** showed "Fetching..." indefinitely with no result.

**Root cause:**
The Microsoft Software Download API requires an active browser session cookie. A cold script call with no session step returns an empty response. The polling timer waited forever for a job that had already silently failed.

**Resolution:** ISO Downloader tab removed. Will be re-evaluated in a future build.

---

### BUG-05 · Active nav button not reflecting light mode

**Reported in:** v0.5 / v0.6 BETA &nbsp;|&nbsp; **Status:** Fixed in v0.6 BETA

**What happened:**
The active nav button stayed dark blue even in light mode.

**Root cause:**
`NavBtnActive` was a XAML `Style` resource with hardcoded dark colors. WPF style precedence meant runtime property assignments from `Apply-Theme` could not override style-set values.

**Fix in v0.6:**
`switchPage` no longer assigns a style object. It sets `Background`, `Foreground`, and `FontWeight` directly using `$script:T` tokens.

---

### BUG-06 · GUI crash: Missing expression after comma (Build 4.033)

**Reported in:** v0.6 BETA &nbsp;|&nbsp; **Status:** Fixed in Build 4.034

**Error:**
```
At gui.ps1:1185 char:48
+ "AboutTitle","AboutSub","AboutVersion",
Missing expression after ','.
```

**Root cause:**
Trailing comma after the last element of the `$names` array. PowerShell requires the next element to follow a comma — a trailing comma at end of array is a syntax error.

**Fix in Build 4.034:**
Trailing comma removed. `"DNSCardPanel","DNSOutput","DNSStatusTxt"` added as the actual last elements.

---

### BUG-07 · GUI crash: RowDefinitions property already set (Build 4.033)

**Reported in:** v0.6 BETA &nbsp;|&nbsp; **Status:** Fixed in Build 4.034

**Error:**
```
Exception calling "Load": "'RowDefinitions' property has already been set on 'Grid'."
```

**Root cause:**
During the Fluent Design XAML refactor, the new `Window.Resources` block ended with `<Grid><Grid.RowDefinitions>...</Grid.RowDefinitions>` to start the layout — but the original code already had that same `<Grid>` and its `RowDefinitions` block immediately after `</Window.Resources>`. WPF aborted loading because the same property was declared twice on the same element.

**Fix in Build 4.034:**
Duplicate `<Grid.RowDefinitions>` block removed. One declaration remains.

---

### BUG-08 · GUI crash: Unknown member CharacterSpacing (Build 4.033)

**Reported in:** v0.6 BETA &nbsp;|&nbsp; **Status:** Fixed in Build 4.034

**Error:**
```
Exception calling "Load": "Cannot set unknown member
'System.Windows.Controls.TextBlock.CharacterSpacing'."
```

**Root cause:**
`CharacterSpacing` is a WinUI 3 / UWP property. It does not exist on `System.Windows.Controls.TextBlock` in WPF (.NET Framework). WPF throws immediately when it encounters an unknown property on a known type.

**Fix in Build 4.034:**
`CharacterSpacing="80"` removed from all three nav section label `TextBlock` elements.

---

## Known Issues in Build 4.034

| Issue | Affected tab | Severity | Target fix |
|---|---|---|---|
| **DNS Changer tab — Work in Progress** | DNS Changer | Medium | v0.6.1 |
| **Some nav icon backgrounds may appear dark** | All tabs | Low | v0.6.1 |

### DNS Changer — Work in Progress

The DNS Changer tab is functional for applying DNS settings but should be considered a preview in this build. Known gaps include: no visual indication of which provider is currently active, no per-adapter selection (applies to all `Up` adapters at once), and no IPv6 support in the current implementation. The UI card grid also does not scroll on very small window heights. These will all be addressed in v0.6.1.

### Some icons may appear dark in light mode

Certain nav button icon badge backgrounds use hardcoded color values that were not fully migrated to the Fluent Design token system. On some system DPI or accent color configurations the icon background can appear as a dark tint against the white sidebar. This is a cosmetic issue only and does not affect functionality. Full token-based repaint of all icon badges is planned for v0.6.1.

---

## Features

### Install Apps
111 apps across 10 categories: Browsers, Communications, Development, Documents, Gaming, Media, Network, Productivity, Security, Utilities. Category sidebar, live search, icon tiles with per-app status badges, progress bar, smart retry on scope conflicts and hash errors.

### Uninstall Apps
Queries `winget list` live on tab open. Search filter, checkbox multi-select, silent bulk uninstall.

### App Updates
Scanned at boot. Red badge on nav shows count. Update All, Update Selected, Re-Check. Smart retry on version-mismatch and hash errors.

### Tweaks
23 system tweaks with Low, Medium, and High risk labels. Templates: **None**, **Minimal** (6), **Standard** (13), **Heavy** (20 — includes irreversible bloatware removal). Live search, select all, select none.

### Services
18 Windows services with live status badges. Bulk Disable, Set Manual, Re-Enable.

### Repair and Maintenance
**SFC + DISM** (async, live output), **Clear Temp Files**, **Flush DNS**, **Reset Microsoft Store**, **Create Restore Point**, **Network Reset**.

### DNS Changer *(new in Build 4.034 — Work in Progress)*
10 DNS provider cards with one-click apply to all active adapters. Providers: Default DHCP, Google, Cloudflare, Cloudflare Malware, OpenDNS, Quad9, AdGuard, NextDNS, Comodo, Level3. Live output log below the card grid.

### Windows Update
Check and install via PSWindowsUpdate. Pause 7 days. Resume.

### English and Español
Full bilingual UI selected at startup.

---

## Roadmap — v0.6.1 BETA

> v0.6.1 is the next planned release. The focus is on fixing known issues from Build 4.034 and completing in-progress features before any new additions.

### Bug Fixes and Stability

| Fix | Details |
|---|---|
| **DNS tab completion** | Add current-provider highlight, per-adapter selector, IPv6 support, scrollable card grid |
| **Icon badge dark tint** | Migrate all nav icon badge backgrounds to `$script:T` token system so they repaint correctly with the Fluent palette |
| **DNS tab output polish** | Clearer status messages, error handling for no active adapters, confirmation on successful apply |
| **Startup screen language persistence** | Ensure selected language is correctly stored and applied across the full session without edge-case resets |

### Planned New Features

| Feature | Details |
|---|---|
| **Startup Manager** | View, enable, and disable startup programs and scheduled tasks from the GUI |
| **Disk Cleaner** | Scan for junk files, old Windows update caches, WinSxS backups, and Delivery Optimization leftovers with a size preview before cleaning |
| **In-App Language Toggle** | Switch between EN and ES at any time from the sidebar without restarting |
| **Profile Backup** | Export your current tweak and service configuration as a JSON file and restore it on another machine |
| **Driver Updater** | Detect outdated drivers and update via winget or direct vendor sources |

---

## GUI Boot Sequence

| # | Stage | Description |
|:---:|---|---|
| 01 | STA Thread Check | Auto-restarts with `-STA` flag if needed for WPF |
| 02 | Root Detection | 3-tier path fallback: `$PSScriptRoot`, script path, working directory |
| 03 | Admin Verification | Exits cleanly if not elevated |
| 04 | Restore Point | Creates snapshot before any changes; bypasses 24-hour cooldown |
| 05 | winget Bootstrap | Auto-installs winget if missing |
| 06 | NuGet Provider | Silent DLL download — no interactive prompt |
| 07 | PSWindowsUpdate | Auto-installs from PSGallery if missing |
| 08 | Execution Policy | Sets `RemoteSigned` for current process if restricted |
| 09 | .NET Framework | Detects version 4.5 through 4.8.1 |
| 10 | winget Sources | Refreshes package metadata |
| 11 | App Update Scan | Runs `winget upgrade`, stores results for App Updates tab |
| 12 | Module Load | Dot-sources `repair.ps1`, `tweaks.ps1`, `updates.ps1` |
| 13 | Config Load | Loads `apps.json`, `tweaks.json`, `services.json` |
| 14 | Startup Screen | Language picker, then launches main GUI |

---

## Project Structure

```
WinToolerV1_BUILD4\
  WinToolerV1.ps1        <- Main launcher and 14-step boot sequence
  Launch.bat             <- Double-click entry point (runs as Admin with -STA)
  README.md
  scripts\
    gui.ps1              <- Full WPF GUI and startup screen (~3150 lines)
  functions\
    tweaks.ps1           <- 23 tweak functions
    repair.ps1           <- Async SFC/DISM, DNS flush, temp clean, restore, network reset
    updates.ps1          <- PSWindowsUpdate integration
  config\
    apps.json            <- 111 apps across 10 categories (all Default: false)
    tweaks.json          <- 23 tweaks with risk levels (all Default: false)
    services.json        <- 18 Windows services with descriptions
```

---

## Requirements

| | |
|---|---|
| OS | Windows 10 (build 1809 or later) or Windows 11 |
| PowerShell | 5.1 or later (built into Windows) |
| Privileges | Administrator — UAC prompt fires automatically via Launch.bat |
| Network | Required for Install Apps, App Updates, DNS Changer, and Windows Update only |
| winget | Auto-installed at boot if missing |
| NuGet provider | Auto-installed silently at boot |
| PSWindowsUpdate | Auto-installed from PSGallery at boot if missing |

---

## Known Limitations

- **Heavy template tweaks are irreversible.** The boot-time restore point is your safety net — verify it was created before applying the Heavy template.
- **Language cannot be changed after launch** without a full restart. In-app toggle planned for v0.6.1.
- **System Protection must be enabled on C:** for restore points. May be blocked by Group Policy on managed machines.
- **winget requires internet access** for Install, App Updates, and Windows Update. Tweaks, Services, Repair, and DNS Changer work fully offline (DNS Changer modifies adapter settings locally — no network call needed).
- **DNS Changer is a work in progress.** Applied to all active adapters at once. No per-adapter selection in this build. See Known Issues above.

---

## License

[GPL-3.0](https://www.gnu.org/licenses/gpl-3.0.html) — Free to use, modify, and distribute. Any derivative work must also be released under GPL-3.0 and remain open source.

---

<div align="center">

Made with care by **[ErickP (Eperez98)](https://github.com/eperez98)**

*If this tool saved you time, a star on the repo goes a long way.*

</div>
