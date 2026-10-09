# oryx — service deployment repo

Oryx is the trading-bot host. This repo holds the **service-level** deployment
files (docker compose + CI/CD), following the same split as the
`freddy` / `sullivan` / `princess` repos: application code lives in its own
repo, this repo only describes how it runs on the box.

## Services

| Service | Source repo | What it does |
|---|---|---|
| `crypto-bot` | [nuniesmith/crypto](https://github.com/nuniesmith/crypto) | Kraken live-trading bot — daily 200-day MA ±5% regime rule (BTC 44 / ETH 22 / SOL 12 / LINK·XRP·INJ 5% each / 7% cash) |

The compose file builds the bot's `Dockerfile` straight from the crypto repo
(`https://github.com/nuniesmith/crypto.git#main`), so a crypto release is
picked up on the next oryx deploy with no changes here.

## Deploy

Pushes to `main` (and manual workflow runs) deploy via Tailscale + SSH using
the composite actions in [nuniesmith/actions](https://github.com/nuniesmith/actions):

1. Git pull the oryx repo to `~/oryx` on the box
2. Assemble `.env` from GitHub Actions secrets (see [docs/GITHUB_SECRETS.md](docs/GITHUB_SECRETS.md))
3. `docker compose build` the bot image, then `up -d --force-recreate`
4. Health-check the container

## Migrating from the systemd deploy

The bot previously ran as a systemd user unit (`crypto-bot-live.service`)
from `~/github/crypto`. Before the first Docker deploy:

```bash
# Carry the live state over so order tracking and history survive
mkdir -p ~/oryx/data/crypto-bot/paper
cp ~/github/crypto/data/paper/state.json ~/oryx/data/crypto-bot/paper/state.json

# Stop the old unit so two bots never trade one account
systemctl --user stop crypto-bot-live.service
systemctl --user disable crypto-bot-live.service
```

## Secrets

See [docs/GITHUB_SECRETS.md](docs/GITHUB_SECRETS.md) for the full list of
GitHub Actions secrets this deploy needs.
