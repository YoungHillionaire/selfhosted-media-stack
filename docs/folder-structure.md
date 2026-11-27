# Folder structure and hardlinks

## Recommended layout

```text
DATA_ROOT/
├── media/
│   ├── movies/
│   └── tv/
└── torrents/
    ├── movies/
    └── tv/
```

Use the same `/data` container mount in qBittorrent, Sonarr, Radarr and Plex. This repository does that by mounting the same host `DATA_ROOT` at `/data`.

## Configure the download client

In qBittorrent, create categories:

| Category | Save path |
|---|---|
| `movies` | `/data/torrents/movies` |
| `tv` | `/data/torrents/tv` |

Keep incomplete downloads in a subfolder such as `/data/torrents/incomplete` if desired. If you change the paths, use the same paths consistently in the *arr applications.

## Root folders

- Sonarr root folder: `/data/media/tv`
- Radarr root folder: `/data/media/movies`
- Plex movie library: `/data/media/movies`
- Plex TV library: `/data/media/tv`

## Why hardlinks matter

With completed downloads and the library on the same filesystem, Sonarr and Radarr can create hardlinks instead of copying the entire file. This saves space and allows the torrent client to continue seeding while the library has its own organized path.

Hardlinks generally require source and destination to be on the same filesystem. A common mistake is placing downloads on one share/disk and the library on a different filesystem. Docker path consistency alone cannot make cross-filesystem hardlinks possible.

On Unraid, use a storage/share arrangement that keeps the relevant download and media paths on the same underlying filesystem when you want hardlinks. Review share settings and cache/array behavior for your own layout.

## Avoid Remote Path Mappings when possible

Because qBittorrent and Sonarr/Radarr all see the same files under `/data`, configure the download client host as `qbittorrent` and use matching paths. A Remote Path Mapping should not be needed in this single Compose network arrangement.
