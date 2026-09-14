# AEX Plugins Hub

Catalog of plugins for [AEX](https://github.com/FoarteBine/AEX) (Infinite Yield FE redesign).
AEX opens it in **Settings → Plugins Hub**, or with the `hub` / `pluginhub` command.

## Layout

```
plugins.json              <- the catalog AEX reads
plugins/                  <- the .aex plugin files
  chatsound.aex
  interactable.aex
  MOMENTUM.aex
  nanfling.aex
  playercollision.aex
  serverfinder.aex
  sussy.aex
README.md
```

Raw base URL used by AEX:
`https://raw.githubusercontent.com/FoarteBine/AEXPluginsHub/refs/heads/main/`

## plugins.json

```json
{
  "Schema": 1,
  "Name": "AEX Plugins Hub",
  "UpdatedAt": "2026-09-14",
  "Notice": "Shown for humans only, AEX ignores it for now.",
  "Plugins": [
    {
      "Id": "nsfw",              // unique key, used for the installed-version record
      "Name": "NSFW",            // card title (and the letter tile)
      "Author": "FoarteBine",    // "by <author>" on the card
      "Version": "1.0.0",        // bump this to push an Update to everyone
      "Category": "Animation",   // becomes a filter chip
      "Desc": "Sussy anims",     // card description
      "File": "plugins/sussy.aex",   // repo path OR a full http(s) URL
      "SaveAs": "nsfw.aex",          // file name written into the exploit workspace
      "Icon": "",                // rbxassetid://123 or http(s) png/jpg -> else letter tile
      "Accent": "#FF4081",       // letter tile color
      "Commands": ["stopall", "annoy"]  // first 3 shown on the card, all searchable
    }
  ]
}
```

AEX reads `Plugins` (falls back to `plugins` / `Items` / a bare array), extra fields are ignored.
Missing/absent values degrade gracefully: no icon → colored tile with the first letter, no version → `1.0`, no `SaveAs` → the file name from `File`.

### Rules that actually matter

- **`File` must match the repo path exactly, including case** — raw.githubusercontent.com is
  case sensitive and does not follow redirects, so `plugins/momentum.aex` ≠ `plugins/MOMENTUM.aex`
  (that one 404s and the hub reports "could not download").
- Prefer plain file names (`a-z 0-9 _ - .`): spaces and parentheses have to be URL-encoded
  (`ServerFinder (VersionBackUp).aex` is a bad name for a raw link).
- `Version` is what the Update button compares against `aex_hub_state.json` (the version AEX
  installed for that `Id`), so keep bumping it when a plugin file changes.
- `Id` should stay stable: it is the key of the installed-version record.

## Plugin file format (what `LoadPlugin` expects)

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

`Commands` may be omitted (a plugin can also just run code on load). If several plugins register
the same command name, AEX appends a number (`mycmd1`), so a plugin may silently get renamed —
check the autocomplete list in Settings if a command "does nothing".

## Adding a plugin

1. Drop the file into `plugins/`.
2. Add an entry to `Plugins` in `plugins.json` and update `UpdatedAt`.
3. Commit. The hub's **Refresh** button and every download cache-bust with `?cb=<time>`, but
   GitHub's own CDN can still hold an object for a few minutes.

## Icons

AEX only accepts icon bytes that really are PNG/JPEG/GIF/BMP (checked by magic bytes —
`ImageLabel` cannot render WebP) and caches them as `aex/assets/hub_<Id>.<ext>`.
If the icon changes, change its URL/`Id` or delete that cached file.
