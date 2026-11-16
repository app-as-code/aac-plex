<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="Plex logo" width="128">
  </picture>
</p>

<h1 align="center">aac-plex</h1>

<p align="center">Self-hosted deployment definition for Plex Media Server, a media server for streaming your library.</p>

<p align="center">
  <a href="https://github.com/plexinc/pms-docker"><img alt="Upstream" src="https://img.shields.io/badge/upstream-plexinc%2Fpms--docker-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-lscr.io%2Flinuxserver%2Fplex%3A1.43.0.10492--ls295-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/linuxserver/docker-plex/blob/master/LICENSE"><img alt="License" src="https://img.shields.io/badge/container%20license-GPL--3.0-green"></a>
</p>

## About

Plex Media Server organizes video, music and photo files from local storage and streams them
to clients on many devices. It indexes the library, fetches metadata and artwork, and handles
transcoding when a client cannot play a file directly. This deployment runs the
LinuxServer.io build of Plex Media Server as a Docker container with optional NVIDIA GPU
access for hardware transcoding.

Key features (as provided by upstream):

- Library indexing with metadata and artwork for movies, TV series and music
- Streaming to web, mobile, TV and desktop clients
- Remote access and sharing with other users
- Hardware-accelerated transcoding (requires a supported GPU)

## Upstream

- Project: <https://www.plex.tv>
- Server Docker repository (Plex): <https://github.com/plexinc/pms-docker>
- Container image: `lscr.io/linuxserver/plex:1.43.0.10492-121068a07-ls295` (LinuxServer.io build)
- Image documentation: <https://docs.linuxserver.io/images/docker-plex/>
- Licenses: the container packaging (LinuxServer.io) is GPL-3.0. Plex Media Server itself is
  proprietary software of Plex, Inc.

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run:

```bash
# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

The container uses host networking (`network_mode: host`), so no external Docker networks
are required. The NVIDIA runtime requires the NVIDIA Container Toolkit on the host.

Secrets are not stored in this repository. Provide them through the deployment pipeline or
create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `PUID` | `1000` | User ID the Plex process runs as |
| `PGID` | `1000` | Group ID the Plex process runs as |
| `TZ` | `Europe/Berlin` | Time zone |
| `VERSION` | `docker` | Plex update behaviour of the LinuxServer.io image |
| `PLEX_CLAIM` | `CHANGE_ME` (secret) | Claim token from plex.tv, used once to link a new server to an account. Short-lived; only needed on first start |
| `HOMEPAGE_WIDGET_KEY` | `CHANGE_ME` (secret) | Passed through to the container from the secrets block of `service.yml`; intended for the Homepage dashboard widget integration |
| `NVIDIA_VISIBLE_DEVICES` | `all` | GPUs visible to the container |
| `NVIDIA_DRIVER_CAPABILITIES` | `compute,video,utility,compat32` | Driver capabilities exposed to the container |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `32400` | TCP | Plex Media Server web interface and API (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `library` -> `/config` | Plex library database, metadata and configuration |
| `tvseries` -> `/tv` | Mount point for TV series media |
| `movies` -> `/movies` | Bind mount from host storage for movie media |

GPU: the service requests all NVIDIA GPUs with the `gpu`, `compute`, `compat32`, `graphics`,
`utility` and `video` capabilities.

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-plex>.

## Credits and license

Plex Media Server is developed by Plex, Inc. and is proprietary software. The container
packaging is provided by the LinuxServer.io project under GPL-3.0. This repository only
contains the deployment definition for it and is not a fork of the application. The logo is
a trademark of Plex, Inc.; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon
sources and licenses.
