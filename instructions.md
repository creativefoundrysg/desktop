# Custom GitHub Desktop Build — Instructions

This document describes the changes made to this checkout and how to build a
custom macOS / Windows build of GitHub Desktop from it.

Two things set this build apart from a stock GitHub Desktop build:

1. **Auto-update is completely disabled** (no update checks, no update banners,
   no install-on-quit flow).
2. **Local (unsigned / ad-hoc signed) builds are supported** on macOS so the
   app can be packaged without Apple Developer credentials.

---

## 1. Source changes (what was modified)

### 1.1 Auto-update disabled

All triggers and plumbing for the auto-updater were neutralized. The app never
contacts the update server, so these can never fire:

- the "an updated version is available" banner
- the "Exciting new features" showcase banner
- the "prioritized / important updates" banner
- the "installing update, don't quit" flow

**`app/src/main-process/app-window.ts`**

| Location | Change |
| --- | --- |
| `setupAutoUpdater()` | Body removed — the main process no longer registers any `autoUpdater` event listeners and therefore never sends `auto-updater-*` IPC events to the renderer. |
| `checkForUpdates(url)` | Now a no-op returning `undefined`. It never calls `autoUpdater.setFeedURL(...)` or `autoUpdater.checkForUpdates()`. |
| `quitAndInstallUpdate()` | Now a no-op. It never calls `autoUpdater.quitAndInstall()`. |
| imports | Removed the now-unused `getUpdaterGUID` import. |
| module scope | Removed the now-unused `trySetUpdaterGuid()` helper. |

Note: `autoUpdater` is still imported and used by
`autoUpdater.removeAllListeners()` in the window-close handler (harmless).

**`app/src/ui/app.tsx`**

| Location | Change |
| --- | --- |
| `checkForUpdates(inBackground, skipGuidCheck)` | Now returns immediately. This covers every trigger: the startup check, the 4-hour periodic re-check in `performDeferredLaunchActions()`, and the on-demand "Check for Updates" menu action. |
| imports | Removed the now-unused `isMacOSAndNoLongerSupportedByElectron` and `isWindowsAndNoLongerSupportedByElectron` imports. |

### 1.2 Local code-signing support on macOS

**`script/build.ts`** — the `osxSign` block was adjusted so that builds made
**outside GitHub Actions** always ad-hoc sign with `-` (Apple Developer
identity not required):

```ts
osxSign: {
  optionsForFile: (path: string) => ({
    hardenedRuntime: true,
    entitlements: entitlementsPath,
  }),
  type:
    isPublishableBuild && isGitHubActions()
      ? 'distribution'
      : 'development',
  identity: isDevelopmentBuild || !isGitHubActions() ? '-' : undefined,
  identityValidation: isGitHubActions() && !isDevelopmentBuild,
},
```

CI behavior is unchanged; only non-CI builds are affected.

---

## 2. Prerequisites

### macOS

- Node.js — use the exact version pinned in `.node-version` / `.tool-versions`
  (currently **24.19.0**). Newer majors (e.g. 25.x) will fail `yarn install`
  with an engine error from `ini@7`. Install it via `nvm`, `asdf-nodejs`
  (`asdf install nodejs 24.19.0`), or directly from nodejs.org.
- Yarn 1.x (`yarn -v` → 1.x). The repo vendors its own copy via `.yarnrc`.
- Python 3 and Xcode Command Line Tools (`xcode-select --install`).

### Windows

- Node.js 24.19.0 (via `nvm-windows` or direct install).
- Yarn 1.x.
- Python 3.9+ (with `Add python.exe to Path`).
- Visual Studio 2019/2022 with the **Desktop development with C++** workload
  so native modules (keytar, desktop-trampoline, etc.) compile
  (`npm config set msvs_version 2019` if needed).

---

## 3. Critical: git submodules

This checkout is a source archive, not a `git clone`, so three git submodules
are **empty** and the build will fail unless they are populated. They must
contain specific pinned commits:

| Path | Repo | Pinned commit |
| --- | --- | --- |
| `gemoji/` | https://github.com/github/gemoji.git | `50865e8895c54037bf06c4c1691aa925d030a59d` |
| `app/static/common/choosealicense.com/` | https://github.com/github/choosealicense.com.git | `aed28f9933c9e8435c0f1d07aa563c0a830859bc` |
| `app/static/common/gitignore/` | https://github.com/github/gitignore.git | `81ceb66b6b9d32324c98fe1c51f27d2eb9451865` |

Recommended fix: clone the repository recursively and re-apply the changes from
section 1:

```sh
git clone --recursive https://github.com/desktop/desktop.git
```

Or populate the directories manually in this checkout (example for `gemoji`):

```sh
git clone --depth 1 https://github.com/github/gemoji.git gemoji
cd gemoji
git fetch --depth 1 origin 50865e8895c54037bf06c4c1691aa925d030a59d
git checkout 50865e8895c54037bf06c4c1691aa925d030a59d
```

Requirements at build time:

- `gemoji/images/emoji/*` and `gemoji/db/emoji.json` (used by
  `script/build.ts` → `copyEmoji()`).
- `choosealicense.com/_licenses/*` and `LICENSE.md` (used by
  `generateLicenseMetadata()`).
- `gitignore/*` (copied as static resources).

---

## 4. Environment notes (no git repo)

Because this checkout has no `.git` directory:

1. **Webpack SHA** — `app/git-info.ts` reads `.git` to compute the build SHA,
   but it short-circuits on the `CIRCLE_SHA1` environment variable. Export any
   40-hex value before building:
   ```sh
   export CIRCLE_SHA1=5f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
   ```
   (Or initialize a git repo with one commit — then the variable is optional.)

2. **Post-install `git submodule update`** — `script/post-install.ts` runs
   `git submodule update --recursive --init`, which fails with
   `fatal: not a git repository`. This is fine because the submodules are
   already populated; just run the remaining post-install step manually:
   ```sh
   node vendor/yarn-1.21.1.js compile:script
   ```

---

## 5. Build instructions

### 5.1 macOS — development build (live reload)

Development builds load the renderer from a webpack dev server on
`localhost:3000`, so they are **not** standalone — run them with `yarn start`.

```sh
yarn                                   # install deps + native modules
node vendor/yarn-1.21.1.js compile:script   # if postinstall's git step failed
export CIRCLE_SHA1=5f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
yarn build:dev                         # compile + package a dev app
yarn start                             # launch with live reload
```

### 5.2 macOS — standalone app (no dev server, no certs)

Production builds bundle the renderer locally and ad-hoc sign, so the `.app`
is self-contained and double-clickable.

```sh
yarn
node vendor/yarn-1.21.1.js compile:script   # only if postinstall's git step failed
export CIRCLE_SHA1=5f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a

NODE_ENV=production RELEASE_CHANNEL=production yarn build:prod

# Required: re-sign the whole bundle ad-hoc. Without this, macOS refuses to
# load the Electron Framework ("different Team IDs") and the app won't start.
codesign --force --deep --preserve-metadata=entitlements --sign - \
  "dist/GitHub Desktop-darwin-arm64/GitHub Desktop.app"

open "dist/GitHub Desktop-darwin-arm64/GitHub Desktop.app"
```

If you **have** an Apple Developer ID certificate:

```sh
security find-identity -v -p codesigning   # confirm an "Developer ID Application" cert exists
NODE_ENV=production RELEASE_CHANNEL=production yarn build:prod
# then skip the manual codesign step; envs for notarization (optional):
export APPLE_ID=you@example.com APPLE_ID_PASSWORD=xxxx APPLE_TEAM_ID=ABCD1234
```

Output bundle: `dist/GitHub Desktop-darwin-arm64/GitHub Desktop.app`
Optionally zip it: `yarn package`.

> Note: the production bundle id is `com.github.GitHubClient`, the same as an
> official install, and it shares
> `~/Library/Application Support/GitHub Desktop`. Don't run it at the same time
> as the official app.

### 5.3 Windows — standalone app

No code-signing is needed for a local Windows build (the installer signing
happens only on GitHub Actions), so the same production flow applies:

```bat
yarn
node vendor\yarn-1.21.1.js compile:script   REM only if postinstall's git step failed
set CIRCLE_SHA1=5f0a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a
set NODE_ENV=production
set RELEASE_CHANNEL=production
yarn build:prod
yarn package
```

Outputs (under `dist\installer\`):

- `GitHubDesktopSetup-x64.exe` (standalone installer)
- `GitHubDesktopSetup-x64.msi`
- `GitHubDesktop-<version>-x64-full.nupkg` and `-delta.nupkg` (update feeds —
  unused since updates are disabled)

The unsigned installer will trigger a SmartScreen warning; that is expected.

---

## 6. Verifying the build

Confirm the auto-update changes are actually in the bundled app:

```sh
# macOS (path varies on Windows)
grep -c "autoUpdater.checkForUpdates" \
  "dist/GitHub Desktop-darwin-arm64/GitHub Desktop.app/Contents/Resources/app/main.js"
# expect: 0
```

Launch and confirm no devtools/console window appears on its own (in a prod
build devtools only open if the page fails to load) and that the UI renders.

---

## 7. Troubleshooting

| Symptom | Cause / fix |
| --- | --- |
| `error ini@7.0.0: engine "node" is incompatible` | Node is too new. Use Node **24.19.0**. |
| `ENOENT: ... stat '.../.git'` from webpack | `getSHA()` can't find git info. Set `CIRCLE_SHA1` (any 40 hex chars). |
| `fatal: not a git repository` during postinstall | Expected without a git repo. Submodules are already present; run `node vendor/yarn-1.21.1.js compile:script` and continue. |
| App launches but window is blank + devtools open | You're running a **dev** build standalone. Dev builds need `yarn start` (renderer served on `localhost:3000`). Use a production build for a standalone app. |
| `different Team IDs` / `Library not loaded: Electron Framework` on launch (macOS) | The bundle wasn't fully re-signed. Run `codesign --force --deep --preserve-metadata=entitlements --sign - "dist/GitHub Desktop-darwin-arm64/GitHub Desktop.app"`. |
| Package fails on macOS signing | No Apple identity on the machine; section 1.2's `build.ts` change makes non-CI builds ad-hoc, so this should not occur. |
| Empty `gemoji/` yields build errors | Submodules missing — see section 3. |