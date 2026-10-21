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
   existing Seerr installation (see [Moving from another install](#moving-from-another-install)).
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

## Moving from another install

A Seerr install (v3.0.0 or later) from Docker, a NAS, Linux, Windows or another Mac can move
here with its users, requests and settings. With SQLite (the default) the import works on a
copy, so the old install is never changed and you can go back to it. Still on Jellyseerr or
Overseerr? Upgrade it to Seerr where it runs first, with Seerr's
[migration guide](https://docs.seerr.dev/migration-guide), then move it here.

**Before you start**

- This Seerr must be the same version as the old install or newer. Seerr can't move its data
  back to an older version.
- Note the old install's environment settings, such as `TZ`, `LOG_LEVEL`, `PORT` and any
  `DB_*` lines. They live in your Docker Compose file, `docker run` command or service file,
  not in the config folder. (The Synology Seerr package keeps them in a `seerr.env` inside the
  config folder, and the import brings that along.)
- Stop the old install, and anything that restarts it, so its database doesn't change while
  you copy it. With Docker, stop just that container with `docker stop <container>`:
  `docker compose down` deletes the container that `docker cp` copies from, and a PostgreSQL
  container has to keep running.

### 1. Find the config folder

It's the folder with `settings.json` and `db/db.sqlite3` in it. Seerr shows it under
**Settings > About > Data Directory**. In Docker that's the path inside the container
(`/app/config`), so look up the host folder mapped to it.

| Old install | Config folder |
|---|---|
| Docker or Docker Compose | The host folder mapped to `/app/config` (the `volumes:` line, or `docker inspect <container>`) |
| Docker named volume, including Docker Desktop on Windows or a Mac | Inside the volume: copy it out with `docker cp` (step 2) |
| Unraid | `/mnt/user/appdata/seerr` (binhex image: `/mnt/user/appdata/seerr/seerr`) |
| TrueNAS SCALE app | `/mnt/.ix-apps/app_mounts/<app name>/config` (the app name is usually `seerr`), or the host path you chose |
| Synology Container Manager | The folder mapped to `/app/config` in the container's settings. Over SSH, put your volume in front of it: `/docker/seerr` is `/volume1/docker/seerr` |
| Synology Seerr package | `/var/packages/seerr/var/config` |
| Kubernetes (Helm chart) | The volume mounted at `/app/config`: copy it out with `kubectl cp` (step 2) |
| Built from source | The `config` folder of the install, such as `/opt/seerr/config` or `C:\seerr\config` |
| Arch Linux (AUR) | `/var/lib/seerr` |
| NixOS | `/var/lib/seerr` (`/var/lib/jellyseerr/config` if `system.stateVersion` is older than 26.05) |

### 2. Copy it to the Mac

If the old install ran on this Mac in a folder you can open in Finder, there's nothing to
copy: skip to step 3. A Docker named volume still needs `docker cp`, below.

**From a Linux server or NAS over SSH.** The files usually belong to root or the container's
user, so archive them with `sudo` (leave it out where you're already root, as on Unraid),
using your folder from step 1:

```bash
sudo tar -czf ~/seerr-config.tar.gz -C /path/to/config --exclude ./cache .
```

Then on the Mac:

```bash
scp you@server:seerr-config.tar.gz ~/Downloads/
```

Double-click `seerr-config.tar.gz` in Finder to unpack it into a `seerr-config` folder. On
Synology, turn on the user home service first (Control Panel > User & Group > Advanced), and
if `scp` stops with "Connection closed", run it again with `scp -O`.

**From Docker.** `docker cp` works on a stopped container and on named volumes. Use your
container's name, and its config path from step 1 (`/app/config` for the official image,
`/config` for hotio's, `/config/seerr` for binhex's):

```bash
docker cp seerr:/app/config ./seerr-config
```

If your account can't run `docker`, put `sudo` in front, then make the copy yours with
`sudo chown -R "$USER" seerr-config`. Copy the `seerr-config` folder to the Mac with
`scp -r`, a shared folder or a USB drive. On Windows, run the command in a PowerShell window
opened with **Run as administrator**: the logs folder holds symbolic links, which Windows only
lets an administrator create. On Kubernetes,
`kubectl cp <namespace>/<pod>:/app/config ./seerr-config` does the same from the running pod,
so do it while nobody is using Seerr.

### 3. Import it

For most installs: open Seerr, choose **Import Existing…** and pick the `seerr-config` folder.
The import checks the settings and the database, keeps a copy in
`~/.config/Seerr-backups/<date>-import/`, and starts Seerr.

**Import from Terminal instead** if any of these apply, because the button starts Seerr right
away and these need changes before its first start:

```bash
/Applications/Seerr.app/Contents/Resources/seerrctl import ~/Downloads/seerr-config
```

- **You already set Seerr up on this Mac.** Quit Seerr and add `--replace`. Your current data,
  including `seerr.env`, is moved to the backups folder, not deleted.
- **PostgreSQL.** Add `--postgres`. Only the settings are copied; the database stays on your
  PostgreSQL server, and this Seerr upgrades it in place if it's newer than the old install, so
  back it up first with `pg_dump -Fc`. Then add your `DB_*` settings to
  `~/.config/Seerr/seerr.env`. `DB_HOST` must be an address this Mac can reach, not `localhost`,
  a Docker name such as `postgres`, or a `DB_SOCKET_PATH`, and the server must accept
  connections from this Mac.
- **Jellyfin or Emby at an address only the old machine could use,** such as `localhost` or a
  Docker name like `jellyfin`. Seerr signs people in through that address, and it can only be
  changed in Seerr's settings after signing in. Edit it first with
  `nano ~/.config/Seerr/settings.json`: in the `jellyfin` section, change `ip` (and `port`, if
  it differs) to an address this Mac can reach.

Then open Seerr.

### 4. Finish up

- Put the settings you noted into `~/.config/Seerr/seerr.env`, one `KEY=value` per line, and
  choose **Restart Seerr** from the menu bar icon. Docker-only options such as `PUID`, `PGID`
  or `--user` don't apply here.
- Sign in with your usual account and check that your users and requests are there. If you
  see Seerr's first-time setup screen instead, the import didn't take: don't set it up again.
  Quit Seerr and import the right folder from Terminal with `--replace`.
- In Seerr's settings, change other addresses that only worked on the old machine, such as
  `localhost` or Docker names like `radarr` and `sonarr`, to addresses this Mac can reach, and
  test each connection.
- Point any reverse proxy at this Mac.
- Keep the old config folder (and any `pg_dump`) until you're happy, then remove the old
  install.

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
