# AI Backend Stack

Self-hosted AI services focused on AMD-friendly acceleration paths:

- `koboldcpp`: local LLM inference for GGUF models
- `sdnext`: image generation stack from the SD.Next project

## Files

- `docker-compose.yml`: service definitions
- `docker/koboldcpp/Dockerfile`: custom image build from a selectable release artifact

## Requirements

- Linux host with Docker + Compose
- AMD GPU support on host
- Host devices available:
  - `/dev/dri`
  - `/dev/kfd`

## Directory Preparation

Create expected local directories before first run:

```bash
mkdir -p \
  models \
  models/stable-diffusion \
  sdnext/app \
  sdnext/python \
  sdnext/data \
  sdnext/huggingface
```

For KoboldCpp, place at least one GGUF model in `./models`.

## Configuration

Optional environment variables in `docker-compose.yml`:

- `KOBOLDCPP_RELEASE` (default: `v1.110`)
- `SDNEXT_REF` (default: `2026-01-22`)

Example temporary override:

```bash
KOBOLDCPP_RELEASE=v1.111 SDNEXT_REF=2026-02-01 docker compose up -d --build
```

Or use a local `.env` file in this folder:

```dotenv
KOBOLDCPP_RELEASE=v1.110
SDNEXT_REF=2026-01-22
```

## Start

```bash
docker compose up -d --build
```

## Stop

```bash
docker compose down
```

## Endpoints

- KoboldCpp: http://localhost:5001
- SD.Next: http://localhost:7860

## Service Details

### koboldcpp

- Built from `docker/koboldcpp/Dockerfile`
- Uses release artifact selected by `KOBOLDCPP_RELEASE`
- Default command includes:
  - model path: `/models/MyModel.gguf`
  - Vulkan mode: `--usevulkan`
  - GPU layers: `--gpulayers 60`
  - threads: `--threads 4`

Adjust these values in `docker-compose.yml` for your model and hardware.

If Vulkan mode is not supported for your setup, the compose file documents an AMD fallback with CLBlast.

### sdnext

- Builds from remote Git source with ref `SDNEXT_REF`
- Uses ROCm-oriented Dockerfile from SD.Next: `configs/Dockerfile.rocm`
- Persistent paths are mounted under `./sdnext/*` and `./models/stable-diffusion`

## Common Operations

Show logs:

```bash
docker compose logs -f
docker compose logs -f koboldcpp
docker compose logs -f sdnext
```

Restart a service:

```bash
docker compose restart koboldcpp
```

Rebuild cleanly:

```bash
docker compose build --no-cache
docker compose up -d
```

## Troubleshooting

- `Unsupported architecture` during KoboldCpp build:
  - Current Dockerfile supports `amd64` only.
- Model not found in KoboldCpp:
  - Verify the file exists at `./models/MyModel.gguf` or update command path.
- GPU not detected:
  - Verify host drivers and presence of `/dev/dri` and `/dev/kfd`.
- SD.Next build reference fails:
  - Confirm selected `SDNEXT_REF` exists in upstream repo.
