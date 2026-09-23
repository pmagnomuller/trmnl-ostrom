---
name: trmnl-ostrom
description: "Use when the user asks about Ostrom Germany day-ahead electricity spot prices, the cheapest time to run an appliance or charge an EV, triggering smart-home actions from price thresholds, or pushing Ostrom total EUR/kWh to a TRMNL e-ink Private Plugin. Requires OSTROM_CLIENT_ID and OSTROM_CLIENT_SECRET. Not for OMIE, Tibber, or other providers."
homepage: https://docs.ostrom-api.io/reference/api-access
---

# Ostrom Energy + TRMNL

## When to use

Use when the user asks about:
- Current or upcoming Ostrom electricity spot prices (Germany)
- Cheapest time to run a load (dishwasher, laundry, EV charging)
- Device actions based on price thresholds
- Pushing prices to a **TRMNL** e-ink Private Plugin

## Setup

```bash
cp .env.example .env
# OSTROM_CLIENT_ID, OSTROM_CLIENT_SECRET, optional OSTROM_ZIP, TRMNL_PLUGIN_UUID
```

Or `~/.config/ostrom-energy/config.json` from `config.json.example`.

Precedence: env → home config → interactive prompt only with `--prompt-missing-secrets`.

## Run

```bash
bash run.sh prices
bash run.sh trmnl
bash run.sh trmnl --push
```

## Commands

### prices

```bash
bash run.sh prices --hours 36
```

### optimize

```bash
bash run.sh optimize --duration-hours 2
bash run.sh optimize --kwh 28 --power-kw 11
```

### control

```bash
bash run.sh control --price-below 0.25 --on-command "echo on" --off-command "echo off"
```

### trmnl

```bash
bash run.sh trmnl
bash run.sh trmnl --push
```

Full setup: [`trmnl/SETUP.md`](trmnl/SETUP.md).

## Notes

- OAuth2 client credentials → `/spot-prices` at **HOUR** resolution.
- Display / optimize / control / TRMNL use **total** = `grossKwhPrice + grossKwhTaxAndLevies` as EUR/kWh.
- Timestamps: **Europe/Berlin**.
- TRMNL payload mirrors `trmnl-omie` (current, today stats, cheapest_next, upcoming bars); soft size guard ≤ 2 KB.

## Safety

- Never commit `.env` with real credentials.
- Keep `--execute` off until dry-run looks right.
