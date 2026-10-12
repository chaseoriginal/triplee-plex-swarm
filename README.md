# triplee-plex-swarm

Plex Media Server stack for the "TripleE" (Echo Entertainment Emporium) instance, hosted on ore-dock-110-4 Docker Swarm.

## Architecture

- **Host**: ore-dock-110-4 (swarm leader) — has Quadro P2000 GPU for hardware transcoding
- **Stack**: `triplee-plex-swarm`
- **Service**: `plex`
- **Image**: lscr.io/linuxserver/plex:latest
- **Advertise URL**: http://lplex.echoinggreen.net:32400/
- **Config volume**: `/var/lib/docker/volumes/triplee-plex-swarm/config` (LVM bind mount)
- **Transcode volume**: `/var/lib/docker/volumes/triplee-plex-swarm/transcode` (LVM bind mount)
- **Media**: CIFS share `//shares.echoinggreen.info/Multimedia` mounted at `/media/Multimedia`
- **Music**: CIFS share at `/media/Multimedia/Music`
- **Network**: `triplee-plex-swarm_default` (overlay, external)

## Hardware

GPU: NVIDIA Quadro P2000 — accessed via NVIDIA Container Runtime. Hardware transcoding enabled.

## Volumes

| Volume | Type | Source | Target |
|--------|------|--------|--------|
| config | bind | /var/lib/docker/volumes/triplee-plex-swarm/config | /config |
| transcode | bind | /var/lib/docker/volumes/triplee-plex-swarm/transcode | /transcode |
| media | bind | /media/Multimedia | /media |
| music | bind | /media/Multimedia/Music | /music |
