# Kenji-NX Fork

## Repository Information

- **Repository:** https://git.ryujinx.app/kenji-nx/ryujinx
- **Base Commit:** `f908bfe150934296766904bf325889a7df3ac18b`
- **Patch:** `0001-occlusion-query-fix.patch`

## Quick Build

```bash
# Clone and checkout
git clone https://git.ryujinx.app/kenji-nx/ryujinx.git
cd ryujinx
git checkout f908bfe150934296766904bf325889a7df3ac18b

# Apply patch (adjust path as needed)
git apply /path/to/rk3588-ryujinx-hacks/kenji-nx/0001-occlusion-query-fix.patch

# Build
dotnet publish -c Release -r linux-arm64 -o ./publish \
  -p:DebugType=embedded \
  -p:ExtraDefineConstants=DISABLE_UPDATER \
  src/Ryujinx --self-contained
```

## Documentation

See the [main README](../README.md) for:
- Prerequisites (.NET SDK, build dependencies)
- Performance optimization (Mali-G610 specific settings)
- Technical details about the patch

## Fork-Specific Changes

This patch replaces the SDL2 package in addition to adding the occlusion query fix:
- Replaces `Ryujinx.SDL2-CS-Redux` (2.32.10) with `Ryujinx.SDL2-CS` (2.30.0-build32) from ryubing
- **Why:** `SDL2-CS-Redux` doesn't support Wayland, which is required for optimal Mali-G610 performance

## Files Modified by Patch

- `Directory.Packages.props`
- `src/Ryujinx.SDL2.Common/Ryujinx.SDL2.Common.csproj`
- `src/Ryujinx.Graphics.GAL/OcclusionQueryConfig.cs` (new)
- `src/Ryujinx.Graphics.GAL/OcclusionQueryFallbackMode.cs` (new)
- `src/Ryujinx.Graphics.Gpu/OcclusionQueryFallbackOverrides.cs` (new)
- `src/Ryujinx.Graphics.GAL/Multithreading/ThreadedRenderer.cs`
- `src/Ryujinx.Graphics.Vulkan/VulkanRenderer.cs`
- `src/Ryujinx.Graphics.Vulkan/Queries/BufferedQuery.cs`
- `src/Ryujinx.HLE/Loaders/Processes/Extensions/FileSystemExtensions.cs`
- `src/Ryujinx.HLE/Loaders/Processes/ProcessLoader.cs`
