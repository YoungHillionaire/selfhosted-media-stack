# App connections

All services share the Compose network. Use service names and container ports for connections between containers—not the host's LAN IP and not the published host port.

## qBittorrent

1. Open qBittorrent at `http://SERVER-IP:8080` and complete its initial login/security setup.
2. Set the WebUI username and a strong password.
3. Set the listening port to `6881` (or update the Compose port mapping if you choose another port).
4. Add categories `movies` and `tv` with save paths `/data/torrents/movies` and `/data/torrents/tv`.

LinuxServer.io image versions may print the initial WebUI password in the container logs. Check `docker logs qbittorrent` if needed, then change it immediately.

## Prowlarr to Sonarr and Radarr

In Prowlarr, add each application under Settings → Apps:

| Application | URL from Prowlarr |
|---|---|
| Sonarr | `http://sonarr:8989` |
| Radarr | `http://radarr:7878` |

Copy each application's API key from its Settings → General page into Prowlarr. Use the correct application type and test the connection.

Add indexers in Prowlarr. Prowlarr can then sync supported indexers to the connected Sonarr and Radarr instances.

## Sonarr and Radarr to qBittorrent

In each application, open Settings → Download Clients and add qBittorrent:

- Host: `qbittorrent`
- Port: `8080`
- Username/password: the qBittorrent WebUI credentials
- Category in Sonarr: `tv`
- Category in Radarr: `movies`

Test and save. Do not use `localhost`: inside a container, localhost refers to that same container.

Set the root folders:

- Sonarr: `/data/media/tv`
- Radarr: `/data/media/movies`

## Seerr

Complete the Seerr setup wizard at `http://SERVER-IP:5055`.

- Connect Plex using the Plex account/server selection flow.
- Add Sonarr with URL `http://sonarr:8989`, its API key, and root folder `/data/media/tv`.
- Add Radarr with URL `http://radarr:7878`, its API key, and root folder `/data/media/movies`.
- Set quality profiles and default request behavior to match your preferences.

Use container DNS names for Seerr's connections to Sonarr and Radarr. The host-published ports are for browser access from your LAN.

## Plex

Create libraries using paths inside the container:

- Movies: `/data/media/movies`
- TV Shows: `/data/media/tv`

Do not enter the host path (such as `/mnt/user/data`) in Plex. Plex sees the mounted container path `/data`.
