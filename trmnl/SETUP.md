# TRMNL Private Plugin — Ostrom Germany prices

Push Ostrom day-ahead **total** EUR/kWh (gross spot + taxes/levies) to your TRMNL e-ink display every 15 minutes.

## One-time setup

### 1. Create a Private Plugin

1. In [TRMNL](https://trmnl.com) go to **Plugins** → **Private Plugin**.
2. Set **Strategy** to **Webhook**.
3. Save the plugin so a **Webhook URL** / UUID appears.
4. Copy the UUID from `https://trmnl.com/api/custom_plugins/<UUID>`.

### 2. Paste markup

Open the Markup editor and paste (or connect this repo’s `trmnl/` folder via GitHub sync):

| Size | File |
|------|------|
| Full | [`src/full.liquid`](src/full.liquid) |
| Half vertical | [`src/half_vertical.liquid`](src/half_vertical.liquid) |
| Quadrant | [`src/quadrant.liquid`](src/quadrant.liquid) |

Title bars use **Ostrom**.

### 3. Configure credentials / CI

Local dry-run (prints JSON, no push):

```bash
bash run.sh trmnl
```

Push once:

```bash
export OSTROM_CLIENT_ID="..."
export OSTROM_CLIENT_SECRET="..."
export OSTROM_ZIP="10115"   # optional
export TRMNL_PLUGIN_UUID="your-uuid-here"
bash run.sh trmnl --push
```

GitHub Actions secrets on this repo:

| Secret | Required |
|--------|----------|
| `OSTROM_CLIENT_ID` | yes |
| `OSTROM_CLIENT_SECRET` | yes |
| `OSTROM_ZIP` | recommended |
| `TRMNL_PLUGIN_UUID` | yes |

The workflow [`.github/workflows/trmnl-ostrom.yml`](../.github/workflows/trmnl-ostrom.yml) runs every 15 minutes and on manual dispatch.

### 4. Playlist

Add the plugin to your device playlist. A refresh interval of **15–30 minutes** is enough; day-ahead prices only change when Ostrom publishes, but the “current” hour and cheap window labels update each push.

## Payload fields

| Field | Meaning |
|-------|---------|
| `current.price_label` | Total EUR/kWh for the active hour |
| `current.slot_label` | e.g. `13:00–14:00` (Europe/Berlin) |
| `current.gross_ct_per_kwh` / `taxes_ct_per_kwh` | Breakdown in ct/kWh |
| `today_min_label` / `today_max_label` / `today_avg_label` | Today’s total EUR/kWh stats |
| `cheapest_next.label` | Next cheapest 1h block (default) |
| `upcoming.t` / `upcoming.p` / `upcoming.bars` | Sparkline labels, cents/kWh, 0–10 bar heights |
| `tomorrow_ready` | `true` once tomorrow’s hours appear in the API response |
| `updated_label` | Last push time (Berlin) |

Retail Ostrom totals — includes VAT on energy and taxes/levies when `OSTROM_ZIP` is set. Does not amortize monthly base/grid fees into the per-kWh figure.
