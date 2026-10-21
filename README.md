# Seerr for macOS (unofficial)

Unofficial Apple Silicon builds of [Seerr](https://github.com/seerr-team/seerr), built from
source automatically whenever Seerr publishes a release. These builds are not made or
supported by the Seerr project, so please don't report problems with them there.

**[Download the latest version](https://github.com/simplytoast1/seerr-macos/releases/latest/download/Seerr-macos-arm64.dmg)**
(or see [all releases](https://github.com/simplytoast1/seerr-macos/releases))

Needs a Mac with Apple Silicon (M1 or later). Each build is tested on macOS 15 and macOS 26
before it is published.

## Install

1. Open the DMG and drag Seerr into Applications.
2. Open Seerr. The first time, choose **Start Fresh**, or **Import Existing…** to bring in an
   existing Seerr, Jellyseerr or Overseerr installation (pick its config folder, the one with
   `settings.json`, after stopping it).
3. A Seerr icon appears in the menu bar and Seerr opens in your browser at
   http://localhost:5055.

If macOS blocks the first launch, open **System Settings > Privacy & Security** and click
**Open Anyway** once. Each release's notes say whether that build is notarized.

## Using it

- Seerr runs while the app is open. **Quit Seerr** from its menu bar icon stops it.
- The menu bar icon also has Open Seerr, Restart Seerr, Back Up Now, Show Logs,
  Get New Version and Open at Login (off unless you turn it on).
- Settings, the database and logs are kept in `~/.config/Seerr`, and backups in
  `~/.config/Seerr-backups`. Replacing or deleting the app never touches them.

## Updating

Quit Seerr from its menu bar icon, drag the new Seerr into Applications and choose
**Replace**, then open it. Your data is backed up automatically before a new version starts
for the first time.

## Licenses

Seerr is MIT licensed. Each release includes `THIRD-PARTY-NOTICES.txt` with the licenses of
Node.js and every bundled package, and links to the exact Seerr source it was built from.
