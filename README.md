# RK3588 Ryujinx Patches

Patches and build guides for Ryujinx on RK3588 and Mali-G610 GPUs.

## Problem

On ARM Mali-G610, Vulkan(libmali) drivers fail to write occlusion query results back to host-visible memory. The result buffer stays stuck at the sentinel value (`0xFFFFFFFEFFFFFFFE`), causing games to freeze indefinitely while waiting for query results.

This repository contains patches that detect stuck queries after 5000+ attempts and provide fallback values to keep games running.

## Fixed Games

- **MK8 Deluxe** (0100152000022000)
- **Crash Bandicoot 4: It's About Time** (010073401175e000)
- **Little Nightmares** series (01002fc00412c000, 010097100edd6000, 010066101a55a000)
- **Princess Peach: Showtime!** (01007a3009184000)

## Scope & Support

This repository **only** provides patches for Mali GPU-specific issues (occlusion queries and similar fixes). Support is limited to:
- Adding title IDs for games with similar freezing issues
- Updating patches when upstream forks release new versions

For all other emulator-related issues (gameplay, compatibility, performance, features, etc.), please visit the upstream Ryujinx fork repositories.

## Available For

- **[ryubing fork](ryubing/README.md)**
- **[kenji-nx fork](kenji-nx/README.md)** 

## Quick Start

### Option 1: Download Pre-built Binaries (Recommended)

Pre-built binaries are available in [GitHub Releases](../../releases):

### Option 2: Build from Source

See the [Building](#building) section below for detailed instructions.

## Prerequisites

### Install .NET SDK 9.0

```bash
# Download and install .NET SDK 9.0
wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh
chmod +x dotnet-install.sh
./dotnet-install.sh --channel 9.0

# Add to PATH (add to ~/.bashrc for persistence)
export DOTNET_ROOT=$HOME/.dotnet
export PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools
```

Verify installation:
```bash
dotnet --version  # Should show 9.0.x or higher
```

## Building

See fork-specific READMEs for repository URLs and commit hashes:
- [ryubing/README.md](ryubing/README.md)
- [kenji-nx/README.md](kenji-nx/README.md)

General build steps:

```bash
# 1. Clone the repository (see fork README for URL)
git clone <fork-url>
cd ryujinx

# 2. Checkout base commit (see fork README for hash)
git checkout <base-commit>

# 3. Apply patch
git apply /path/to/rk3588-ryujinx-hacks/<fork>/0001-occlusion-query-fix.patch

# 4. Build
dotnet publish -c Release -r linux-arm64 -o ./publish \
  -p:DebugType=embedded \
  -p:ExtraDefineConstants=DISABLE_UPDATER \
  src/Ryujinx --self-contained
```

## Performance Optimization

For Mali-G610 with libmali driver, use headless mode with Wayland for best performance:

```bash
SDL_VIDEODRIVER=wayland ./publish/Ryujinx \
    --no-gui \
    --disable-docked-mode \
    --exclusive-fullscreen \
    --exclusive-fullscreen-width 1280 \
    --exclusive-fullscreen-height 720 \
    --graphics-backend Vulkan \
    --resolution-scale 0.75 \
    /path/to/game.nsp
```

## What the Patch Does

**The Problem:**
Ryujinx creates host-visible GPU buffers for occlusion queries and writes a sentinel value (`0xFFFFFFFEFFFFFFFE`) to mark them as "waiting for result". After issuing the query, it polls the buffer expecting the Vulkan driver to overwrite the sentinel with the actual result. On Mali GPUs, the driver never writes the result back to host memory, leaving the buffer stuck at the sentinel value. Games polling for these results wait indefinitely and freeze.

**The Fix:**
After 5000 polling attempts with no change (~5 seconds), the patch detects the stuck query and writes a fallback value (0 or 1) to the buffer. Game-specific overrides determine which fallback to use:
- **ForceNonZero** (value: 1) - For games that need visible pixels reported (Mario Kart 8, etc.)
- **ForceZero** (value: 0) - For games expecting hidden geometry

Overrides are automatically applied based on title ID when games load.

**Tested Configuration:**
- **Hardware**: RK3588 SoCs with Mali G610
- **Kernel**: 6.1.115
- **OS**: Armbian Ubuntu Noble (24.04)
- **Desktop**: GNOME with Wayland
- **Mali Drivers**: libmali-rockchip v1.9-1 (g24p0)
