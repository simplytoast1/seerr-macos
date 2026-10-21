<!-- Generated from the build repository on every release; edits made here are replaced. -->
# Seerr for macOS (unofficial)

Unofficial Apple Silicon builds of [Seerr](https://github.com/seerr-team/seerr), built from
source automatically whenever Seerr publishes a release. These builds are not made or
supported by the Seerr project, so please don't report problems with them there.

**[Download the latest version](https://github.com/simplytoast1/seerr-macos/releases/latest/download/Seerr-macos-arm64.dmg)**
(or see [all releases](https://github.com/simplytoast1/seerr-macos/releases))

Needs a Mac with Apple Silicon (M1 or later) and macOS 12 or later. Each build is tested on
macOS 15 and macOS 26 before it is published.

## Install

1. Open the DMG and drag Seerr into Applications.
2. Open Seerr. The first time, choose **Start Fresh**, or **Import Existing…** to bring in an
   existing Seerr, Jellyseerr or Overseerr installation (pick its config folder, the one with
   `settings.json`, after stopping it).
3. A Seerr icon appears in the menu bar and Seerr opens in your browser at
   http://localhost:5055. Finish the setup right away: the first account that signs in
   becomes Seerr's admin, and until then anyone on your network who can reach port 5055
   could be that account. If a new install says Access denied or shows an admin you don't
   know, quit Seerr, move `~/.config/Seerr` aside, and open Seerr again to start fresh.

If macOS blocks the first launch, open **System Settings > Privacy & Security** (on macOS 12,
System Preferences > Security & Privacy > General) and click **Open Anyway**. Each release's
notes say whether that build is notarized.

On macOS 15 or later, when Seerr first connects to your media server, Sonarr or Radarr,
macOS asks to allow local network access: allow it, and test the connection again if that
first test failed. If you clicked Don't Allow, or a test keeps saying "Failed to connect"
(and Show Logs mentions EHOSTUNREACH), turn Seerr on in
**System Settings > Privacy & Security > Local Network**.

## Using it

- Seerr runs while the app is open. **Quit Seerr** from its menu bar icon stops it.
- The menu bar icon also has Open Seerr, Restart Seerr, Back Up Now, Show Logs,
  Check for Updates and, on macOS 13 or later, Open at Login (off unless you turn it on).
- Other devices on your network can open Seerr at `http://<this Mac's name>.local:5055`. To
  allow only this Mac, add `HOST=127.0.0.1` to `~/.config/Seerr/seerr.env` and restart Seerr.
- Seerr is only reachable while the Mac is awake.
- Settings, the database and logs are kept in `~/.config/Seerr`, and backups in
  `~/.config/Seerr-backups`. Replacing or deleting the app never touches them.

## Updating

Seerr checks for new versions once a day and offers to install them (or choose
**Check for Updates…** from its menu bar icon). Updates are signed and verified before they
install, and your settings and SQLite database are backed up automatically to
`~/.config/Seerr-backups` before a new version starts for the first time. If you use
PostgreSQL, back the database up with `pg_dump` before updating.

To go back to an earlier version: quit Seerr, drag the older release's Seerr into
Applications and choose Replace (don't open it yet), then restore the backup taken before
the update and open Seerr:

```bash
/Applications/Seerr.app/Contents/Resources/seerrctl restore ~/.config/Seerr-backups/<date>-before-<new>-from-<old>.tar.gz
```

When Seerr offers the newer version again, choose **Skip This Version**. If you had turned
on automatic installs in the update window, turn them off before opening the older Seerr,
or it installs the newer version again on its own:

```bash
defaults write io.github.simplytoast1.seerr SUAutomaticallyUpdate -bool false
```

## Licenses

Seerr and Sparkle are MIT licensed. Each release includes `THIRD-PARTY-NOTICES.txt` with the
licenses of Node.js and every bundled package, links to the exact source of the copyleft
components, and the exact Seerr source each build was made from.
