# P3-T03 — STR-BEAM-01: RC Beam Engine

## Engine ID: `STR-BEAM-01`
## Code: ACI 318-19
## Drawing Output: Longitudinal Elevation + Cross-Section Mid + Cross-Section Support + Bar Schedule

---

## Required Inputs

| Field | Unit | Notes |
|---|---|---|
| `span_m` | m | BLOCKING |
| `dead_load_knm` | kN/m | BLOCKING (excluding self-weight) |
| `live_load_knm` | kN/m | BLOCKING |
| `fc_mpa` | MPa | Default 25 |
| `fy_mpa` | MPa | Default 420 |
| `cover_mm` | mm | Default 40 |
| `support_type` | — | simply_supported / continuous, default simply_supported |
| `b_mm` | mm | Default span/12×1000 |
| `h_mm` | mm | Default span/10×1000, rounded to 50mm |

---

## Calculation Sequence (ACI 318-19)

### Step 1 — Loads
```
w_sw = 24 × b_mm/1000 × h_mm/1000      [kN/m self-weight]
w_d  = dead_load_knm + w_sw
w_u  = 1.2×w_d + 1.6×live_load_knm
```

### Step 2 — Moments & Shear (Simply Supported)
```
M_u_mid  = w_u × span_m² / 8           [kN·m]
V_u_face = w_u × span_m / 2            [kN]
d = h_mm - cover_mm - stirrup_dia - bar_dia/2
```

### Step 3 — Flexural Steel (Midspan)
```
R_n = M_u_mid×10⁶ / (φ_flex × b_mm × d²)
ρ = 0.85×fc/fy × (1 - sqrt(1 - 2×R_n/(0.85×fc)))
ρ = max(ρ, max(0.25×sqrt(fc)/fy, 1.4/fy))   [ACI 9.6.1.2]
A_s_bot = ρ × b_mm × d                 [mm²]
select bars: n_bot⌀dia_bot
```

### Step 4 — Shear Design
```
V_u_d = V_u_face - w_u×d/1000          [kN at d from face]
V_c = 0.17×sqrt(fc)×b_mm×d/1000       [kN]
if V_u_d ≤ φ_shear×V_c/2:
  stirrups = minimum only
else:
  A_v_req = (V_u_d/φ_shear - V_c)×s / (fy×d/1000)
  s_max = min(d/2, 600)               [ACI 9.7.6.2.2]
  select stirrup: dia=10mm, s≤s_max
```

### Step 5 — Top Steel (if continuous)
```
if support_type == continuous:
  M_u_support = w_u × span_m² / 12
  compute A_s_top using same flexure procedure
  n_top⌀dia_top
```

---

## DrawingPrimitive Emission

### Longitudinal Elevation (1:50)

| Primitive | Layer | Description |
|---|---|---|
| rect | S-BEAM-POCH | Beam outline |
| hatch | S-BEAM-POCH | Concrete hatch |
| line ×n_bot | S-BEAM-RBAR | Bottom bars full length |
| line ×n_top | S-BEAM-RBAR | Top bars (supports if continuous) |
| line ×m | S-BEAM-RBAR | Stirrups at spacing s |
| dim_linear | S-ANNO-DIMS | Span dim |
| dim_linear | S-ANNO-DIMS | Stirrup zone dims |
| text | S-ANNO-TEXT | "B1 — b×h — 1:50" |
| text | S-ANNO-TEXT | "n⌀d bot / n⌀d top" |
| text | S-ANNO-TEXT | "⌀10 @ s mm stirrups" |

### Cross-Section at Midspan (1:20)

| Primitive | Layer | Description |
|---|---|---|
| rect | S-BEAM-POCH | b×h outline |
| hatch | S-BEAM-POCH | Concrete hatch |
| rebar ×n_bot | S-BEAM-RBAR | Bottom bars |
| polyline | S-BEAM-RBAR | Stirrup shape |
| dim_linear ×4 | S-ANNO-DIMS | b, h, cover, d |

### Cross-Section at Support (1:20)

Same as midspan but with top bars shown instead.

---

## CalculationSummary Fields

```json
{
  "element": "beam",
  "tag": "B1",
  "size_mm": "b × h",
  "bot_bars": "n⌀d",
  "top_bars": "n⌀d or N/A",
  "stirrups": "⌀10 @ s mm",
  "Mu_mid_knm": 0.0,
  "Vu_kn": 0.0,
  "rho_bot": 0.0,
  "phi_Mn_knm": 0.0,
  "phi_Vn_kn": 0.0,
  "flexure_util": 0.0,
  "shear_util": 0.0,
  "code": "ACI_318_19"
}
```
