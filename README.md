# oryx — service deployment repo

Oryx is the trading-bot host. Everything that runs on it now deploys with the
FKS stack, through this repo's **FKS Deploy** workflow (`fks-deploy.yml`,
manual dispatch). See [docs/GITHUB_SECRETS.md](docs/GITHUB_SECRETS.md) for the
secrets it needs.

## Retired services

| Service | Retired | Replaced by |
|---|---|---|
| `crypto-bot` | 2026-10-25 | The spawner-managed `crypto-regime` bot in the FKS stack. Removed from this repo so no deploy here can ever run a second trader. Its state stays on disk in `~/oryx/data/crypto-bot`. |
| `btc-recorder` | 2026-11-05 | The FKS `btc` recorder (lake `/srv/fks-lake/raw/binance/usdm/btc`), which recorded beside it from 2026-10-26. The 10-27..11-04 comparison matched every liquidation (9,373 of 9,373). Its pre-lake history (10-14..10-25) was copied into the lake with sha256 verification. The original recording stays on disk in `~/oryx/data/btc-recorder`. |

With both retired, this repo has no compose file and no push-triggered deploy
of its own. `stop-standalone-crypto.yml` and `stop-btc-recorder.yml` are the
one-off workflows that stopped them (idempotent, manual dispatch).
