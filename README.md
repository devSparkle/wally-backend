# wally-backend

Wally is a package manager for Roblox inspired by Cargo (Rust) and npm (JavaScript). It brings the familiar, community-oriented world of sharing code from other communities into the Roblox ecosystem.

Wally has two pieces that work together: a command line tool named wally and a registry server that hosts packages.

This repository contains an easy-to-setup registry server container that can provide a stable environment to run your own private or public repository!

## Setup

The system was designed to work with the following docker-compose file:

```yaml
x-watchtower-scope: &watchtower-scope
  - "com.centurylinklabs.watchtower.scope=${COMPOSE_PROJECT_NAME}"

services:
  api:
    image: ghcr.io/devsparkle/wally-backend
    container_name: Wally-Backend
    user: "root"
    volumes:
      - ./Rocket.toml:/app/Rocket.toml:ro
      - packages:/app/packages
    ports:
      - 3049:8000
    restart: unless-stopped
    labels: *watchtower-scope
  
  watchtower-service:
    image: containrrr/watchtower
    container_name: Wally-Watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --scope ${COMPOSE_PROJECT_NAME}
    restart: unless-stopped
    labels: *watchtower-scope

volumes:
  packages:
```

While it may be a common recommendation with the Wally community that you must use an external storage provider, such as an S3 Bucket; its my opinion and experience that as long as you follow standard backup procedures for the docker `packages` volume, there's no need to rely on external services.

I recommend placing port `3049` behind an HTTPS reverse-proxy with a valid certificate. This will ensure the security of your secret, which must be transmitted to download or upload a package.
