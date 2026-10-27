# gpui-fast, patched for NerdRaw

The gpui-fast used by NerdRaw's GPUI shell for Linux and Windows.

It starts from the sources published on crates.io rather than the git repository, so the package names
(`gpui-fast`, `gpui-fast-linux`, `gpui-fast-windows`) and versions match what gpui-component depends on,
and a single `[patch.crates-io]` entry replaces gpui-fast across the whole dependency graph.
The crates sit under upstream [longbridge/gpui-fast](https://github.com/longbridge/gpui-fast)'s `crates/` paths,
so the patches apply to both.

- The first commit is the unchanged 0.1.12 release (the same sources as longbridge/gpui-fast 07b42eb).
  Each patch follows as its own commit.
- Each patched release is tagged `nerdraw-<version>-<n>`. Depend on a tag, not on the branch:
  upgrading rewrites the branch.
- To upgrade, import the new release as the first commit and cherry-pick the patches onto it,
  dropping those that upstream has merged.

## Using it

```toml
[patch.crates-io]
gpui-fast = { git = "https://github.com/firede/gpui-fast", tag = "nerdraw-0.1.12-1" }
gpui-fast-linux = { git = "https://github.com/firede/gpui-fast", tag = "nerdraw-0.1.12-1" }
gpui-fast-windows = { git = "https://github.com/firede/gpui-fast", tag = "nerdraw-0.1.12-1" }
```

## Patches

| Patch | Why |
|---|---|
| `Window::paint_glyph_scaled` | Zooming text reuses its raster, scaled on the GPU with an unsnapped origin; without it, zooming drops about 30% of frames or text jumps in size |
| Wayland clipboard | Structured content across processes (a custom MIME type named by `GPUI_CLIPBOARD_METADATA_MIME`), and images on the clipboard |
| Runtime shader compilation on Windows | Cross-compiling the Windows build from Linux or macOS |
| Mouse up counts as mouse input | After a key press during a drag, the release was dropped and the drag's last move lost (zed-industries/zed#64614) |
| Public `Window::with_element_opacity` | Fading images that an element paints itself; `paint_image` takes no color |

All crates are licensed under Apache-2.0, like upstream.
