# Self-hosted Media Stack

A six-container Docker Compose media automation stack for **Linux** and **Unraid**. It brings together Plex for playback, Seerr for requests, qBittorrent for downloads, and the *arr applications for library automation.

## Services

| Service | Purpose | Default URL |
|---|---|---|
| [Plex](https://www.plex.tv/) | Media server and playback | `http://HOST:32400/web` |
| [Seerr](https://docs.seerr.dev/) | Media discovery and requests | `http://HOST:5055` |
| [qBittorrent](https://www.qbittorrent.org/) | BitTorrent download client | `http://HOST:8080` |
| [Prowlarr](https://prowlarr.com/) | Indexer management | `http://HOST:9696` |
| [Sonarr](https://sonarr.tv/) | TV series management | `http://HOST:8989` |
| [Radarr](https://radarr.video/) | Movie management | `http://HOST:7878` |

Only these six application containers are defined. Docker Compose creates the project network automatically; no extra proxy, database, VPN, or updater container is included.

## Quick start

1. Install Docker Engine and the Docker Compose plugin (or enable Docker on Unraid).
2. Clone this repository and enter its directory.
3. Copy `.env.example` to `.env`, then adjust paths, IDs, timezone and ports.
4. Create the host directories and ensure the containers can write to them.
5. Validate and start the stack:

   ```bash
   cp .env.example .env
   docker compose config
   docker compose pull
   docker compose up -d
   ```

6. Open Seerr at `http://HOST:5055` and complete its setup wizard.
7. Configure the connections described in [App connections](docs/app-connections.md).

Platform-specific steps are in [Installation](docs/installation.md).

## Storage layout

The default layout keeps application configuration separate from media and downloads:

```text
${APPDATA_ROOT}/
├── plex/
├── seerr/
├── qbittorrent/
├── prowlarr/
├── sonarr/
└── radarr/

${DATA_ROOT}/
├── media/
│   ├── movies/
│   └── tv/
└── torrents/
    ├── movies/
    └── tv/
```

All media-aware containers mount the same host data directory at `/data`. This consistent path is important for hardlinks and avoids Remote Path Mappings in a normal single-host setup. See [Folder structure and hardlinks](docs/folder-structure.md).

## Important notes

- **Set permissions deliberately.** LinuxServer.io containers use `PUID` and `PGID`; Seerr's official image runs as UID `1000` by default. See the installation guide before first launch.
- **Do not expose these admin interfaces directly to the public internet.** Use a trusted VPN or a properly secured reverse proxy with authentication and TLS.
- **Keep `.env` private.** It is ignored by Git. Never put API keys, passwords, claim tokens or other secrets in committed files.
- **Back up appdata** before upgrades or major configuration changes.
- `latest` tags are convenient but are not immutable. For a stability-focused deployment, pin tested image versions and update intentionally.
- Hardware transcoding is optional and host-specific; the `/dev/dri` mapping is commented out in the Compose file.

## Documentation

- [Installation: Linux and Unraid](docs/installation.md)
- [Folder structure and hardlinks](docs/folder-structure.md)
- [App connections and recommended paths](docs/app-connections.md)
- [Configuration reference](docs/configuration.md)
- [Troubleshooting and maintenance](docs/troubleshooting.md)

## Scope

This repository is a portable starting point, not a one-size-fits-all security or storage policy. Review host paths, permissions, firewall rules, download-client settings and application versions before using it with valuable data.

## License

No license has been added yet. Until one is selected, normal copyright applies and reuse is not automatically granted.
