# TablePro Plugin Registry

This repository hosts the plugin registry for [TablePro](https://github.com/TableProApp/TablePro). The `plugins.json` manifest is fetched by the app's Browse tab in Settings > Plugins.

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

## Submitting a Theme

Themes are open to anyone. A theme is JSON, carries no executable code, and is verified by its
SHA-256 checksum rather than by a code signature, so nothing here needs the TablePro team.

1. Build the theme in the app: **Settings > Appearance**, then **Export**. A theme pack is several
   `.json` files in one zip.
2. Zip the `.json` files: `zip MyTheme.zip *.json`
3. Compute the checksum: `shasum -a 256 MyTheme.zip`
4. Host the zip on GitHub Releases, or any direct-download URL.
5. Open a PR adding an entry to `plugins.json` with `"category": "theme"`, your `downloadURL` and
   your `sha256`. Themes carry no native code, so they need no `binaries` array.

See [Theme Distribution](https://docs.tablepro.app/development/plugin-registry) for the schema.

## Submitting a Driver or a Format Plugin

**Third-party driver and format plugins cannot be installed today, and a PR adding one will not
work yet.** The app verifies every `.tableplugin` bundle against TablePro's own Apple Team ID, so a
bundle signed with your own Developer ID certificate is rejected at load time with "Bundle failed to
load executable". That check is deliberate, because a driver holds database credentials and runs as
code inside the app, but it also means the maintainers are the only ones who can currently publish
one.

Opening this up is planned: the app needs to accept a notarized Developer ID signature behind an
explicit per-developer trust prompt, and TableProPluginKit needs to ship as a versioned XCFramework
so a driver can live in its own repository.

Until then, the useful thing you can do is open an issue on
[TableProApp/TablePro](https://github.com/TableProApp/TablePro/issues) describing the database you
want, or say in an existing request that you are willing to write the driver. Several are open
already.

## Verified Plugins

Plugins with `"isVerified": true` have been reviewed and signed by the TablePro team. Only the TablePro maintainers can set this flag.
