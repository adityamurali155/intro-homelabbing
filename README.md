# Intro to Home Labbing Part 1

Workshop repo for learning how to SSH into a VPS, install Docker, understand Docker Compose, and deploy services.

## Contents

- `slideshow/` - Slidev deck used during the workshop
- `examples/` - Docker Compose stacks for practice

## Run the slide deck

```bash
cd slideshow
bun install
bunx slidev --open
```

To build static files:

```bash
cd slideshow
bun run build
```

## Compose examples

All examples are in `examples/<stack-name>/compose.yaml`.

| Stack | Purpose | URL/Port |
| --- | --- | --- |
| `homarr` | Dashboard app from workshop | `http://<vps-ip>:7575` |
| `whoami` | Simple container/port check app | `http://<vps-ip>:8081` |
| `it-tools` | Handy browser-based networking/dev tools | `http://<vps-ip>:8082` |
| `uptime-kuma` | Basic service uptime monitoring | `http://<vps-ip>:3001` |
| `linkding` | Self-hosted bookmark manager | `http://<vps-ip>:9090` |

## Run any example stack

```bash
cd examples/<stack-name>
cp .env.example .env  # only if this file exists
sudo docker compose up -d
sudo docker compose ps
```

Stop a stack:

```bash
sudo docker compose down
```

## Post-workshop practice ideas

1. Put one stack behind a reverse proxy (Caddy or Traefik).
2. Add backups for each stack's data volume.
3. Pin image tags to specific versions (avoid `latest`).
4. Add health checks and restart policies where useful.
