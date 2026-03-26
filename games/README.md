# Games Stack

Containerized game-oriented services:

- `sunshine`: LAN game streaming host for Moonlight clients
- `emulatorjs`: browser-based emulator frontend/backend

## Requirements

- Linux host with Docker + Compose
- GPU/video encode access via `/dev/dri`
- Input and uinput devices for Sunshine:
  - `/dev/input`
  - `/dev/uinput`

## Directory Preparation

Create persistent data folders:

```bash
mkdir -p sunshine/config emulatorjs/config emulatorjs/data
```

## Start

```bash
docker compose up -d
```

## Stop

```bash
docker compose down
```

## Endpoints

- EmulatorJS frontend: http://localhost:3080
- EmulatorJS backend/admin: http://localhost:3300
- Sunshine: host network mode (discover from Moonlight client on LAN)

## Service Details

### sunshine

- Image: `lizardbyte/sunshine:latest-ubuntu-22.04`
- Runs with:
  - `network_mode: host`
  - `privileged: true`
- Mounts config path:
  - `./sunshine/config:/config`
- Device and input mappings include:
  - `/dev/dri`
  - `/dev/input`
  - `/dev/uinput`

Because Sunshine uses host networking, it does not expose mapped Docker ports in compose.

### emulatorjs

- Image: `linuxserver/emulatorjs:latest`
- Port mappings:
  - `3080:80` for web frontend
  - `3300:3000` for backend/admin
- Data mounts:
  - `./emulatorjs/config:/config`
  - `./emulatorjs/data:/data`

Optional ROM mount examples are included in `docker-compose.yml`.

## Common Operations

Tail logs:

```bash
docker compose logs -f
docker compose logs -f sunshine
docker compose logs -f emulatorjs
```

Restart one service:

```bash
docker compose restart emulatorjs
```

## Troubleshooting

- Moonlight cannot find Sunshine:
  - Ensure both devices are on same LAN.
  - Confirm host firewall allows Sunshine traffic.
  - Verify Sunshine container is running with `network_mode: host`.
- Controller/input problems:
  - Verify `/dev/input` and `/dev/uinput` are present and mapped.
- EmulatorJS library not visible:
  - Confirm ROMs are placed under the expected data path or mounted read-only from host.
