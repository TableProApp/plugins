# TablePro plugin registry (frozen)

`plugins.json` is the plugin catalog [TablePro](https://github.com/TableProApp/TablePro) 0.81 and earlier read from `https://raw.githubusercontent.com/TableProApp/plugins/main/plugins.json`. It no longer changes.

TablePro 0.82 and later read `https://updates.tablepro.app/plugins/v3.json`, which each TablePro release writes. See [Plugin Registry](https://docs.tablepro.app/development/plugin-registry).

## The frozen file

- It holds the last builds published for PluginKit 38 and below. An app installs the highest `pluginKitVersion` it supports, so 0.81 (PluginKit 38) and 0.80 (PluginKit 37) keep installing them.
- PluginKit 39 builds, from 0.82.0 on, are listed only in `v3.json`.

## Format

Schema version 2.

- `binaries` has one entry per architecture and PluginKit version.
- The entry's `minAppVersion` is the lowest app version any of its binaries needs. An older app refuses the install before it downloads anything. The app ignores the per-binary `minAppVersion`.
- `architecture` is `arm64` or `x86_64`. An app that meets any other value rejects the whole file and keeps its cached copy.
- An app that supports a lower `schemaVersion` than the file declares also keeps its cached copy.

## Security

Report a vulnerability in a plugin as described in [SECURITY.md](https://github.com/TableProApp/TablePro/blob/main/SECURITY.md).
