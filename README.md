# obsidian-mgr

A tiny Bash helper to **install, update, and uninstall [Obsidian](https://obsidian.md)** on Debian/Ubuntu (and derivatives) using the official `.deb` published on GitHub releases.

Obsidian does **not** publish an apt repository, so `apt upgrade` will never update it — you normally have to find the latest release, download the right `.deb`, and install it by hand. This script automates exactly that, and skips the work when you're already up to date.

## Requirements

- A Debian-based distro with `apt` (Ubuntu, Debian, Mint, Pop!_OS, WSL2 + Ubuntu, …)
- `curl` and `dpkg` (both ship by default on these systems)
- `sudo` (or `doas`, or run as root — see [Configuration](#configuration))

## Install

```bash
curl -fLo obsidian-mgr https://raw.githubusercontent.com/<you>/obsidian-mgr/main/obsidian-mgr
chmod +x obsidian-mgr
./obsidian-mgr install
```

Optionally put it on your `PATH` so you can call it from anywhere:

```bash
mkdir -p ~/.local/bin && mv obsidian-mgr ~/.local/bin/
# add ~/.local/bin to PATH if it isn't already:
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

## Usage

```
obsidian-mgr <command>

  install [--force]   Install, or update to the latest release
  update  [--force]   Alias for install
  uninstall           Remove the package (keeps your vaults/config)
  status              Show installed vs. latest available version
```

| Command | What it does |
| --- | --- |
| `obsidian-mgr install` | Installs the latest release, or **no-ops if already current** |
| `obsidian-mgr update` | Same as `install`; run it whenever to upgrade |
| `obsidian-mgr install --force` | Reinstall even when already up to date |
| `obsidian-mgr uninstall` | `apt remove` the package — your vaults and config in `$HOME` are left untouched |
| `obsidian-mgr status` | Prints installed version vs. latest available |

## Configuration

All machine-specific settings live in one block at the top of the script:

| Variable | Default | Purpose |
| --- | --- | --- |
| `REPO` | `obsidianmd/obsidian-releases` | GitHub `owner/repo` that publishes the release assets |
| `PKG` | `obsidian` | Debian package name (used by `dpkg-query` / `apt remove`) |
| `ARCH` | `""` (auto-detect) | Target arch; leave empty to detect via `dpkg`, or set `amd64` / `arm64` to override |
| `DEB_ASSET` | `obsidian_{version}_{arch}.deb` | Release asset filename; `{version}` and `{arch}` are substituted at runtime |
| `SUDO` | `sudo` | Privilege escalation: `sudo`, `doas`, or `""` if you already run as root |

## How updating actually works

Obsidian has two independent update layers:

1. **The app itself** (the part you use daily) **auto-updates silently** inside your home directory — no apt, no sudo. This covers the vast majority of releases.
2. **The "installer"** (the Electron shell shipped in the `.deb`) updates rarely. This is the *only* layer this script manages, and Obsidian shows a "new version of the Obsidian installer is available" notice when one is out — typically a handful of times a year.

So you don't need to run `update` often; it's there for those occasional installer bumps.

## License

[MIT](LICENSE)
