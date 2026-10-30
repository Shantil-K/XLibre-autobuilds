# XLibre-autobuilds

An automated build system for **XLibre** (Xserver). This repository monitors the official XLibre source tree, runs compilation tests, and builds binaries on an automated schedule so you don't have to deal with unmaintained PPAs or manual compilation overhead.

## Features

* **Bi-Weekly Updates:** Automatically pulls down code modifications every 12 days.
* **Manual Override:** Trigger on-demand binary compilation directly from the GitHub UI at any time.
* **Static Assets:** Bundles the output into a `.tar.gz` package and pushes it directly to a personal GitHub Release.

## Infrastructure Overview

The compilation pipeline is powered by a GitHub Actions runner using the following modern toolchain:
* **Build System:** Meson
* **Backend:** Ninja
* **Environment:** Ubuntu Linux Virtual Runner

## How to Download Your Build

1. Navigate to the **Releases** section on the right side of this repository page.
2. Download the latest compiled package (`xlibre-[commit-hash].tar.gz`).
3. Extract the binaries to your local directory:
   ```bash
   tar -xzvf xlibre-*.tar.gz
   ```

## Workflow Manual Trigger

If you know a vital update was pushed upstream and you don't want to wait for the 12-day automated cycle:
1. Go to the **Actions** tab at the top of this repository.
2. Select **Track and Build XLibre** from the left side panel.
3. Click the **Run workflow** dropdown on the right, and confirm the execution.
