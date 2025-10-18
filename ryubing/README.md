# Ryubing Fork

## Repository Information

- **Repository:** https://git.ryujinx.app/ryubing/ryujinx
- **Base Commit:** `fdbdb05cb583a1604ada310d160791bd945f758e`
- **Patch:** `0001-occlusion-query-fix.patch`

## Quick Build

```bash
# Clone and checkout
git clone https://git.ryujinx.app/ryubing/ryujinx.git
cd ryujinx
git checkout fdbdb05cb583a1604ada310d160791bd945f758e

# Apply patch (adjust path as needed)
git apply /path/to/rk3588-ryujinx-hacks/ryubing/0001-occlusion-query-fix.patch

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

## Files Modified by Patch

- `src/Ryujinx.Graphics.GAL/OcclusionQueryConfig.cs` (new)
- `src/Ryujinx.Graphics.GAL/OcclusionQueryFallbackMode.cs` (new)
- `src/Ryujinx.Graphics.Gpu/OcclusionQueryFallbackOverrides.cs` (new)
- `src/Ryujinx.Graphics.GAL/Multithreading/ThreadedRenderer.cs`
- `src/Ryujinx.Graphics.Vulkan/VulkanRenderer.cs`
- `src/Ryujinx.Graphics.Vulkan/Queries/BufferedQuery.cs`
- `src/Ryujinx.HLE/Loaders/Processes/Extensions/FileSystemExtensions.cs`
- `src/Ryujinx.HLE/Loaders/Processes/ProcessLoader.cs`
