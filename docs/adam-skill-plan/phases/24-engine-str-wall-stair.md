# P4-T05 — STR-WALL-01 + STR-STAIR-01

---

# Part A: STR-WALL-01 — Shear Wall Engine

## Engine ID: `STR-WALL-01`
## Code: ACI 318-19 Ch.11 (non-seismic) + Ch.18 (seismic)
## Drawing Output: Plan + Section + Rebar Schedule

## Required Inputs

| Field | Unit | Notes |
|---|---|---|
| `length_m` | m | BLOCKING |
| `height_m` | m | BLOCKING |
| `thickness_mm` | mm | Default 200 |
| `fc_mpa` | MPa | Default 25 |
| `fy_mpa` | MPa | Default 420 |
| `shear_force_kn` | kN | BLOCKING |
| `cover_mm` | mm | Default 20 |

## Calculation Sequence

```
Horizontal reinforcement (ACI 11.6.2):
  ρ_h_min = 0.0025
  A_h = ρ_h × thickness_mm × 1000 [mm²/m]
  select bar_dia_h + spacing_h ≤ min(3t, 450mm)

Vertical reinforcement:
  ρ_v_min = max(0.0025, ρ_h)
  A_v = ρ_v × thickness_mm × 1000

Shear capacity:
  V_c = 0.17 × √fc × t × L
  V_s = A_h/s × fy × L
  φVn = φ × (V_c + V_s) ≥ shear_force_kn

Boundary elements (ACI 18.10.6):
  Check if seismic demand requires special boundary elements
  If yes: boundary_width = max(c/2, 1.5t), ρ_boundary ≥ 0.006
```

## DrawingPrimitive Emission

| Primitive | Layer | Description |
|---|---|---|
| rect | S-WALL-POCH | Wall outline |
| hatch | S-WALL-POCH | Concrete hatch |
| line ×n | S-WALL-RBAR | Horizontal bars |
| line ×m | S-WALL-RBAR | Vertical bars |
| rect | S-WALL-RBAR | Boundary element zones |
| dim_linear ×4 | S-ANNO-DIMS | Length, height, thickness, cover |
| text | S-ANNO-TEXT | Reinforcement notation |

---

# Part B: STR-STAIR-01 — RC Stair Engine

## Engine ID: `STR-STAIR-01`
## Code: ACI 318-19 + IBC 2021 §1011
## Drawing Output: Stair Plan 1:50 + Section 1:25 + Waist Slab Rebar

## Required Inputs

| Field | Unit | Notes |
|---|---|---|
| `floor_height_m` | m | BLOCKING |
| `stair_width_m` | m | Default 1.2 |
| `riser_mm` | mm | Default 175 (IBC max 190) |
| `tread_mm` | mm | Default 280 (IBC min 280) |
| `fc_mpa` | MPa | Default 25 |
| `fy_mpa` | MPa | Default 420 |
| `live_load_knm2` | kN/m² | Default 4.8 |

## Geometry & Waist Slab Design

```
n_risers = round(floor_height_m × 1000 / riser_mm)
actual_riser = floor_height_m × 1000 / n_risers
n_treads = n_risers - 1
run_m = n_treads × tread_mm / 1000
α = atan(floor_height_m / run_m)             [degrees]
span_inclined = sqrt(run_m² + floor_height_m²)

Waist slab thickness:
  h_min = span_inclined × 1000 / 20
  h = ceil(h_min/10) × 10                    [mm]

Design same as STR-SLAB-01:
  w_u = 1.2×(24×h/1000/cos(α)) + 1.6×live_load
  M_u = w_u × span_inclined² / 8
  → A_s, bar_dia, spacing
```

## DrawingPrimitive Emission

### Plan (1:50)
| Primitive | Layer | Description |
|---|---|---|
| polyline ×n_treads | S-STRS-TRED | Tread lines |
| polyline | S-STRS-TRED | Stair boundary |
| line | S-STRS-TRED | Landing lines |
| symbol | A-ANNO-SYMB | Direction arrow + step count |
| dim_linear | S-ANNO-DIMS | Overall run + width |
| text | S-ANNO-TEXT | "ST-01 — n×riser mm" |

### Section (1:25)
| Primitive | Layer | Description |
|---|---|---|
| polygon | S-STRS-TRED | Stair profile (filled) |
| hatch | S-STRS-TRED | Concrete hatch |
| rebar ×n | S-STRS-RBAR | Waist slab main bars |
| rebar ×m | S-STRS-RBAR | Distribution bars |
| dim_linear ×4 | S-ANNO-DIMS | Riser, tread, waist h, floor height |
| text | S-ANNO-TEXT | Rebar notation |

## CalculationSummary

```json
{
  "element": "stair",
  "tag": "ST-01",
  "n_risers": 0,
  "riser_mm": 0,
  "tread_mm": 0,
  "waist_h_mm": 0,
  "main_bars": "∅d @ s mm",
  "dist_bars": "∅10 @ s mm",
  "span_inclined_m": 0.0,
  "code": "ACI_318_19"
}
```
