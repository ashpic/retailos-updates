# RetailOS Updates

This repo is a **distribution/version-check channel only** — it holds no
source code and no business logic. It exists so the RetailOS ASM Windows/Mac
build can silently check for and download a newer build, without needing
any credentials baked into the installed app (this repo is public and
read-only from the app's side; the real source lives in the private
`ashpic/retailos-installer` repo).

## Contents

- `version.json` — current version + public download URLs, e.g.:
  ```json
  {
    "version": "b82dd06",
    "built_at": "2026-07-28T11:00:00Z",
    "windows_url": "https://github.com/ashpic/retailos-updates/releases/download/b82dd06/RetailOS-Windows.exe",
    "mac_url": "https://github.com/ashpic/retailos-updates/releases/download/b82dd06/RetailOS-Mac"
  }
  ```
- A GitHub Release per version, holding the actual built binaries as public
  assets (mirrored here from `retailos-installer`'s private `latest-build`
  release after each build).

## How it's updated

Manually, from the machine doing RetailOS development — after a build in
`retailos-installer` succeeds, its binaries + a fresh `version.json` are
copied here and pushed. There's no CI wired between the two repos on
purpose (avoids needing a cross-repo write token); this repo only changes
when a human deliberately ships a new version.

## How the app uses this

On every launch, before starting its server, the app fetches
`version.json` from this repo's `main` branch (a few seconds' timeout,
no auth needed since this repo is public). If the version differs from
its own baked-in version, it downloads the matching binary from the
release above, replaces itself, and relaunches — silently, no prompt.
Any failure (no internet, this repo unreachable, bad response) is
swallowed and the app just starts normally with whatever it already has.
