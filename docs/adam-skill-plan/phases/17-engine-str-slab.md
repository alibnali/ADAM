# P3-T04 — STR-SLAB-01: One-Way Slab Engine

## Engine ID: `STR-SLAB-01`
## Code: ACI 318-19
## Drawing Output: Plan (1m strip) + Section + Bar Schedule

---

## Required Inputs

| Field | Unit | Notes |
|---|---|---|
| `span_m` | m | BLOCKING |
| `live_load_knm2` | kN/m² | Default 2.0 |
| `fc_mpa` | MPa | Default 25 |
| `fy_mpa` | MPa | Default 420 |
| `cover_mm` | mm | Default 20 |
| `slab_type` | — | one_way / two_way, default one_way |
| `support_type` | — | simply_supported / continuous, default simply_supported |

---

## Calculation Sequence — One-Way (ACI 318-19)

### Step 1 — Thickness
```
h_min = span_m×1000 / 20              [mm, ACI Table 7.3.1.1, simply supported]
h = ceil(h_min/10)*10                  [round up to 10mm]
d = h - cover_mm - bar_dia/2
```

### Step 2 — Loads (per 1m strip)
```
w_sw = 24 × h/1000 × 1.0             [kN/m]
w_d  = w_sw + superimposed_dead (0 if not given)
w_u  = 1.2×w_d + 1.6×live_load_knm2
```

### Step 3 — Flexure
```
M_u = w_u × span_m² / 8              [kN·m/m]
R_n = M_u×10⁶ / (φ_flex × 1000 × d²)
ρ = 0.85×fc/fy × (1 - sqrt(1 - 2×R_n/(0.85×fc)))
ρ = max(ρ, ρ_min=0.0018)             [ACI 7.6.1.1 temperature & shrinkage]
A_s = ρ × 1000 × d                   [mm²/m]

select bar + spacing:
  bar_dia = 10 or 12mm (preferred)
  spacing = bar_area(bar_dia) / A_s × 1000   [mm]
  spacing = floor(spacing/5)*5               [round down to 5mm]
  assert spacing ≤ min(3h, 450)              [ACI 7.7.2.3]
```

### Step 4 — Shrinkage Steel (perpendicular)
```
A_s_temp = 0.0018 × 1000 × h
spacing_temp = bar_area(10mm) / A_s_temp × 1000
spacing_temp = min(floor(spacing_temp/5)*5, min(5h, 450))
```

### Step 5 — Shear Check
```
V_u = w_u × span_m / 2 - w_u×d/1000  [kN/m at d from face]
V_c = 0.17×sqrt(fc)×1000×d/1000       [kN/m]
assert φ_shear×V_c ≥ V_u               [slabs rarely need stirrups]
```

---

## DrawingPrimitive Emission

### Plan — 1m Reference Strip (1:50)

| Primitive | Layer | Description |
|---|---|---|
| rect | S-SLBS-POCH | Slab strip outline span×1000mm |
| line ×n | S-SLBS-RBAR | Bottom main bars |
| line ×m | S-SLBS-RBAR | Temp/shrink bars (perpendicular) |
| dim_linear | S-ANNO-DIMS | Span |
| dim_linear | S-ANNO-DIMS | Bar spacing |
| text | S-ANNO-TEXT | "⌀d @ s mm — MAIN" |
| text | S-ANNO-TEXT | "⌀10 @ s mm — TEMP" |

### Section (1:20)

| Primitive | Layer | Description |
|---|---|---|
| rect | S-SLBS-POCH | Slab thickness |
| hatch | S-SLBS-POCH | Concrete hatch |
| rebar ×n | S-SLBS-RBAR | Bottom bars |
| dim_linear | S-ANNO-DIMS | h total |
| dim_linear | S-ANNO-DIMS | Cover 20mm |
| dim_linear | S-ANNO-DIMS | d effective |
| text | S-ANNO-TEXT | "SECTION — 1:20" |

---

## CalculationSummary Fields

```json
{
  "element": "one_way_slab",
  "tag": "S1",
  "thickness_mm": 0,
  "main_bars": "⌀d @ s mm",
  "temp_bars": "⌀10 @ s mm",
  "Mu_knm_per_m": 0.0,
  "rho_main": 0.0,
  "phi_Mn_knm": 0.0,
  "shear_ok": true,
  "code": "ACI_318_19"
}
```
