# triplee-plex-swarm

Portainer-managed stack. **Git is the source of truth**; Portainer owns the
deployment and polls this repository for changes.

| | |
|---|---|
| Portainer stack id | 8 |
| Portainer host | ore-dock-110-4 |
| Endpoint | - (id -) |
| Swarm | True |
| Live service | svc:triplee-plex-swarm_plex |
| Git repo | https://github.com/chaseoriginal/triplee-plex-swarm |
| Compose file | `docker-compose.yml` |
| Auto-update poll | every 900s |

## Files

- `docker-compose.yml` - the deployed compose. It is **derived from the live
  service spec**, not from Portainer's stored copy, so that the redeploy
  triggered by auto-update is a no-op: the same named volumes (by exact
  name), the same binds, and the same environment values.

  Named volumes are declared with an explicit `name:` on purpose. Docker
  Swarm prefixes a plain named volume with the stack name
  (`beets-swarm` + `config` becomes `beets-swarm_config`), so declaring
  `beets-swarm_beets.config` would mount a brand new EMPTY volume on
  redeploy and the service would come up healthy with no data.

- `.env.example` - the variable names Portainer substitutes, values
  redacted. The real values live in the Portainer stack environment and are
  deliberately NOT committed.

## How a change is deployed

1. Edit `docker-compose.yml` and push to `main`.
2. Portainer polls the repo and redeploys automatically.

## Verifying a deployment

```bash
docker service ls --filter name=svc:triplee-plex-swarm_plex
docker service inspect svc:triplee-plex-swarm_plex --format '{{json .Spec.TaskTemplate.ContainerSpec.Mounts}}'
```

A healthy stack shows `1/1` and the same volume names as before.
