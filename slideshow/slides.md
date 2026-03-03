---
theme: dracula
title: Intro to Homelabbing with Docker
info: |
  Workshop deck covering SSH access, Docker install, Docker Compose basics,
  and deploying Homarr on a VPS.
---

# Intro to Homelabbing with Docker
## From SSH to a running Homarr stack

---

## Session Goals

- Log into a Linux VPS over SSH
- Install Docker Engine + Compose plugin
- Understand Docker Compose file structure
- Deploy Homarr with one `compose.yaml`

---

## Prerequisites

- A VPS (Ubuntu 22.04/24.04 recommended)
- SSH access (IP, username, and password from handout)
- Local terminal on Linux/macOS/Windows
- A Tailscale, VPN, or trusted network plan (recommended)

---

## 1) Get Connected to the VPS

Find these details from your provider:

- Server IP or DNS name
- SSH user (often `root` or `ubuntu`)
- Authentication method (password for this workshop)

Test network reachability:

```bash
ping -c 3 <vps-ip>
```

---

## Workshop Login Method (Password)

For this workshop, everyone logs in with the provided password:

```bash
ssh <user>@<vps-ip>
```

Accept host key prompt on first login, then enter password.

---

## Log In to the VPS

```bash
ssh <user>@<vps-ip>
```

First-run checks:

```bash
whoami
hostnamectl
lsb_release -a
```

---

## 2) Install Docker (Convenience Script)

```bash
sudo apt update && sudo apt upgrade -y
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
rm get-docker.sh
```

---

## Post-install Setup

```bash
sudo systemctl enable --now docker
```

For this workshop, run Docker commands with `sudo`.

---

## Verify Docker + Compose

```bash
sudo docker version
sudo docker compose version
sudo docker run --rm hello-world
```

---

## 3) Docker Compose Syntax at a Glance

A Compose file usually has:

- `services`: the containers to run
- `volumes`: persistent data locations
- `networks`: container networking rules
- optional `environment`, `ports`, `depends_on`, `restart`

---

## Compose Anatomy Example

```yaml
# Top-level section listing all containers in this stack
services:
  # Service name (used by Compose for DNS and management)
  web:
    # Container image to run: <repo>:<tag>
    image: nginx:alpine
    # Port mapping: <host-port>:<container-port>
    ports:
      - "8080:80"
    # Restart policy when Docker/host restarts
    restart: unless-stopped
    # Bind mount: <host-path>:<container-path>:<mode>
    volumes:
      - ./html:/usr/share/nginx/html:ro
```

---

## Common Compose Commands

```bash
sudo docker compose up -d        # start in background
sudo docker compose ps           # show running services
sudo docker compose logs -f      # tail logs
sudo docker compose pull         # fetch latest images
sudo docker compose down         # stop + remove containers
```

---

## 4) Deploy Homarr with Compose

On the VPS:

```bash
mkdir -p ~/stacks/homarr
cd ~/stacks/homarr
openssl rand -hex 32
```

Use the generated value as `SECRET_ENCRYPTION_KEY`.

---

## Homarr `compose.yaml`

```yaml
services:
  homarr:
    image: ghcr.io/homarr-labs/homarr:latest
    container_name: homarr
    restart: unless-stopped
    ports:
      - "7575:7575"
    environment:
      - SECRET_ENCRYPTION_KEY=${SECRET_ENCRYPTION_KEY}
    volumes:
      - ./appdata:/appdata
      - /var/run/docker.sock:/var/run/docker.sock:ro
```

---

## `.env` file for secrets

Create `.env` in the same folder as `compose.yaml`:

```bash
SECRET_ENCRYPTION_KEY=<paste-64-char-hex-from-openssl>
```

Then deploy:

```bash
sudo docker compose up -d
```

---

## Validate the Deployment

```bash
sudo docker compose ps
sudo docker compose logs -f homarr
```

Open in browser:

```text
http://<vps-ip>:7575
```

---

## Updating Homarr Later

```bash
cd ~/stacks/homarr
sudo docker compose pull
sudo docker compose up -d
sudo docker image prune -f
```

---

## Recap

- You logged into a VPS with SSH
- Installed Docker + Compose on Ubuntu
- Learned core Compose syntax and commands
- Deployed a real app (Homarr) with persistent data

Next lab idea: add a reverse proxy + HTTPS with Caddy or Traefik.
