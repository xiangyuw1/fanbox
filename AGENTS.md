# AGENTS.md — FanBox

## What this is

FanBox is a local file browser + embedded terminal + preview/edit cockpit for "vibe coding." Electron desktop app wrapping a zero-dependency Node.js backend. macOS-first (Apple Silicon arm64 native), also runnable as a web server on any platform.

**Key insight**: The backend (`server.js`) uses only Node built-ins — zero runtime dependencies. Frontend dependencies (xterm, Monaco, Milkdown, marked, highlight.js) are vendored into `public/vendor/`. This is intentional: the app works fully offline.

## Commands

```bash
npm install              # install deps (electron, node-pty, esbuild, etc.)
npm run app              # Electron desktop app (full features: terminal, editors, etc.)
npm start                # web-only mode on localhost:4567 (alias for `node server.js`)
npm run dist             # build + sign arm64 .dmg → dist/ (macOS only, needs Apple cert)
npm run rebuild          # electron-rebuild for node-pty (run after Electron upgrade or npm install)
npm run build:milkdown   # rebundle milkdown vendor from src-vendor/milkdown-entry.js
npm run build:hljs       # rebundle hljs + copy marked vendor from src-vendor/hljs-entry.js
node server.js           # same as npm start; set FANBOX_NO_OPEN=1 to skip auto-open browser
```

**Port**: Default `4567`, override with `FANBOX_PORT` env var.

**No automated tests, lint, typecheck, or formatter exist.** Verify changes by running `npm run app` and testing manually.

## Vendor patch (CRITICAL)

xterm.js has a local patch for IME keyCode handling. The `predist` script verifies it:

```bash
npm run check:vendor-patch   # greps public/vendor/xterm/xterm.js for the patch
```

If this check fails, `npm run dist` will abort. The patch content: `20===e.keyCode||229===e.keyCode`. Never overwrite `public/vendor/xterm/` without preserving this patch.

## Architecture

| Layer | File | Role |
|---|---|---|
| Backend | `server.js` (~2100 lines) | HTTP server, file API, thumbnails, search, git (read-only), agent usage, skills, AI organize, release wizard |
| Electron main | `electron/main.js` (~420 lines) | Window, node-pty terminals, fs.watch, clipboard, screenshots, menus |
| Electron preload | `electron/preload.js` (~60 lines) | IPC bridge: `fanboxPty`, `fanboxFs`, `fanboxClipboard`, `fanboxDrop`, `fanboxShot`, `fanboxUpdate` |
| Frontend SPA | `public/app.js` (~3800 lines) | All UI logic, state, rendering — single file, vanilla JS, no framework |
| Styles | `public/style.css` | Three skins: Volt (terminal/neon), Archive (warm/cream), Index (editorial/b&w) |
| i18n | `public/i18n.js` + `public/i18n-dict.js` | Bilingual UI (Chinese primary, English secondary) |
| Vendor entries | `src-vendor/` | esbuild entry points for Milkdown and highlight.js bundles |

**Data flow**: Electron main `require('../server.js')` which starts HTTP on `127.0.0.1:4567` (side-effect on require). Frontend loads from same origin. Terminal I/O goes through Electron IPC (`fanboxPty`). File change events go through `fanboxFs` (Electron fs.watch → IPC → frontend).

**Two HTTP servers**: Main server on PORT (all APIs + static). Preview server on PORT+1 (only `/fs/` endpoint, no `/api`) — HTML preview iframes point here for sandbox isolation. Preview scope restricted to home directory, dot-paths blocked.

## Runtime config

- Config dir: `~/.fanbox/` (config.json, thumbs/, organize-log/, organize-prefs.md, organize-brief.md)
- Config writes use serialized read-modify-write + atomic persistence (temp + fsync + rename)
- Thumbnail cache: LRU pruning at 400MB cap, triggered on startup
- Agent session data: reads `~/.claude/projects/` and `~/.codex/sessions/` (read-only)

## Security model

- Backend binds `127.0.0.1` only — data never leaves the machine
- Host header validation blocks DNS rebinding
- POST Origin validation blocks CSRF
- HTML preview served from separate port (PORT+1) with sandboxed iframe, no /api exposed
- File deletions go to system Trash (recoverable)
- Text file writes use expectedMtime conflict detection (won't blindly overwrite agent changes)

## Code conventions

- **Language**: Plain JavaScript (no TypeScript, no transpilation for main code)
- **Module system**: CommonJS (`require`/`module.exports`) in backend, no modules in frontend (single IIFE)
- **No framework**: Frontend is vanilla JS with direct DOM manipulation
- **No build step for main code**: `server.js`, `electron/`, `public/app.js` run directly
- **Bilingual**: Code comments and UI strings are bilingual (Chinese primary, English secondary)
- **Error handling**: Silent catch with fallback is common pattern (e.g., `catch { /* */ }`) — intentional for a local tool where partial degradation is acceptable

## Electron specifics

- `node-pty` is a native module — requires `electron-rebuild` (`npm run rebuild`) after Electron version changes or fresh `npm install`
- `asarUnpack` for node-pty (native modules can't live inside asar)
- Window state (bounds) persisted to `<userData>/window-state.json`
- macOS: dock icon set in dev mode, entitlements for hardened runtime in `build/entitlements.mac.plist`
- Auto-update check: GitHub Releases API, 2-hour interval + window focus trigger (30min throttle)
- `IGNORE_DIRS` in server.js: node_modules, .git, .next, dist, build, .cache, .venv, venv, __pycache__, .DS_Store, Pods, .gradle, target, .idea, .vscode-test, DerivedData, .expo, .turbo, vendor, .svn, .hg

## Key files to read before editing

- `server.js` lines 1–100: imports, constants, IGNORE_DIRS, file type sets
- `server.js` lines 1850–2040: HTTP routing (all API endpoints)
- `electron/main.js` lines 1–100: window creation, PTY setup
- `electron/preload.js`: all IPC bridges (what the frontend can call)
- `public/app.js` lines 1–80: API helpers, SVG icon system, file kind mappings

## Project boundaries

- `public/vendor/` — vendored third-party assets, mostly generated by build scripts. Don't edit directly; use `npm run build:milkdown` or `npm run build:hljs`.
- `build/` — Electron builder resources (icons, entitlements). Only `icon.icns`, `icon.png`, `icon-1024.png`, `entitlements.mac.plist` are tracked in git.
- `experiments/` — test/demo scripts, not part of the shipped app.
- `design-demos/` — static HTML design explorations, not shipped.
- `docs/` — concept docs, PRD, acceptance criteria. In Chinese. Key: `docs/05-验收角色与评分标准.md` defines the 5-role scoring system.
- `素材/` — user asset folder (screenshot express target). Not code.
