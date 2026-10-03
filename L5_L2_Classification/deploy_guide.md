# Deploy Guide — KAZCADE_RUNTIME
## Prerequisites
- Python 3.11+, asyncio (stdlib), psutil 5.9+, PAX 27B weights, AIOSS_FORMAT

## Environment
- 16GB RAM minimum. GPU strongly recommended for PAX inference.
- Runs as systemd service or Windows Service.

## Install
```bash
pip install anticloud-kazcade psutil
```

## Start runtime
```bash
python -m kazcade_runtime --config ./kazcade_config.yaml --aioss ./runtime.aioss
```

## Air-Gap
All deps pre-installed, PAX weights local, AIOSS chain local. No network required.

## AIOSS Integration
Runtime auto-appends lifecycle events. Configure: `aioss_chain: ./runtime.aioss` in config.

## PAX Harness Wiring
Runtime manages PAX as a named module: `name: PAX_INFERENCE, path: ./pax_harness.py, gpu: true`

## Verification
```bash
python -m kazcade_runtime health --all
aioss verify --chain ./runtime.aioss
```
