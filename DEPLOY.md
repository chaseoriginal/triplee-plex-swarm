# Deploying triplee-plex-swarm

## Prerequisites

- Swarm leader: `ore-dock-110-4` (10.77.110.14)
- Must be on 110-4 because it has the Quadro P2000 GPU
- Network `triplee-plex-swarm_default` must exist

## Deploy from CLI

```bash
ssh ore-dock-110-4
cd /data/compose/triplee-plex-swarm
git pull origin main
docker stack deploy -c docker-compose.yml --with-registry-auth triplee-plex-swarm
```

Or use the env file:

```bash
set -a && source .env && docker stack deploy -c docker-compose.yml --with-registry-auth triplee-plex-swarm && set +a
```

## Environment variables

| Variable | Value | Notes |
|----------|-------|-------|
| TZ | America/Chicago | |
| IP | lplex.echoinggreen.net | Public-facing hostname |
| CNAME | triplee-plex-swarm | Container/service name base |
| CONFIGLOC | /var/lib/docker/volumes/ | |
| PUID | 1000 | Matches CIFS mount uid |
| PGID | 1000 | Matches CIFS mount gid |
| PLEX_CLAIM | | Claim token (see RUNBOOK) |

## Volume mapping

The compose uses `${CONFIGLOC}${CNAME}/config` and `${CONFIGLOC}${CNAME}/transcode` which resolve to:
- `/var/lib/docker/volumes/triplee-plex-swarm/config`
- `/var/lib/docker/volumes/triplee-plex-swarm/transcode`
