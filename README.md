# TablePro Plugin Registry

This repository hosts the plugin registry for [TablePro](https://github.com/datlechin/TablePro). The `plugins.json` manifest is fetched by the app's Browse tab in Settings > Plugins.

## Registry Format

`plugins.json` contains a flat array of available plugins:

```json
{
  "schemaVersion": 1,
  "plugins": [
    {
      "id": "com.example.cassandra-driver",
      "name": "Cassandra Driver",
      "version": "1.0.0",
      "summary": "Apache Cassandra database support for TablePro",
      "author": {
        "name": "Example Corp",
        "url": "https://github.com/example"
      },
      "homepage": "https://github.com/example/tablepro-cassandra",
      "category": "database-driver",
      "downloadURL": "https://github.com/example/tablepro-cassandra/releases/download/v1.0.0/CassandraDriver-arm64.zip",
      "sha256": "abc123...",
      "binaries": [
        { "architecture": "arm64", "downloadURL": "https://...arm64.zip", "sha256": "abc123..." },
        { "architecture": "x86_64", "downloadURL": "https://...x86_64.zip", "sha256": "def456..." }
      ],
      "minAppVersion": "0.16.0",
      "minPluginKitVersion": 1,
      "iconName": "cylinder.fill",
      "isVerified": false
    }
  ]
}
```

## Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | yes | Bundle identifier (must match the `.tableplugin` bundle ID) |
| `name` | string | yes | Display name |
| `version` | string | yes | Semantic version |
| `summary` | string | yes | One-line description |
| `author` | object | yes | `name` (required) and `url` (optional) |
| `homepage` | string | no | Project URL |
| `category` | string | yes | One of: `database-driver`, `export-format`, `import-format`, `theme`, `other` |
| `databaseTypeIds` | [string] | no | Maps to `DatabaseType.pluginTypeId` values for auto-install prompts |
| `downloadURL` | string | no* | Direct link to the `.zip` archive containing the `.tableplugin` bundle |
| `sha256` | string | no* | SHA-256 hex checksum of the zip file |
| `binaries` | [object] | no | Per-architecture binaries with `architecture` (`arm64` or `x86_64`), `downloadURL`, and `sha256` |
| `minAppVersion` | string | no | Minimum TablePro version required |
| `minPluginKitVersion` | int | no | Minimum TableProPluginKit version required |
| `iconName` | string | no | SF Symbol name (defaults to `puzzlepiece`) |
| `isVerified` | bool | yes | `true` if reviewed and signed by the TablePro team |

\* Either `downloadURL`/`sha256` (flat fields) or `binaries` array is required. If `binaries` is present, the app picks the binary matching the current architecture. Flat fields serve as fallback for older app versions.

## Multi-Architecture Support

Each plugin entry can include a `binaries` array with per-architecture downloads:

```json
"binaries": [
  { "architecture": "arm64", "downloadURL": "https://...arm64.zip", "sha256": "abc123..." },
  { "architecture": "x86_64", "downloadURL": "https://...x86_64.zip", "sha256": "def456..." }
]
```

The app selects the binary matching the current Mac's architecture. The flat `downloadURL`/`sha256` fields should point to arm64 for backward compatibility with older app versions that don't support the `binaries` field.

## Submitting a Plugin

1. Build your `.tableplugin` bundle following the [plugin registry docs](https://docs.tablepro.app/development/plugin-registry)
2. Code-sign it with a valid Apple Developer certificate
3. Build for both architectures (arm64 and x86_64) and create a `.zip` archive for each
4. Compute the SHA-256 checksums: `shasum -a 256 YourPlugin-arm64.zip YourPlugin-x86_64.zip`
5. Host the zips on GitHub Releases (or any direct-download URL)
6. Open a PR adding your plugin entry to `plugins.json` with a `binaries` array

## Verified Plugins

Plugins with `"isVerified": true` have been reviewed and signed by the TablePro team. Only the TablePro maintainers can set this flag.
