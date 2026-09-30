# Docker Twitch Drops Miner (fork)

A personal fork of [fireph/docker-twitch-drops-miner](https://github.com/fireph/docker-twitch-drops-miner), built against [NUCL3ARN30N/TwitchDropsMiner](https://github.com/NUCL3ARN30N/TwitchDropsMiner) (itself a fork of fireph's `TwitchDropsMiner`) with additional fixes for campaign discovery and the finished-drops inventory view.

## Overview

A containerized version of Twitch Drops Miner with a native WebUI for easy management directly in your browser.

**Key benefits:**

- Native web interface that works directly in any browser
- Low memory footprint, around 80MB RAM versus 500-600MB for the desktop version
- No additional dependencies beyond Docker and a browser

## Quick Start

### Docker Run

```
docker run -d \
  --name twitch-drops-miner \
  -p 5800:5800 \
  -u 1000:1000 \
  -v /path/to/config:/TwitchDropsMiner/config \
  -v /path/to/cache:/TwitchDropsMiner/cache \
  -e TZ=America/New_York \
  ghcr.io/nucl3arn30n/twitch-drops-miner:latest
```

### Docker Compose

```
services:
  twitch-drops-miner:
    image: ghcr.io/nucl3arn30n/twitch-drops-miner:latest
    container_name: twitch-drops-miner
    ports:
      - "5800:5800"
    user: "1000:1000"
    volumes:
      - /path/to/config:/TwitchDropsMiner/config
      - /path/to/cache:/TwitchDropsMiner/cache
    environment:
      - TZ=America/New_York
    restart: unless-stopped
```

## Volume Mounts

| Path                       | Description                            | Required       |
| --------------------------- | --------------------------------------- | --------------- |
| `/TwitchDropsMiner/config` | Application settings and configuration | Yes             |
| `/TwitchDropsMiner/cache`  | Cache directory for better performance | Recommended     |

## Access

After starting the container, access the web interface at `http://localhost:5800` (or `https://localhost:5800` with `SECURE_CONNECTION=1`). No VNC client needed.

## Environment Variables

| Variable            | Description                                          | Default   |
| -------------------- | ----------------------------------------------------- | --------- |
| `TZ`                | Timezone for the container                           | `UTC`     |
| `WEBUI_HOST`        | Host address the web UI binds to                     | `0.0.0.0` |
| `WEBUI_PORT`        | Port the web UI listens on                            | `5800`    |
| `WEBUI_AUTH`        | Enable authentication for the web UI (`0`/`1`)        | `0`       |
| `SECURE_CONNECTION` | Enable HTTPS (`0`/`1`)                                | `0`       |
| `USER_ID`           | User ID for file permissions (fallback)               | `1000`    |
| `GROUP_ID`          | Group ID for file permissions (fallback)              | `1000`    |

The recommended way to set the user is via `--user`/`user:` in Docker; `USER_ID`/`GROUP_ID` are only used as a fallback when running as root.

## Configuration

1. Access the web interface at `http://localhost:5800`
2. Log in through the web interface to your Twitch account to generate `cookies.jar`
3. Adjust settings in the Settings tab or in `/TwitchDropsMiner/config/settings.json`

## Building From Source

```
git clone https://github.com/NUCL3ARN30N/docker-twitch-drops-miner.git
cd docker-twitch-drops-miner
docker build -f Dockerfile.webui -t twitch-drops-miner:latest .
docker run -d -p 5800:5800 twitch-drops-miner:latest
```

## Troubleshooting

### Permissions issues on mounted volumes

Make sure the container's user (e.g. uid/gid 1000) has read/write permissions on your mounted directories. Pass `-u <uid>:<gid>` to match your host user, or `chmod -R 777` the mounted directory.

### Reverse proxy and WebSocket support

The web interface uses WebSockets, so make sure your reverse proxy forwards `Upgrade`/`Connection` headers and uses a long `proxy_read_timeout`/tunnel timeout for long-lived connections.

### Cannot connect to Twitch / login not working

DNS-level ad blockers (AdGuard, Pi-hole, etc.) can block `beacon.twitch.tv`, which the app needs to function. Whitelist it if you run into login or connection issues.

## Security

HTTPS is available via `SECURE_CONNECTION=1`; certificates are read from `config/certs/web-privkey.pem` and `config/certs/web-fullchain.pem`, or a self-signed certificate is generated automatically if missing. Basic auth is available via `WEBUI_AUTH=1`.

## Credits

- [DevilXD/TwitchDropsMiner](https://github.com/DevilXD/TwitchDropsMiner) — the original Twitch Drops Miner project this is all built on.
- [fireph/TwitchDropsMiner](https://github.com/fireph/TwitchDropsMiner) and [fireph/docker-twitch-drops-miner](https://github.com/fireph/docker-twitch-drops-miner) — the WebUI fork and Docker packaging this repository is forked from.

## License

MIT License.
