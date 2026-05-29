# P3-T01 — STR-FOOTING-01: Isolated Footing Engine

## Engine ID: `STR-FOOTING-01`
## Code: ACI 318-19
## Drawing Output: Plan View + Section A-A + Reinforcement Schedule

---

## Required Inputs (after AssumptionEngine)

| Field | Unit | Notes |
|---|---|---|
| `load_dead_kn` | kN | Service dead load — BLOCKING |
| `load_live_kn` | kN | Service live load — BLOCKING |
| `soil_bearing_kpa` | kPa | Default 150 |
| `fc_mpa` | MPa | Default 25 |
| `fy_mpa` | MPa | Default 420 |
| `cover_mm` | mm | Default 75 |
| `column_width_mm` | mm | Default 300 |
| `column_depth_mm` | mm | Default 300 |
| `footing_depth_m` | m | Default 1.5 |

---

## Calculation Sequence (ACI 318-19)

### Step 1 — Service Load & Area
```
P_service = load_dead_kn + load_live_kn
A_req = P_service / soil_bearing_kpa        [m²]
B = ceil(sqrt(A_req) × 10) / 10            [m, rounded up to 0.1m]
A_actual = B × B
```

### Step 2 — Factored Load
```
P_u = 1.2 × load_dead_kn + 1.6 × load_live_kn
q_u = P_u / A_actual                        [kN/m²]
```

### Step 3 — Footing Thickness (Punching Shear)
```
d_trial = column_width_mm / 2              [mm, start]
loop until punching OK:
  b_o = 4 × (column_width_mm + d_trial)   [mm]
  V_c = 0.33 × sqrt(fc_mpa) × b_o × d_trial / 1000  [kN]
  V_u_punch = P_u - q_u × ((column_width_mm + d_trial)/1000)²
  if V_u_punch ≤ φ_shear × V_c: break
  d_trial += 50
h = d_trial + cover_mm + bar_diameter/2    [mm, round to 50mm]
```

### Step 4 — One-Way Shear Check
```
critical_dist = d_trial / 1000             [m from face of column]
V_u_1way = q_u × B/2 × (B/2 - column_width_mm/2000 - d_trial/1000)
V_c_1way = 0.17 × sqrt(fc_mpa) × B×1000 × d_trial / 1000
assert φ_shear × V_c_1way ≥ V_u_1way
```

### Step 5 — Flexural Reinforcement
```
cantilever = (B - column_width_mm/1000) / 2    [m]
M_u = q_u × B × cantilever² / 2               [kN·m]
R_n = M_u×10⁶ / (φ_flexure × B×1000 × d_trial²)
ρ = 0.85×fc_mpa/fy_mpa × (1 - sqrt(1 - 2×R_n/(0.85×fc_mpa)))
ρ = max(ρ, ρ_min=0.0018)
A_s = ρ × B×1000 × d_trial                    [mm²]
bar_dia = select_bar(A_s, B)                   [mm]
n_bars = ceil(A_s / bar_area(bar_dia))
spacing = (B×1000 - 2×cover_mm) / (n_bars - 1)
assert spacing ≤ 3×h and spacing ≤ 450mm
```

### Step 6 — Output Summary
```
Footing size:  B × B × h  [mm]
Reinforcement: n_bars ⌀bar_dia @ spacing mm — Each Way
Concrete:      fc = fc_mpa MPa
Steel:         fy = fy_mpa MPa
```

---

## DrawingPrimitive Emission

### Plan View (1:50)

| Primitive | ID pattern | Layer | Description |
|---|---|---|---|
| rect | PRM-RECT-001 | S-FNDP-POCH | Footing outline B×B |
| rect | PRM-RECT-002 | S-COLS-POCH | Column footprint |
| line ×n | PRM-LINE-0xx | S-FNDP-RBAR | Bottom rebar grid X-dir |
| line ×n | PRM-LINE-1xx | S-FNDP-RBAR | Bottom rebar grid Y-dir |
| dim_linear | PRM-DIM-001 | S-ANNO-DIMS | Footing width B |
| dim_linear | PRM-DIM-002 | S-ANNO-DIMS | Footing depth B |
| dim_linear | PRM-DIM-003 | S-ANNO-DIMS | Column position dims |
| text | PRM-TXT-001 | S-ANNO-TEXT | "PLAN — FOOTING F1 — 1:50" |
| text | PRM-TXT-002 | S-ANNO-TEXT | "n⌀d @ s mm — E.W." |

### Section A-A (1:25)

| Primitive | Layer | Description |
|---|---|---|
| polygon | S-FNDP-POCH | Footing profile (filled hatch) |
| hatch | S-FNDP-POCH | Concrete hatch ANSI31 |
| rect | S-COLS-POCH | Column section |
| rebar ×n | S-FNDP-RBAR | Bottom bars (section cut circles) |
| line | S-FNDP-RBAR | Top bars if any |
| dim_linear | S-ANNO-DIMS | Total height h |
| dim_linear | S-ANNO-DIMS | Cover 75mm |
| dim_linear | S-ANNO-DIMS | d (effective depth) |
| text | S-ANNO-TEXT | "SECTION A-A — 1:25" |
| text | S-ANNO-TEXT | Material notes |

### Reinforcement Schedule Table

| Col | Content |
|---|---|
| Mark | F1 |
| Size (mm) | B × B |
| Thickness (mm) | h |
| Bars X-dir | n⌀d @ s |
| Bars Y-dir | n⌀d @ s |
| fc (MPa) | fc_mpa |
| fy (MPa) | fy_mpa |

---

## CalculationSummary Fields

```json
{
  "element": "isolated_footing",
  "tag": "F1",
  "size_mm": "B × B × h",
  "reinforcement": "n⌀d @ s mm — E.W.",
  "fc_mpa": 25,
  "fy_mpa": 420,
  "q_u_kpa": 0.0,
  "punching_ratio": 0.0,
  "shear_ratio": 0.0,
  "flexure_rho": 0.0,
  "governing_check": "punching",
  "code": "ACI_318_19"
}
```
