# Installation

## Requirements

- A Linux host or Unraid server with Docker support.
- Docker Engine and the Docker Compose plugin on Linux.
- A writable appdata location and a media/downloads location.
- Enough storage for the media library and downloads.

## Linux

Choose stable host directories. This example uses `/opt/media-stack/appdata` for application configuration and `/srv/media` for downloads and media. Adjust these to match your disks and mount points.

```bash
sudo mkdir -p /opt/media-stack/appdata/{plex,seerr,qbittorrent,prowlarr,sonarr,radarr}
sudo mkdir -p /srv/media/{media/{movies,tv},torrents/{movies,tv}}
```

Copy the environment template and edit it:

```bash
cp .env.example .env
```

Set at least:

```dotenv
APPDATA_ROOT=/opt/media-stack/appdata
DATA_ROOT=/srv/media
PUID=1000
PGID=1000
TZ=Asia/Bahrain
```

Use the UID and GID of the account that should own files created by LinuxServer.io containers. Check with `id <username>`. If your media directories are managed by a shared group, configure ownership and permissions to match that policy instead of blindly using the example.

**Seerr permission note:** the official Seerr image runs as UID `1000` by default. Ensure `/opt/media-stack/appdata/seerr` is writable by UID 1000. Do not assume the LinuxServer.io `PUID`/`PGID` variables change Seerr's user.

Validate and start:

```bash
docker compose config
docker compose pull
docker compose up -d
docker compose ps
```

## Unraid

1. Enable Docker in Unraid if it is not already enabled.
2. Clone or download this repository to a persistent location on the array or cache, for example `/mnt/user/appdata/selfhosted-media-stack/` (the repository itself may also live elsewhere).
3. Keep the default `APPDATA_ROOT=/mnt/user/appdata` and `DATA_ROOT=/mnt/user/data`, or change `DATA_ROOT` to the share you actually use.
4. Create the data directories:

   ```bash
   mkdir -p /mnt/user/data/{media/{movies,tv},torrents/{movies,tv}}
   ```

5. Copy `.env.example` to `.env`. The template uses the common Unraid `PUID=99` and `PGID=100`; confirm these match your share permissions and container strategy.
6. Ensure `/mnt/user/appdata/seerr` is writable by UID `1000` for the official Seerr image. If you choose to run Seerr as a different user, configure that explicitly and verify the image supports the chosen setup.
7. From the directory containing `docker-compose.yml`, run `docker compose config` and `docker compose up -d` in the Unraid terminal.

Alternatively, use an Unraid Compose manager that reads this Compose file. Do not also create duplicate containers from Community Applications templates.

## First launch

- Seerr: `http://SERVER-IP:5055`
- Plex: `http://SERVER-IP:32400/web`
- qBittorrent: `http://SERVER-IP:8080`
- Prowlarr: `http://SERVER-IP:9696`
- Sonarr: `http://SERVER-IP:8989`
- Radarr: `http://SERVER-IP:7878`

Replace `SERVER-IP` with the host's LAN address. If a host port is already occupied, change its left-hand mapping in `.env`; the container-side port stays unchanged.

## Updating

```bash
docker compose pull
docker compose up -d
docker image prune
```

Back up application configuration before updating. For controlled upgrades, pin image versions rather than relying on `latest`.
