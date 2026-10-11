# GitHub Secrets — oryx repo

Settings → Secrets and variables → Actions. Secrets marked **auto** are
generated on the host at deploy time when absent (same pattern as the freddy
deploys); you only need to set the ones marked **manual**.

## FKS stack (used by `fks-deploy.yml`)

| Secret | Auto? | Purpose |
|---|---|---|
| `POSTGRES_PASSWORD` | auto | postgres superuser |
| `REDIS_PASSWORD` | auto | redis requirepass |
| `QUESTDB_PG_PASSWORD` | auto | questdb pg-wire user |
| `GRAFANA_PASSWORD` | auto | grafana admin (`GRAFANA_USER=admin`) |
| `API_KEY` | auto | bearer token for internal HTTP APIs |
| `JANUS_API_TOKEN` | auto | janus mutating-route bearer token (fail-closed without it) |
| `NGINX_INTERNAL_TOKEN` | auto | spawner `X-Internal-Token` auth; botsync uses the same value |
| `EVENTS_TOKEN` | auto | scoped bot→spawner events mailbox token |
| `SPAWNER_SECRETS_KEY` | auto | 64-hex key encrypting exchange secrets at rest |
| `DISCORD_WEBHOOK_GENERAL` | manual | alertmanager-discord bridge (general alerts) |
| `DISCORD_WEBHOOK_SIGNALS` | manual | alertmanager-discord bridge (signal alerts) |
| `DISCORD_WEBHOOK_ANALYSIS` | manual | alertmanager-discord bridge (analysis alerts) |
| `DEADMAN_PING_URL` | manual | optional healthchecks.io URL; leave unset and deadman idles |

The three Discord webhook secrets can reuse the existing Oryx Actions /
`#crypto` channel webhooks or point at new ones — your call.

## Already present (reused)

| Secret | Used by |
|---|---|
| `KRAKEN_API_KEY` / `KRAKEN_API_SECRET` | loaded into the spawner's encrypted secret store (`POST /secrets`, exchange `kraken`) at deploy; bot YAMLs declare `secrets: [kraken]` — keys never land in a bot `.env` |
| `DISCORD_WEBHOOK_URL` | Oryx Actions deploy notifications (unchanged) |
| `CRYPTO_DISCORD_WEBHOOK_URL` | crypto bot reports (unchanged until the spawner cutover) |
| `TAILSCALE_OAUTH_CLIENT_ID` / `TAILSCALE_OAUTH_SECRET` | Tailscale connect |
| `ORYX_TAILSCALE_IP`, `SSH_PORT`, `SSH_USER`, `SSH_KEY` | SSH deploy |

## Variables (optional)

`JANUS_REF`, `SPAWNER_REF`, `WEB_REF` (repo variables, default `main`) pin
which git refs the janus / spawner / webui images build from.
