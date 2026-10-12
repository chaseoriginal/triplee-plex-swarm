# RUNBOOK — triplee-plex-swarm

## Quick commands

```bash
# Check status
ssh ore-dock-110-4
docker service ls | grep triplee
docker ps --filter "name=triplee" --format "{{.Status}}"

# View logs
docker service logs triplee-plex-swarm_plex --tail 50

# Redeploy (from /data/compose/triplee-plex-swarm)
set -a && source .env && docker stack deploy -c docker-compose.yml --with-registry-auth triplee-plex-swarm && set +a

# Restart service
docker service update --force triplee-plex-swarm_plex
```

## GPU status

```bash
docker exec triplee-plex-swarm_plex.1.<id> nvidia-smi
```

## Adding a Plex claim token

If the server shows as unclaimed:

1. Get a claim token: https://www.plex.tv/claim/
2. Edit `.env` and set `PLEX_CLAIM=claim-YOURTOKEN`
3. Redeploy: `set -a && source .env && docker stack deploy -c docker-compose.yml --with-registry-auth triplee-plex-swarm && set +a`

## Restoring from backup

Preferences.xml and all Plex data lives in the named volume at `/var/lib/docker/volumes/triplee-plex-swarm/config`.

To restore:
1. Stop the service: `docker service scale triplee-plex-swarm_plex=0`
2. Copy backup: `sudo cp -r /path/to/backup/* /var/lib/docker/volumes/triplee-plex-swarm/config/`
3. Restart: `docker service scale triplee-plex-swarm_plex=1`

## Troubleshooting

### "No user exists for uid 1000"
Non-critical warning. The linuxserver.io image creates user `abc` with uid matching PUID. This warning appears when PUID doesn't map to a known user in /etc/passwd — the container still runs correctly.

### PMS failure: Read/write access required for /config
Config volume ownership mismatch. Fix:
```bash
docker run --rm -v triplee-plex-swarm/config:/data alpine chown -R 1000:1000 /data
```

### Media not visible in library
Check CIFS mount inside container:
```bash
CID=$(docker ps -q --filter "name=triplee")
docker exec $CID ls /media/
```
If empty, the CIFS mount on the host may have dropped. Check: `grep multimedia /proc/mounts`

### GPU not used for transcoding
Verify GPU access:
```bash
CID=$(docker ps -q --filter "name=triplee")
docker exec $CID nvidia-smi
```
If error, check NVIDIA_VISIBLE_DEVICES and NVIDIA_DRIVER_CAPABILITIES env vars on the service.
