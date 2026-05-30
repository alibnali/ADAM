# P4-T03 — MEP-HVAC-01: HVAC Engine

## Engine ID: `MEP-HVAC-01`
## Code: ASHRAE 62.1 + SMACNA
## Drawing Output: HVAC Plan 1:100 + Duct Schedule

## Required Inputs

| Field | Type | Notes |
|---|---|---|
| `rooms` | array | BLOCKING |
| `climate_zone` | string | Default "hot_dry" |
| `system_type` | string | split / central_ahu, default split |

## Calculation Logic

```
Cooling load per room:
  hot_dry:   area_m2 × 200 W/m²
  hot_humid: area_m2 × 220 W/m²
  temperate: area_m2 × 150 W/m²

Total capacity (Ton) = Q_total_W / 3517

Ventilation (ASHRAE 62.1):
  CFM = occupants × 7.5 + area_m2 × 0.86
  CMH = CFM × 1.699

Duct sizing (SMACNA equal-friction):
  Main duct velocity: 5–8 m/s
  Branch duct velocity: 3–5 m/s
  Round to standard: 100,150,200,250,300,350,400mm
```

## DrawingPrimitive Emission

| Primitive | Layer | Description |
|---|---|---|
| rect | M-HVAC-EQPM | AHU / indoor unit |
| polyline | M-HVAC-SUPL | Supply duct (thick) |
| polyline (dashed) | M-HVAC-RETN | Return duct |
| symbol | M-HVAC-EQPM | Supply diffuser |
| symbol | M-HVAC-EQPM | Return grille |
| text | M-ANNO-TEXT | Duct size + CFM labels |
| text | M-ANNO-TEXT | Equipment tags (AHU-1) |

## CalculationSummary

```json
{
  "element": "hvac",
  "tag": "HVAC-01",
  "total_cooling_ton": 0.0,
  "total_cmh": 0.0,
  "main_duct_mm": 0,
  "room_loads": [],
  "code": "ASHRAE_62.1"
}
```
