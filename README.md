# XLibre Automated Multi-Package Builds

This repository hosts a fully automated continuous integration pipeline that tracks the upstream [XLibre Xserver development branch](https://github.com). Every **12 days**, a workflow automatically fetches the absolute latest source code, compiles it using the native Meson/Ninja toolkit, builds native package files with explicit runtime dependencies, and sets up seamless drops to the Releases page.

## Distribution Packages Provided

Each automated build automatically compiles three package profiles to accommodate different Linux distributions:
* **Debian / Ubuntu (`.deb`)** – Fully integrated package compatible with native `apt` package routines.
* **Fedora / RHEL / CentOS (`.rpm`)** – Packaged for immediate distribution via `dnf` or `rpm`.
* **Generic Tarball (`.tar.gz`)** – Compiled application root binaries optimized for Arch Linux or independent system builds.

---

## Safe Installation & Conflict Resolution

The generated `.deb` and `.rpm` binaries contain custom package manager metadata instructions (`conflicts`, `replaces`, and `provides`). When you run a local installation command, your system package manager will:
1. **Identify Conflicts:** Locate any active instances of standard `xserver-xorg` or `xserver-xorg-core`.
2. **Remove Xorg cleanly:** Purge those packages from your system directory space to prevent binary overlaps.
3. **Handle Dependencies:** Query upstream network mirrors to automatically resolve and pull in platform dependencies like `libc6`, `libx11-6`, `libxext6`, `libxrender1`, and `libxi6`.
4. **Deploy XLibre:** Bind the new high-performance XLibre graphics layers directly to `/usr/bin/` and `/usr/lib/`.

### 1. Installation on Ubuntu / Debian Derivatives (`apt`)
```bash
# Download your built target file from the release sidebar, then run:
sudo apt update
sudo apt install ./xlibre_*_amd64.deb
```

### 2. Installation on Fedora / RHEL Distributions (`dnf`)
```bash
# Download the target .rpm file from the release sidebar, then run:
sudo dnf localinstall ./xlibre-*.x86_64.rpm
```

### 3. Installation on Arch Linux / Slackware / Standalone (`tar.gz`)
```bash
# Extract the core layout directly into your local software directory
sudo tar -xzvf xlibre-*.tar.gz -C /usr/local/
```

---

## Reverting to Standard Xorg
If you ever need to uninstall XLibre and re-establish the standard display configuration stack, simply query your package manager to pull the native upstream server suite back into your workspace:

```bash
# On Debian / Ubuntu platforms:
sudo apt update
sudo apt purge xlibre
sudo apt install xorg xserver-xorg-core

# On Red Hat / Fedora platforms:
sudo dnf remove xlibre
sudo dnf install xorg-x11-server-Xorg
```

---

## Manual Build Override

If a critical upstream fix lands on the XLibre master branch and you do not want to wait for the standard 12-day build interval:
1. Navigate to the **Actions** tab along the top menu bar of this repository.
2. Select **Track, Build, and Package XLibre** from the sidebar list.
3. Click the **Run workflow** interactive button located on the right side.
4. The system will cleanly rebuild the application stack and update the repository releases page within minutes.
