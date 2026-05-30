# P4-T02 — MEP-PLUMB-01: Plumbing & Drainage Engine

## Engine ID: `MEP-PLUMB-01`
## Code: UPC 2021
## Drawing Output: Plumbing Plan 1:100 + Riser Diagram

## Required Inputs

| Field | Type | Notes |
|---|---|---|
| `fixtures` | array | BLOCKING |
| `floors` | number | Default 1 |
| `water_pressure_kpa` | number | Default 300 |

### Fixture Types & DFU

| Fixture | DFU | Supply (mm) | Drain (mm) |
|---|---|---|---|
| WC | 4 | 15 | 100 |
| Lavatory | 1 | 10 | 40 |
| Kitchen sink | 2 | 15 | 50 |
| Bathtub/shower | 2 | 15 | 50 |
| Floor drain | 2 | — | 75 |
| Washing machine | 3 | 15 | 75 |

## Pipe Sizing Logic

```
Supply: velocity target 1.2–2.4 m/s → select from 15,20,25,32,40,50mm
Drainage: DFU per branch → dia per UPC Table 703.2
Min slope: 2% for ≤50mm, 1% for ≥75mm
Vent stack: min 50mm
```

## DrawingPrimitive Emission

### Plan View
| Primitive | Layer | Description |
|---|---|---|
| symbol per fixture | P-SANR-FIXT | WC, basin, sink symbols |
| polyline | P-SANR-PIPE | Drain lines with slope arrow |
| polyline | P-DOMW-PIPE | Cold water supply |
| polyline (dashed) | P-DOMW-PIPE | Hot water supply |
| text | P-ANNO-TEXT | Pipe dia + slope labels |

### Riser Diagram
| Primitive | Layer | Description |
|---|---|---|
| line per stack | P-SANR-PIPE | Vertical drain stacks |
| line per floor | P-DOMW-PIPE | Horizontal branches |
| symbol | P-SANR-FIXT | Fixture connections |
| text | P-ANNO-TEXT | Sizes + floor labels |

## CalculationSummary

```json
{
  "element": "plumbing",
  "tag": "PLB-01",
  "total_dfu": 0,
  "drain_main_dia_mm": 0,
  "supply_main_dia_mm": 0,
  "vent_dia_mm": 0,
  "fixture_count": 0,
  "code": "UPC_2021"
}
```
