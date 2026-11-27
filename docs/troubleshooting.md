# Troubleshooting and maintenance

## Check container status

```bash
docker compose ps
docker compose logs --tail=100
docker compose logs --tail=100 qbittorrent
```

## Validate Compose configuration

```bash
docker compose config
```

This catches malformed YAML and unresolved environment substitutions before deployment.

## Permission errors

- Confirm `PUID` and `PGID` match the intended LinuxServer.io file ownership model.
- Confirm the host directories exist and are writable.
- Remember that Seerr's official image runs as UID `1000` by default; it does not use the LinuxServer.io `PUID`/`PGID` variables.
- Avoid broad `chmod 777` fixes. Set ownership and group permissions deliberately.

## Sonarr/Radarr cannot import downloads

- Confirm the download client host is `qbittorrent` and port is `8080`.
- Confirm category names match exactly (`tv` and `movies`).
- Confirm completed download paths and root folders are under `/data` in both containers.
- Confirm the same host `DATA_ROOT` is mounted at `/data` in qBittorrent, Sonarr and Radarr.
- Check permissions and whether the download and library directories are on the same filesystem if hardlinks are expected.

## App cannot connect to another container

Use the Compose service name (for example `sonarr`, `radarr`, or `qbittorrent`) and the container's listening port. Do not use `localhost` or the host-published port for container-to-container traffic.

## Port already in use

Change the relevant host port in `.env`. For example, `SONARR_PORT=8990` publishes host port 8990 while Sonarr continues listening on 8989 inside the Docker network.

## Backups

Back up the directories under `${APPDATA_ROOT}` while the applications are stopped or use an application-consistent backup method. Keep a separate backup of irreplaceable media and important metadata. A backup on the same physical disk is not a disaster-recovery backup.

To stop the stack:

```bash
docker compose down
```

This removes the containers and network but does not delete bind-mounted appdata or media directories. Do not use `docker compose down -v` unless you understand exactly which volumes would be removed.
