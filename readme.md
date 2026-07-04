# Windows Passport Utilities Manager (WPUM)

**Windows Passport Utilities Manager** is a lightweight Windows 11 tray utility that keeps your system responsive and your files easy to find. WPUM combines two focused tools in one app:

- **DCA Efficiency Mode Disabler** — continuously scans processes and removes Windows 11 Efficiency Mode (EcoQoS + idle priority) so foreground apps stay snappy.
- **File & Folder Search** — fast Everything-powered search with a cleaner split-pane UI, filters, and keyboard navigation.

Built with **Tauri 2**, **SvelteKit**, and **Rust**. Runs locally on your machine. No cloud account required.

---

## Features

### DCA Efficiency Mode Disabler

- Monitors **all processes** (not just browsers)
- Automatically disables EcoQoS and idle-priority throttling
- Live event log of fixes (PID, process name, timestamp)
- Configurable scan interval (500 ms – 60 s)
- System tray toggle for start/stop
- Optional launch at Windows startup
- Skips protected system processes (e.g. `lsass.exe`, `csrss.exe`)

### File & Folder Search

- Powered by [voidtools Everything](https://www.voidtools.com/)
- Split-pane layout with breadcrumbs and keyboard navigation
- Filter by files, folders, or both
- Sort by name, path, size, or date modified
- Open files or reveal in Explorer (paths validated against current search results)

### Setup & Security

- First-run setup wizard (Terms & Conditions, permissions, dependency check)
- Optional guided Everything install (with your explicit consent)
- HMAC-signed setup state, path allowlists, and strict Content Security Policy
- Runs as standard user (`asInvoker`); elevation only when needed for installs or full DCA coverage

---

## Requirements

| Component | Required | Notes |
|-----------|----------|-------|
| Windows 10/11 (64-bit) | Yes | WebView2 runtime (embedded in installer build) |
| Administrator | Recommended | Full DCA coverage; app runs without it in limited mode |
| voidtools Everything | For File Search | Installed and running; WPUM can help install it during setup |
| `Everything64.dll` | Bundled | Must sit next to the EXE for search IPC |

---

## Installation

### Option A — Portable EXE (quick start)

1. Copy these files to the same folder (e.g. `C:\Users\<you>\win\`):
   - `WindowsPassportUtilitiesManager.exe`
   - `Everything64.dll`
2. Double-click `WindowsPassportUtilitiesManager.exe`.
3. Complete the **Setup Wizard** on first launch.

Pre-built artifacts (when available):

```
dist/windows/WindowsPassportUtilitiesManager.exe
dist/windows/Everything64.dll
```

### Option B — NSIS installer

If built with NSIS (`sudo apt install nsis` on Linux, then `npm run build:windows`):

- Run `WindowsPassportUtilitiesManager-setup.exe`
- Accept the license, grant admin if prompted, and follow the installer steps
- Launch the app — the in-app setup wizard still runs on first use

---

## Usage

1. **Dashboard** — overview of DCA and search status
2. **DCA Disabler** (`/dca`) — start/stop monitoring, adjust poll interval, view fix log, enable autostart
3. **File Search** (`/search`) — type to search; use arrow keys, Enter to open, and filters in the toolbar

**System tray**

- Left-click tray icon — show window
- **Toggle DCA Monitor** — start/stop background scanning (requires completed setup)
- **Quit** — exit the app

Closing the window hides WPUM to the tray; it keeps running in the background.

---

## Building from Source

### Prerequisites

- Node.js 18+
- Rust (stable)
- For Windows cross-build from Linux/WSL: LLVM 19+, `cargo-xwin`, Windows MSVC target

### Commands

```bash
npm install
npm run dev          # Svelte dev server + Tauri dev window
npm run check        # TypeScript / Svelte checks
npm run build:windows # Portable EXE (+ NSIS installer if makensis is installed)
```

Output:

- `dist/windows/WindowsPassportUtilitiesManager.exe`
- `dist/windows/Everything64.dll`
- `dist/windows/WindowsPassportUtilitiesManager-setup.exe` (when NSIS is available)

### Copy from WSL to Windows

```bash
mkdir -p /mnt/c/Users/<you>/win
cp dist/windows/WindowsPassportUtilitiesManager.exe /mnt/c/Users/<you>/win/
cp dist/windows/Everything64.dll /mnt/c/Users/<you>/win/
```

---

## FAQ

### What is DCA / Efficiency Mode?

On Windows 11, **Efficiency Mode** (via EcoQoS and idle priority class) reduces CPU and power use for background tasks. That is good for battery life, but it can make apps feel sluggish when Windows misclassifies them. WPUM detects throttled processes and restores normal scheduling.

### Do I need to run WPUM as Administrator?

**Recommended for full DCA coverage.** Some processes cannot be modified without elevation. WPUM still runs as a normal user; you will see **limited access** on the permissions step if not elevated. File Search does not require admin.

### Why does File Search say “Everything unavailable”?

Everything must be **installed and running**. Start it from the Start menu, or use the setup wizard to install it. Ensure `Everything64.dll` is in the same folder as the WPUM executable.

### Can I use WPUM without installing Everything?

Yes. Skip Everything during setup. DCA Disabler works independently. File Search stays disabled until Everything is installed.

### Does WPUM send data to the internet?

**No personal data is collected or transmitted.** Settings are stored locally. The only network use is the optional, consent-based download of the official Everything installer from voidtools during setup.

### Why did I see “TaskDialogIndirect could not be located”?

That error means the app manifest was missing the **Common Controls v6** dependency. Rebuild with the current `build.rs` or use a binary built after that fix. Keep `Everything64.dll` beside the EXE.

### Is it safe to let WPUM modify process priorities?

WPUM only **removes** Efficiency Mode / idle throttling on non-protected processes. Critical system processes are on a denylist and are never touched. Use at your own discretion; see the license disclaimer.

### How do I uninstall?

- **Portable:** Delete the folder and remove autostart if enabled (Settings → Apps → Startup, or disable autostart in WPUM first).
- **Installer:** Windows Settings → Apps → Windows Passport Utilities Manager → Uninstall.

### What license is WPUM under?

MIT — see project license. voidtools Everything has its own license. WPUM Terms & Conditions are shown in the setup wizard and NSIS installer (`src-tauri/windows/LICENSE.txt`).

---

## Contact Developer

| | |
|---|---|
| **Developer** | Najoe |
| **Email** | [me@najoe.info](mailto:me@najoe.info) |
| **Product** | Windows Passport Utilities Manager v0.2.0 |
| **Publisher ID** | `com.najoesolutions.windows-passport-utilities` |

**When reporting issues, please include:**

- Windows version (e.g. Windows 11 24H2)
- Whether you run as Administrator
- WPUM version and install method (portable vs installer)
- Steps to reproduce and any error text or screenshots

Bug reports and feature requests are welcome by email. If this project is hosted on GitHub, you may also open an issue there.

---

## Features Roadmap

### v0.1 — Current (shipped)

- [x] DCA Efficiency Mode Disabler (all-process monitor)
- [x] Everything-powered File & Folder Search
- [x] Setup wizard (T&C, permissions, dependency install)
- [x] System tray integration and autostart
- [x] Security hardening (setup gate, path validation, CSP)
- [x] Portable EXE + NSIS installer pipeline

### v0.2 — Near term

- [ ] Per-app allowlist / denylist for DCA (user-defined process rules)
- [ ] Persist DCA config to disk across restarts
- [ ] In-app “Run as Administrator” relaunch button
- [ ] Search: recent queries, saved filters, and result export (CSV)
- [ ] Light / dark theme toggle
- [ ] Auto-check for WPUM updates (optional, opt-in)

### v0.3 — Medium term

- [ ] Process detail view (QoS state, priority class, fix history per PID)
- [ ] Scheduled quiet hours (pause DCA during specific times)
- [ ] Duplicate file finder (Everything-backed)
- [ ] Startup impact report (which apps launch with Efficiency Mode)
- [ ] Multi-language UI (starting with EN + common locales)

### Future ideas

- [ ] Additional utilities module slot (registry-free, tray-first tools)
- [ ] Group Policy / enterprise deployment package (MSI)
- [ ] CLI mode for scripting DCA on/off
- [ ] Integration with Windows Terminal / PowerShell helper cmdlets

Roadmap items are planned, not guaranteed. Priorities may change based on user feedback.

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| Desktop shell | Tauri 2 |
| UI | SvelteKit 2, Svelte 5, TypeScript |
| Backend | Rust 2021 |
| DCA | `win32-ecoqos` |
| Search | `everything-rs` + Everything64 SDK |
| Installer | NSIS (optional) |

---

## License

MIT — Copyright (c) 2026 Najoe Solutions Inc
