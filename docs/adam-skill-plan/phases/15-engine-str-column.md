# P3-T02 — STR-COLUMN-01: RC Column Engine

## Engine ID: `STR-COLUMN-01`
## Code: ACI 318-19
## Drawing Output: Plan (Cross-Section) + Elevation + Tie Schedule

---

## Required Inputs

| Field | Unit | Notes |
|---|---|---|
| `axial_load_kn` | kN | BLOCKING |
| `moment_knm` | kN·m | Default 0 |
| `height_m` | m | Default 3.0 |
| `fc_mpa` | MPa | Default 25 |
| `fy_mpa` | MPa | Default 420 |
| `cover_mm` | mm | Default 40 |
| `column_shape` | — | rectangular / circular, default rectangular |
| `b_mm` | mm | Width, default from load estimate |
| `h_mm` | mm | Depth, default = b_mm |
| `rho_target` | — | Default 0.02 |

---

## Calculation Sequence (ACI 318-19 — Tied Column)

### Step 1 — Size Estimate
```
A_g_req = P_u / (φ × (0.80 × (0.85×fc + ρ×(fy - 0.85×fc))))
P_u = 1.2×axial_load_kn × 1.0   (assume D only if moment=0)
if b_mm not given: b_mm = h_mm = ceil(sqrt(A_g_req)/50)*50
```

### Step 2 — Steel Area
```
A_g = b_mm × h_mm
A_st = rho_target × A_g
A_st = clamp(A_st, 0.01×A_g, 0.08×A_g)   [ACI 10.6.1.1]
bar_dia = select_bar_column(A_st)
n_bars = ceil(A_st / bar_area(bar_dia))
n_bars = max(n_bars, 4)   [ACI 10.7.3.1 min 4 for rectangular]
```

### Step 3 — Tie Design
```
tie_dia = 10mm  if bar_dia ≤ 32mm
          12mm  if bar_dia > 32mm
tie_spacing = min(
  16 × bar_dia,
  48 × tie_dia,
  min(b_mm, h_mm)
)   [ACI 25.7.2.1]
```

### Step 4 — Capacity Check
```
φP_n = φ × 0.80 × (0.85×fc×(A_g - A_st) + fy×A_st)
assert φP_n ≥ P_u
```

---

## DrawingPrimitive Emission

### Cross-Section Plan (1:20)

| Primitive | Layer | Description |
|---|---|---|
| rect | S-COLS-POCH | Column outline b×h |
| hatch | S-COLS-POCH | Concrete hatch |
| rebar ×n | S-COLS-RBAR | Main bars (filled circles) |
| circle | S-COLS-RBAR | Tie perimeter (dashed) |
| dim_linear ×2 | S-ANNO-DIMS | b and h dims |
| dim_linear ×2 | S-ANNO-DIMS | Cover dims |
| text | S-ANNO-TEXT | "C1 — b×h" |
| text | S-ANNO-TEXT | "n⌀d main bars" |
| text | S-ANNO-TEXT | "⌀tie @ s mm ties" |

### Elevation (1:50)

| Primitive | Layer | Description |
|---|---|---|
| rect | S-COLS-POCH | Column elevation outline |
| line ×n | S-COLS-RBAR | Longitudinal bars |
| line ×m | S-COLS-RBAR | Ties at spacing s |
| dim_linear | S-ANNO-DIMS | Column height |
| dim_linear | S-ANNO-DIMS | Tie spacing zone |
| text | S-ANNO-TEXT | "ELEVATION C1 — 1:50" |

---

## CalculationSummary Fields

```json
{
  "element": "column",
  "tag": "C1",
  "size_mm": "b × h",
  "main_bars": "n⌀d",
  "ties": "⌀tie_dia @ s mm",
  "rho_provided": 0.0,
  "phi_Pn_kn": 0.0,
  "Pu_kn": 0.0,
  "utilization": 0.0,
  "code": "ACI_318_19"
}
```
