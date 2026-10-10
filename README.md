# TablePro plugin registry

`plugins.json` is the catalog [TablePro](https://github.com/TableProApp/TablePro) reads for **Settings > Plugins > Browse**, and to install a driver when you pick a database type that has none yet. The app fetches it from `https://raw.githubusercontent.com/TableProApp/plugins/main/plugins.json`.

## Where it comes from

CI writes `plugins.json`.

- The source of truth is [`.github/plugin-registry.json`](https://github.com/TableProApp/TablePro/blob/main/.github/plugin-registry.json) in the TablePro repository: one entry per plugin, keyed by its tag slug.
- Pushing a `plugin-<slug>-v<version>` tag there runs [`build-plugin.yml`](https://github.com/TableProApp/TablePro/blob/main/.github/workflows/build-plugin.yml). It builds both architectures, signs and notarizes them, publishes a GitHub release, and rewrites the plugin's entry here with [`update-registry.py`](https://github.com/TableProApp/TablePro/blob/main/.github/scripts/update-registry.py).
- An entry's optional `metadata` block is the one part CI does not write. A maintainer edits it here by hand, and CI carries it forward on every release.

To change a plugin, its summary or its icon, open a pull request against [TableProApp/TablePro](https://github.com/TableProApp/TablePro). A pull request here that edits any other field is closed, because the plugin's next release overwrites it.

## Format

Schema version 2. The field reference is [Plugin Registry](https://docs.tablepro.app/development/plugin-registry) in the docs. A real entry:

```json
{
  "schemaVersion": 2,
  "plugins": [
    {
      "id": "com.TablePro.HanaDriver",
      "name": "SAP HANA Driver",
      "version": "1.0.1",
      "summary": "SAP HANA SQL driver via SAP/go-hdb",
      "author": { "name": "TablePro", "url": "https://tablepro.app" },
      "homepage": "https://docs.tablepro.app/databases/sap-hana",
      "category": "database-driver",
      "databaseTypeIds": ["SAP HANA"],
      "iconName": "cylinder",
      "isVerified": true,
      "minAppVersion": "0.77.0",
      "binaries": [
        {
          "architecture": "arm64",
          "pluginKitVersion": 34,
          "downloadURL": "https://github.com/TableProApp/TablePro/releases/download/plugin-hana-v1.0.0/HanaDriver-arm64.zip",
          "sha256": "561e5dd963f45c530a50acd8d87de534aca79bcf82f59201f136f0740abf807f",
          "minAppVersion": "0.77.0"
        },
        {
          "architecture": "x86_64",
          "pluginKitVersion": 34,
          "downloadURL": "https://github.com/TableProApp/TablePro/releases/download/plugin-hana-v1.0.0/HanaDriver-x86_64.zip",
          "sha256": "7a77e006f5eb73fd158c2e0dcef07ff090ed74b238a02641951318b9397c4ef9",
          "minAppVersion": "0.77.0"
        },
        {
          "architecture": "arm64",
          "pluginKitVersion": 36,
          "downloadURL": "https://github.com/TableProApp/TablePro/releases/download/plugin-hana-v1.0.1/HanaDriver-arm64.zip",
          "sha256": "977be0c454aebd5f13eb2ea305af2c73de97ef016ca0794c087591f1c70b0826",
          "minAppVersion": "0.79.0"
        },
        {
          "architecture": "x86_64",
          "pluginKitVersion": 36,
          "downloadURL": "https://github.com/TableProApp/TablePro/releases/download/plugin-hana-v1.0.1/HanaDriver-x86_64.zip",
          "sha256": "e1155151680503afc245b65a98231802573f94973878a509ac0013b1f4deaf41",
          "minAppVersion": "0.79.0"
        }
      ]
    }
  ]
}
```

- `binaries` has one entry per architecture and PluginKit version. The app installs the highest `pluginKitVersion` it supports for its architecture. CI keeps the three newest PluginKit versions of each plugin.
- The entry's `minAppVersion` is the lowest app version any of its binaries needs. An older app refuses the install before it downloads anything. CI also records `minAppVersion` on each binary, but the app reads only the entry's.
- `category` is one of `database-driver`, `export-format`, `import-format`, `theme`, `other`, and `architecture` is `arm64` or `x86_64`. An app that meets any other value rejects the whole file and keeps its cached copy, so a new value needs an app release first.
- An app that supports a lower `schemaVersion` than the file declares also keeps its cached copy.
- `isVerified` is `true` on every entry CI writes, and Browse shows it as a badge. The app grants no trust on the strength of it.

## Plugins from other developers

TablePro installs plugins signed by other developers:

- The bundle must carry a valid Developer ID signature. Unsigned and ad-hoc signed bundles are refused.
- The first time it meets a developer, TablePro names them and their Team ID and asks whether to trust them. This happens for a file install and for a registry install alike. Trust covers every plugin from that developer, and can be withdrawn in **Settings > Plugins**.

Third-party plugins are not listed in this registry. Distribute yours one of two ways:

- Users drop the `.zip` or `.tableplugin` onto **Settings > Plugins > Installed**, or pick it with the **+** button there.
- Host a manifest in this same format, and users point TablePro at it. It replaces this registry for them rather than adding to it:

  ```bash
  defaults write com.TablePro com.TablePro.customRegistryURL "https://example.com/plugins.json"
  ```

[Plugins](https://docs.tablepro.app/features/plugins#plugins-from-other-developers) covers what users see. [Plugin Development](https://docs.tablepro.app/development/plugin-development#building-outside-this-repository) covers building a plugin outside the TablePro repository.

To have a plugin in this registry, contribute it to the TablePro repository. TablePro's CI then builds and signs it.

## Security

Report a vulnerability in a plugin as described in [SECURITY.md](https://github.com/TableProApp/TablePro/blob/main/SECURITY.md).
