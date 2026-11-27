# Installation

This guide includes the actual terminal commands to download the repository and start the stack. Commands are written for a fresh setup; adjust paths if your server already has an established appdata or media layout.

## 1. Get the repository

The repository is public, so cloning over HTTPS does not require a GitHub login:

```bash
git clone https://github.com/YoungHillionaire/selfhosted-media-stack.git
cd selfhosted-media-stack
```

This creates a local folder named `selfhosted-media-stack` and checks out the default `main` branch. The commands below show where to run them on each platform.

## 2. Unraid

### Open the terminal

Open the Unraid web interface, select **Terminal** (or open an SSH session to the Unraid server), then run:

```bash
cd /mnt/user/appdata
git --version
git clone https://github.com/YoungHillionaire/selfhosted-media-stack.git
cd selfhosted-media-stack
```

If `git --version` says `command not found`, use the ZIP download method below instead. Do not install random packages into the Unraid OS just to clone this repository.

**ZIP alternative (when Git is unavailable):**

```bash
cd /mnt/user/appdata
wget -O selfhosted-media-stack.zip https://github.com/YoungHillionaire/selfhosted-media-stack/archive/refs/heads/main.zip
unzip selfhosted-media-stack.zip
mv selfhosted-media-stack-main selfhosted-media-stack
rm selfhosted-media-stack.zip
cd selfhosted-media-stack
```

If `wget` or `unzip` is unavailable, download the ZIP from the repository page in a browser, extract it, and place the extracted folder in `/mnt/user/appdata/`.

### Create the media folders and environment file

```bash
mkdir -p /mnt/user/appdata/{plex,seerr,qbittorrent,prowlarr,sonarr,radarr}
mkdir -p /mnt/user/data/{media/{movies,tv},torrents/{movies,tv}}
cp .env.example .env
```

Edit `.env` with a terminal editor if available:

```bash
nano .env
```

If `nano` is not installed, try `vi .env`, or edit the file from your computer and save it in the repository folder. The default template uses `APPDATA_ROOT=/mnt/user/appdata`, `DATA_ROOT=/mnt/user/data`, `PUID=99`, `PGID=100`, and `TZ=Asia/Bahrain`. Confirm the IDs and data share path match your Unraid setup.

### Start the stack

Run these commands from `/mnt/user/appdata/selfhosted-media-stack`:

```bash
docker compose config
docker compose pull
docker compose up -d
docker compose ps
```

If `docker compose` is not recognized, check that Docker is enabled and that your Unraid installation has a Compose manager/plugin that supports Compose files. Do not create duplicate containers from separate app templates.

## 3. Standard Linux

### Install Git

Check whether Git is installed:

```bash
git --version
```

If it is missing, install it using the package manager for your distribution. Examples:

```bash
# Debian / Ubuntu
sudo apt update && sudo apt install -y git

# Fedora
sudo dnf install -y git

# Arch Linux
sudo pacman -S --needed git
```

### Clone the repository

```bash
mkdir -p ~/stacks
cd ~/stacks
git clone https://github.com/YoungHillionaire/selfhosted-media-stack.git
cd selfhosted-media-stack
```

### Create host folders and configure `.env`

This example uses `/opt/media-stack/appdata` for application configuration and `/srv/media` for downloads and media. Change these paths if your disks are mounted elsewhere.

```bash
sudo mkdir -p /opt/media-stack/appdata/{plex,seerr,qbittorrent,prowlarr,sonarr,radarr}
sudo mkdir -p /srv/media/{media/{movies,tv},torrents/{movies,tv}}
sudo chown -R "$(id -u):$(id -g)" /opt/media-stack/appdata /srv/media
cp .env.example .env
nano .env
```

Set these values in `.env` (replace the IDs with the output of `id` for the account that should own media files):

```dotenv
APPDATA_ROOT=/opt/media-stack/appdata
DATA_ROOT=/srv/media
PUID=1000
PGID=1000
TZ=Asia/Bahrain
```

Seerr's official image runs as UID `1000` by default. Ensure `/opt/media-stack/appdata/seerr` is writable by UID 1000; the LinuxServer.io `PUID` and `PGID` variables do not change Seerr's user.

### Start the stack

Run from `~/stacks/selfhosted-media-stack`:

```bash
docker compose config
docker compose pull
docker compose up -d
docker compose ps
```

Make sure Docker Engine and the Docker Compose plugin are installed and the current user can access Docker (or prefix the commands with `sudo` if that is how Docker is administered on your system).

## 4. Open the applications

Replace `SERVER-IP` with your Unraid/Linux host's LAN IP address:

| Service | URL |
|---|---|
| Seerr | `http://SERVER-IP:5055` |
| Plex | `http://SERVER-IP:32400/web` |
| qBittorrent | `http://SERVER-IP:8080` |
| Prowlarr | `http://SERVER-IP:9696` |
| Sonarr | `http://SERVER-IP:8989` |
| Radarr | `http://SERVER-IP:7878` |

Complete the initial setup and connect the applications using [App connections](app-connections.md).

## 5. Updating later

After new changes are published to GitHub, update the local checkout from its repository folder:

```bash
cd /path/to/selfhosted-media-stack
git pull
docker compose config
docker compose pull
docker compose up -d
```

Replace `/path/to/selfhosted-media-stack` with the actual location used when cloning. Back up appdata before upgrades. If you edited tracked files locally, review the changes before pulling.

## 6. Useful commands

Run these from the folder containing `docker-compose.yml`:

```bash
# Show container status
docker compose ps

# Follow logs for all services
docker compose logs -f

# Follow logs for one service
docker compose logs -f sonarr

# Stop containers (keeps bind-mounted data)
docker compose down

# Start containers again
docker compose up -d
```

## Storage and permissions notes

- The default Unraid paths are `/mnt/user/appdata` and `/mnt/user/data`. If your media share has a different name, change `DATA_ROOT` in `.env` and create the matching folder layout there.
- LinuxServer.io containers use `PUID` and `PGID`; use IDs appropriate for your host and shared storage policy.
- Seerr's official image runs as UID `1000` by default. Make its config directory writable by that UID.
- Keep the same host data directory mounted at `/data` in qBittorrent, Sonarr, Radarr and Plex. This consistent path supports imports and hardlinks when source and destination are on the same filesystem.
- Avoid exposing admin interfaces directly to the public internet. Use a private VPN or a properly secured reverse proxy.
- Never commit your populated `.env` file. It may contain private tokens or other sensitive values.

## Updating

Back up application configuration before updating. For controlled upgrades, pin image versions rather than relying on `latest`.
