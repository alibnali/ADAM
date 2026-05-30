# P4-T04 — MEP-ELEC-01: Electrical Engine

## Engine ID: `MEP-ELEC-01`
## Code: NEC 2023
## Drawing Output: Electrical Plan 1:100 + Single-Line Diagram + Panel Schedule

## Required Inputs

| Field | Type | Notes |
|---|---|---|
| `rooms` | array | BLOCKING |
| `voltage` | number | Default 220V |
| `phases` | number | 1 or 3, default 1 |
| `lighting_load_wm2` | number | Default 10 W/m² |
| `outlet_load_w` | number | Default 180 W |

## Calculation Logic

```
Lighting per room:
  load_W = area_m2 × 10
  fixtures = ceil(load_W / 18)   [18W LED panel]

Outlets per room (NEC 210.52):
  outlets = ceil(perimeter_m / 3.6)
  outlet_load = outlets × 180W

Circuit grouping:
  Max per 16A circuit = 0.8 × 16 × 220 = 2816W
  Max 8 fixtures OR 6 outlets per circuit

Panel totals:
  Total_kVA = sum(all circuits) / 1000
  Main_breaker_A = ceil(kVA×1000 / (V×0.85) / 10) × 10
```

## DrawingPrimitive Emission

### Electrical Plan
| Primitive | Layer | Description |
|---|---|---|
| symbol | E-LITE-FIXT | Ceiling light symbols |
| symbol | E-POWR-OUTL | Outlet symbols |
| symbol | E-POWR-PNLS | Panel location |
| polyline | E-LITE-CIRC | Lighting circuit wiring |
| polyline | E-POWR-CIRC | Outlet circuit wiring |
| text | E-ANNO-TEXT | Circuit numbers + panel tag |

### Single-Line Diagram
| Primitive | Layer | Description |
|---|---|---|
| rect | E-POWR-PNLS | Panel board |
| line per circuit | E-POWR-CIRC | Branch circuits |
| symbol | E-POWR-PNLS | Breaker symbols |
| text | E-ANNO-TEXT | Load (W), breaker (A), total kVA |

## CalculationSummary

```json
{
  "element": "electrical",
  "tag": "PANEL-01",
  "total_load_kva": 0.0,
  "main_breaker_a": 0,
  "circuit_count": 0,
  "voltage": 220,
  "phases": 1,
  "code": "NEC_2023"
}
```
