# Configuration reference

All host-specific values belong in the local `.env` file. Start by copying `.env.example`; do not commit the populated file.

| Variable | Default | Purpose |
|---|---|---|
| `APPDATA_ROOT` | `/mnt/user/appdata` | Parent directory for persistent application configuration |
| `DATA_ROOT` | `/mnt/user/data` | Parent directory for media and downloads |
| `PUID` | `99` | User ID for LinuxServer.io containers |
| `PGID` | `100` | Group ID for LinuxServer.io containers |
| `TZ` | `Asia/Bahrain` | Timezone database name |
| `PLEX_CLAIM` | empty | Optional one-time Plex claim token |
| `PLEX_PORT` | `32400` | Host port for Plex |
| `SEERR_PORT` | `5055` | Host port for Seerr |
| `QBITTORRENT_WEBUI_PORT` | `8080` | Host port for qBittorrent WebUI |
| `QBITTORRENT_TORRENT_PORT` | `6881` | TCP/UDP torrent listening port |
| `PROWLARR_PORT` | `9696` | Host port for Prowlarr |
| `SONARR_PORT` | `8989` | Host port for Sonarr |
| `RADARR_PORT` | `7878` | Host port for Radarr |

## Host port versus container port

In a mapping such as `8989:8989`, the left side is the host port and the right side is the container port. Change the host side in `.env` if another application already uses that port. Internal service-to-service URLs continue to use the container port.

## Optional Plex hardware transcoding

The Compose file includes a commented `/dev/dri` device mapping for compatible Intel/AMD systems. Uncomment it only when the host exposes the device and the container has appropriate permissions. Hardware transcoding also requires compatible Plex capabilities/licensing and supported media hardware. NVIDIA setups require a different runtime/device configuration and are intentionally not assumed here.

## Security

- Keep the stack on a trusted LAN or private VPN.
- Use strong unique passwords and API keys.
- Do not publish qBittorrent, Sonarr, Radarr, Prowlarr or Seerr admin interfaces directly to the internet.
- Do not commit `.env`, credentials, API keys, Plex claim tokens, or application databases.
- If using a reverse proxy, configure TLS, authentication/access controls and trusted proxy settings appropriately.
