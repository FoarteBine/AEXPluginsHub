# AEX Plugins Hub

Catalog of plugins for [AEX](https://github.com/FoarteBine/AEX) (Infinite Yield FE redesign).
AEX opens this list in **Settings → Manage Plugins → Plugins Hub**.

## Layout

```
plugins.json          <- the catalog AEX reads
plugins/              <- the actual .aex plugin files
  test.aex
README.md
```

Raw base URL used by AEX:
`https://raw.githubusercontent.com/FoarteBine/AEXPluginsHub/refs/heads/main/`

## plugins.json

```json
{
  "Schema": 1,
  "Name": "AEX Plugins Hub",
  "UpdatedAt": "2026-09-12",
  "Notice": "Optional text shown at the bottom of the hub window.",
  "Plugins": [
    {
      "Id": "test",              // unique, lowercase, no spaces (used as a key)
      "Name": "Test Plugin",     // shown as the card title
      "Author": "Sasha",         // shown as "by <author>"
      "Version": "1.0.0",        // compared with the installed copy -> "Update"
      "Category": "Example",     // filter chip in the hub
      "Desc": "What it does.",   // 2 lines shown on the card
      "File": "plugins/test.aex",// repo path OR a full https:// URL
      "SaveAs": "test.aex",      // file name written into the exploit workspace (optional, defaults to File's name)
      "Icon": "",                // rbxassetid://123 or a https:// png/jpg (optional -> letter tile)
      "Accent": "#6366F1",       // icon tile color (optional)
      "Commands": ["test"]       // shown on the card, cosmetic only
    }
  ]
}
```

AEX reads `Plugins` (falls back to `plugins` / `Items`), so key order/extra fields are fine.
Unknown/absent fields just degrade: no icon → colored tile with the first letter, no version → `1.0`.

## Adding a plugin

1. Drop the file into `plugins/` (must be a valid AEX/IY plugin table — see `plugins/test.aex`).
2. Add an entry to `Plugins` in `plugins.json`, bump its `Version`, update `UpdatedAt`.
3. Commit. Raw GitHub caches up to ~5 minutes; the hub's **Refresh** button cache-busts the catalog.

Plugin file format expected by AEX's `LoadPlugin`:

```lua
return {
    PluginName = "My Plugin",
    PluginDescription = "What it does",
    Commands = {
        mycmd = {
            Aliases = { "mc" },
            Description = "What mycmd does",
            Function = function(args, speaker) end,
        },
    },
}
```

## Notes

- Installing writes `SaveAs` (or the file name from `File`) into the exploit's workspace folder,
  then calls `addPlugin` — the plugin appears in **Settings → Manage Plugins** and is reloaded on
  every launch from `AEX_FE.aex` settings.
- AEX only downloads icon images when the bytes really are PNG/JPEG/GIF/BMP (WebP is not rendered
  by `ImageLabel`), otherwise it keeps the letter tile.
