# hexcore-config

Public content for HEX CORE: the server list, server artwork and server
packages (as GitHub Releases). HEX CORE reads `config/servers.json` from this
repository, so servers can be added, removed or changed **without rebuilding
or redistributing HEX CORE**.

This repository contains only what HEX CORE shows to its users. The HEX CORE
source code lives elsewhere.

```
config/servers.json     the server list
assets/banners/         banner images (wide, e.g. 1280x480 PNG/JPG)
assets/logos/           logo images (square, e.g. 256x256 PNG)
```

HEX CORE fetches:
`https://raw.githubusercontent.com/Rudevin17/hexcore-config/main/config/servers.json`

## Add a server

1. Publish the server's package as a ZIP on a GitHub Release of a repository
   owned by **Rudevin17** (this one is fine), e.g.
   `https://github.com/Rudevin17/hexcore-config/releases/download/hellrise-v1.0.0/hellrise-package.zip`.
   Each file must be under 2 GiB.
   The ZIP contents are extracted over the user's existing game folder, so its
   paths must be relative to that folder.
2. Compute its SHA-256 (PowerShell: `Get-FileHash package.zip -Algorithm SHA256`)
   and file size in bytes (`(Get-Item package.zip).Length`).
3. Add an entry to `servers` in `config/servers.json` (see fields below).
4. Commit and push. Clients pick up the change on their next refresh.
   `raw.githubusercontent.com` is CDN-cached, so allow a few minutes.

## Remove or replace a server

Delete its entry (or replace it with a new one) and push. Users' game folders
and files are never deleted by a configuration change.

## Publish a package update

Upload the new ZIP to a new release, then update `latest_version`,
`download_url`, `file_size` and `checksum_sha256`. Installed clients will show
**Update**.

## Fields

| Field | Required | Notes |
|---|---|---|
| `schema_version` | yes (top level) | Must be `1`. |
| `config_version`, `last_updated` | no (top level) | Informational. |
| `id` | yes | `a-z`, `0-9`, `-`, `_`; max 48; unique. Never change it for an existing server (it identifies local installs). |
| `name` | yes | Display name. |
| `description`, `episode`, `rate_type` | no | Display text. |
| `max_level` | no | Integer. |
| `status` | no | `online`, `maintenance` or `offline`. |
| `featured` | no | `true` to feature on the dashboard. |
| `latest_version` | for install | Package version. Any change is offered as an update. |
| `download_url` | for install | Must be a GitHub Releases asset of a repository owned by Rudevin17. |
| `file_size` | no | Bytes. |
| `checksum_sha256` | for install | **Required.** 64 hex characters. Missing or invalid disables installation. |
| `executable` | for play | Relative path inside the game folder, e.g. `Game.exe`. |
| `launch_args` | no | Up to 16 arguments of `[A-Za-z0-9_.:=/-]`. |
| `required_files` | no | Relative paths that must exist before a folder can be linked. |
| `banner_url`, `logo_url` | no | HTTPS on `raw.githubusercontent.com` / GitHub hosts. |
| `website_url`, `discord_url` | no | HTTPS links opened in the browser. |

Invalid entries are skipped (and reported in HEX CORE's Activity page) rather
than breaking the whole list. Unknown fields are ignored.

## Testing locally

Run HEX CORE against a local file before pushing:

```powershell
HexCore.exe --config-file path\to\servers.json
```
