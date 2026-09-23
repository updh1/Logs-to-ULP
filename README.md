# @updh2 Stealer Converter — Windows GUI

A Windows desktop application built with [Wails](https://wails.io) that parses stealer logs (Katz, Redline, Remus, ShapeShifter, StealC, Lumma, Vidar), extracts URL:Login:Password entries, geolocates via IP, and outputs deduplicated ULP lines split by country.

![Dark monochrome UI — Geist fonts, hairline borders, no color except status dots](./screenshot.png)

---

## Features

- **Multi-stealer support** — auto-detects Katz, Redline, Remus, ShapeShifter, StealC, Lumma, Vidar
- **IP geolocation** — loads `ipdb.csv` to map IPs → country codes
- **Multi-threaded** — parallel log processing with real-time progress
- **Deduplication** — removes duplicate lines from output
- **Country splitting** — writes `all.txt` + per-country files in `output/<timestamp>/country/`
- **Dark monochrome GUI** — clean Wails desktop interface
- **Optional cleanup** — can delete input directory after processing

---

## Requirements

| Tool | Version | Purpose |
|------|---------|---------|
| [Go](https://go.dev/dl/) | ≥ 1.21 | Backend compiler |
| [Wails CLI](https://wails.io/docs/gettingstarted/installation) | v2.9+ | Desktop framework |
| Windows | 10/11 | Target platform |

> Wails requires a C compiler (GCC). On Windows, install [MSYS2](https://www.msys2.org/) or [MinGW-w64](https://github.com/niXman/mingw-builds-binaries/releases).

---

## Quick Start

```bash
# 1. Clone or download the project
cd stealer-converter

# 2. Resolve Go dependencies
go mod tidy

# 3. Run in development mode (hot-reload)
wails dev

# 4. Build for production (Windows .exe)
wails build
```

The built binary will be at `build/bin/void-converter.exe`.

---

## Usage

### 1. Place `ipdb.csv` alongside the executable

The IP database CSV should have 3 columns:
```
start_ip,end_ip,country_code
```
Example:
```
1.0.0.0,1.0.0.255,AU
1.0.1.0,1.0.3.255,CN
```

### 2. Launch the application

Double-click `ft7-converter.exe` or run from terminal.

### 3. Configure and process

1. Click **Browse** to select the logs directory
2. Adjust scan depth (default: 10)
3. Optionally enable cleanup
4. Click **Start Processing** (or `Ctrl+Enter`)
5. Watch real-time progress
6. View results when complete

### Output structure

```
output/
└── 2025-01-15_143022/
    ├── all.txt           ← all unique ULP lines
    └── country/
        ├── US.txt        ← lines from US IPs
        ├── DE.txt        ← lines from DE IPs
        ├── ZZ.txt        ← unknown/unresolved IPs
        └── ...
```

Each line format: `url:login:password`

---

## Supported Stealer Formats

| Stealer | Detection Pattern | File Location |
|---------|------------------|---------------|
| Katz | `browser:`, `profile:`, `url:`, `login:` | `Browsers/Passwords/*.txt` |
| Redline | `url:`, `username:`, `password:` | `Passwords.txt` |
| Remus | `browser:`, `url:`, `login:`, `profile:` | `All Passwords.txt` |
| ShapeShifter | `url:`, `login:`, `password:` (no `browser:`) | `Browsers/Passwords/*.txt` |
| StealC | `browser:`, `profile:`, `url:`, `login:` | `passwords.txt` |
| Lumma | `url:`, `login:`, `password:` | `Passwords/*.txt` |
| Vidar | `soft:`, `host:`, `login:`, `password:` | `passwords` (file) |

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+Enter` | Start processing |
| `Escape` | Stop processing |

---

## Project Structure

```
stealer-converter/
├── main.go                  # Wails entry point
├── app.go                   # All business logic + Wails API
├── go.mod                   # Go module
├── wails.json               # Wails configuration
├── scripts/
│   └── build_frontend.go    # Copies frontend → dist/ for embedding
├── frontend/
│   ├── index.html           # UI structure
│   ├── style.css            # Dark monochrome design system
│   ├── app.js               # Frontend logic + Wails IPC
│   └── dist/                # Built frontend (embedded in binary)
└── README.md
```

---

## UI Design System

- **Background**: `#0a0a0a` near-black
- **Panels**: `#111111` with `#232323` hairline borders
- **Hierarchy**: grayscale value steps only, no color
- **Primary button**: inverted (light-on-dark `#f2f2f0`)
- **Status dots**: the ONLY color — green (ok), red (error), yellow (warning)
- **Typography**: Geist (UI) + Geist Mono (data/numbers/paths)
- **Hover**: subtle 2% white lift on rows

---

## Credits

- Original CLI tool: **t.me/voidcloudlinks**
- GUI port: Wails v2 + vanilla HTML/CSS/JS
- Font: [Geist](https://vercel.com/font) by Vercel

---

## License

For personal use only.
