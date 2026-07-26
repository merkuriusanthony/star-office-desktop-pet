# Star Office Desktop Pet

Patched fork of [Star-Office-UI](https://github.com/ringhyacinth/Star-Office-UI)'s
`desktop-pet/` Tauri app, wired to a **remote** backend instead of spawning a local
Python process. Points at `https://dashboard.claireantonia.id` — the Star Office UI
instance already running on the Hermes NAS, driven live by Hermes tool activity.

## What changed vs upstream

- `src-tauri/tauri.conf.json`: main window URL → `https://dashboard.claireantonia.id/?desktop=1`
- `src/minimized.html`: `BASE_URL` → `https://dashboard.claireantonia.id`
- `src-tauri/src/lib.rs`: local backend spawn/wait skipped when `STAR_REMOTE_BACKEND` env var is set
- `desktop-pet/package.json`: `dev`/`build` scripts use `cross-env` so the inline
  `STAR_PROJECT_ROOT=..` env var works on Windows too (upstream's Unix-only syntax
  fails on PowerShell/cmd with `'STAR_PROJECT_ROOT' is not recognized`)

## Setup (macOS)

Requires: [Rust](https://rustup.rs/) + Node.js (Tauri toolchain).

```bash
# 1. Install Rust (if you don't have it)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# 2. Clone this repo
git clone https://github.com/merkuriusanthony/star-office-desktop-pet.git
cd star-office-desktop-pet/desktop-pet

# 3. Install JS deps
npm install

# 4. Run (STAR_REMOTE_BACKEND tells it to skip the local Python backend)
STAR_REMOTE_BACKEND=1 npm run dev
```

## Setup (Windows)

Requires: Rust (MSVC toolchain), Node.js, C++ Build Tools, WebView2 Runtime (usually
pre-installed on Windows 10/11).

```powershell
# 1. Install Rust
winget install Rustlang.Rustup

# 2. Install Node.js
winget install OpenJS.NodeJS.LTS

# 3. Allow local scripts to run (npm ships as a .ps1 wrapper on Windows)
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 4. Clone this repo
git clone https://github.com/merkuriusanthony/star-office-desktop-pet.git
cd star-office-desktop-pet\desktop-pet

# 5. Install JS deps
npm install

# 6. Run
$env:STAR_REMOTE_BACKEND=1; npm run dev
```

(In `cmd.exe` instead of PowerShell, use `set STAR_REMOTE_BACKEND=1 && npm run dev`.)

First run compiles the Rust/Tauri shell — takes a few minutes on either OS. Subsequent
runs are fast.

## Result

A transparent, always-on-top desktop widget showing the pixel office character,
reflecting live Hermes activity (idle/writing/researching/executing/syncing/error)
in real time — no local backend needed, everything reads from the NAS.

## Build a standalone app (optional)

macOS:
```bash
cd desktop-pet
STAR_REMOTE_BACKEND=1 npm run build
```

Windows (PowerShell):
```powershell
cd desktop-pet
$env:STAR_REMOTE_BACKEND=1; npm run build
```

Output lands under `desktop-pet/src-tauri/target/release/bundle/` — `.app`/`.dmg` on
macOS, `.exe`/`.msi` on Windows.
