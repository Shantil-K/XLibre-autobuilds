# XLibre Automated Multi-Package Builds

This repository hosts a fully automated continuous integration pipeline that tracks the upstream [XLibre Xserver development branch](https://github.com/X11Libre/xserver). Roughly every **12 days** (on the 1st, 13th and 25th of each month), a workflow fetches the latest source code, compiles it with Meson/Ninja on Ubuntu 24.04, builds `.deb`, `.rpm` and `.tar.gz` packages, and publishes them to the Releases page.

If the latest upstream commit has already been released, the scheduled run skips the build. Each release is tagged `xlibre-<short commit hash>` and links back to the upstream commit it was built from.

The workflow lives at [`.github/workflows/xlibre-build.yml`](.github/workflows/xlibre-build.yml).

## Distribution Packages Provided

Each automated build automatically compiles three package profiles to accommodate different Linux distributions:
* **Debian / Ubuntu (`.deb`)** – Fully integrated package compatible with native `apt` package routines.
* **Fedora / RHEL / CentOS (`.rpm`)** – Packaged for immediate distribution via `dnf` or `rpm`.
* **Generic Tarball (`.tar.gz`)** – Compiled application root binaries optimized for Arch Linux or independent system builds.

---

## Safe Installation & Conflict Resolution

The generated `.deb` and `.rpm` binaries contain custom package manager metadata instructions (`conflicts`, `replaces`, and `provides`). When you run a local installation command, your system package manager will:
1. **Identify Conflicts:** Find the standard Xorg server packages (`xserver-xorg-core` and `xserver-common` on Debian/Ubuntu, `xorg-x11-server-Xorg` and `xorg-x11-server-common` on Fedora/RHEL).
2. **Remove Xorg:** Remove those packages so files don't overlap.
3. **Handle Dependencies:** Pull in the runtime libraries XLibre needs. For the `.deb`, the dependency list is generated automatically from the libraries the compiled binaries actually link against. For the `.rpm`, it covers the main libraries (`glibc`, `pixman`, `libdrm`, `libepoxy`, `mesa-libgbm`, `libXfont2`, `libxcvt`, `libpciaccess`, `libxshmfence`, `libXau`, `libXdmcp`).
4. **Install XLibre:** Install every file from the build under `/usr`.

### Caveats
* On Debian/Ubuntu, replacing `xserver-xorg-core` will likely also remove installed Xorg video and input driver packages (`xserver-xorg-video-*`, `xserver-xorg-input-*`), since they depend on it.
* All packages are built on Ubuntu 24.04. The `.rpm` may not install on older Fedora/RHEL releases with an older glibc.

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

If a critical upstream fix lands on the XLibre master branch and you do not want to wait for the next scheduled build:
1. Navigate to the **Actions** tab along the top menu bar of this repository.
2. Select **Track, Build, and Package XLibre** from the sidebar list.
3. Click the **Run workflow** button on the right side.
4. Optionally tick **force** to rebuild even if the current upstream commit already has a release.
5. The new packages appear on the Releases page when the run finishes (usually a few minutes).

## How the Build Works
1. Checks out the latest `X11Libre/xserver` commit.
2. Skips the run if a release for that commit already exists (unless forced).
3. Installs Ubuntu's Xorg server build dependencies, plus `xorgproto` 2024.1 built from source (XLibre needs a newer version than Ubuntu 24.04 ships).
4. Compiles and installs XLibre into a staging folder with `--prefix=/usr`.
5. Generates the package metadata and builds the `.deb` and `.rpm` with [nFPM](https://nfpm.goreleaser.com/), plus a `.tar.gz` of `/usr`.
6. Publishes all three files to a GitHub release.
