# heavy-stack

Containerized self-hosted stacks for local AI workloads and game streaming/emulation.

This repository is split into two independent stacks:

- `ai-backend/`: text + image generation services for AMD GPUs (ROCm/Vulkan paths)
- `games/`: game streaming and browser emulator services

## Repository Layout

```text
heavy-stack/
├── README.md
├── ai-backend/
│   ├── README.md
│   ├── docker-compose.yml
│   └── docker/
│       └── koboldcpp/
│           └── Dockerfile
└── games/
		├── README.md
		└── docker-compose.yml
```

## Requirements

- Linux host
- Docker Engine with Compose plugin (`docker compose`)
- AMD GPU drivers and host access to:
	- `/dev/dri`
	- `/dev/kfd` (for ROCm-oriented containers)

Optional but recommended:

- Git
- Enough disk for models, ROMs, and persistent container data

## Quick Start

### 1) Clone and enter repo

```bash
git clone <your-repo-url> heavy-stack
cd heavy-stack
```

### 2) Start AI stack

```bash
cd ai-backend
docker compose up -d --build
```

### 3) Start Games stack

```bash
cd ../games
docker compose up -d
```

### 4) Check service status

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

## Service Endpoints

AI stack:

- KoboldCpp UI/API: `http://localhost:5001`
- SD.Next UI: `http://localhost:7860`

Games stack:

- EmulatorJS frontend: `http://localhost:3080`
- EmulatorJS backend/admin: `http://localhost:3300`
- Sunshine: host-networked, discover with Moonlight on LAN

## Operations

Start/stop stack:

```bash
# Start
docker compose up -d

# Stop
docker compose down

# Rebuild
docker compose up -d --build
```

View logs:

```bash
docker compose logs -f
docker compose logs -f <service-name>
```

## Configuration Notes

- `ai-backend/docker-compose.yml` supports overriding:
	- `KOBOLDCPP_RELEASE` (defaults to `v1.110`)
	- `SDNEXT_REF` (defaults to `2026-01-22`)
- You can set these via shell env vars or a local `.env` file next to the compose file.
- Ensure model/ROM paths exist before starting containers.

## Service Documentation

- AI stack guide: `ai-backend/README.md`
- Games stack guide: `games/README.md`

## Troubleshooting

- Device access problems:
	- Verify host has `/dev/dri` and `/dev/kfd`
	- Verify your user can run Docker and containers can see those devices
- Port conflicts:
	- Change host-side ports in compose files if `5001`, `7860`, `3080`, or `3300` are occupied
- Pull/build issues:
	- Retry with `docker compose build --no-cache` for failing builds
	- Validate network/DNS reachability from host

## Security

- Services are exposed on localhost/host network by default according to compose definitions.
- If opening ports to other hosts, place them behind proper auth/TLS and firewall rules.
