# GitHub Secrets for the Oryx deploy

These secrets live in the **nuniesmith/oryx** repo → Settings → Secrets and variables → Actions.
The CI/CD workflow (`ci-cd.yml`) reads them on every deploy. Nothing secret is
committed to the repo — `.env` on oryx is assembled from these at deploy time.

## Infrastructure (workflow needs these to reach oryx)

| Secret | Value | Notes |
|---|---|---|
| `ORYX_TAILSCALE_IP` | `100.113.72.63` | Oryx's Tailscale IP |
| `TAILSCALE_OAUTH_CLIENT_ID` | _(from Tailscale admin)_ | Same client used by the freddy/sullivan/princess deploys — reuse it |
| `TAILSCALE_OAUTH_SECRET` | _(from Tailscale admin)_ | Same secret used by the other host deploys — reuse it |
| `SSH_PORT` | `22` | |
| `SSH_USER` | `jordan` | Deploy user (in the `docker` group on oryx) |
| `SSH_KEY` | _(generated, see below)_ | Private Ed25519 key; its public half is in `/home/jordan/.ssh/authorized_keys` on oryx |

### SSH_KEY — how it was generated

```bash
ssh-keygen -t ed25519 -f ~/.ssh/ci-deploy/github_actions_oryx -N '' -C 'github-actions-oryx-deploy'
cat ~/.ssh/ci-deploy/github_actions_oryx.pub >> ~/.ssh/authorized_keys
```

Paste the **private** key (`github_actions_oryx`, `-----BEGIN OPENSSH PRIVATE KEY-----` …)
into the `SSH_KEY` secret. Keep the public key on oryx; delete any local copy
of the private key once it's in GitHub.

## Application (written into oryx's `.env` for the bot)

| Secret | Value | Notes |
|---|---|---|
| `KRAKEN_API_KEY` | _(your Kraken key)_ | Same key the systemd bot uses today (`~/github/crypto/.env` on oryx) |
| `KRAKEN_API_SECRET` | _(your Kraken secret)_ | Same secret — never commit it |
| `DISCORD_WEBHOOK_URL` | _(your webhook URL)_ | Optional; daily/weekly/monthly reports. Leave unset to skip |

## Checklist

- [ ] All 6 infrastructure secrets set
- [ ] `KRAKEN_API_KEY` + `KRAKEN_API_SECRET` set (deploy fails fast without them)
- [ ] `DISCORD_WEBHOOK_URL` set (optional)
- [ ] Private deploy key removed from anywhere outside GitHub Secrets
