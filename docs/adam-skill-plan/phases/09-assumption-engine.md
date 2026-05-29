# P2-T02 — AssumptionEngine

## Purpose

The AssumptionEngine fills every gap in a `CanonicalDesignRequest` before the domain engine runs.

Rule: **Never block execution unless a field is both missing AND has no safe default.**

---

## Input / Output Contract

```
Input:   CanonicalDesignRequest  +  RouterDecision
Output:  EnrichedRequest  +  List<AssumptionEntry>  +  List<ClarificationQuestion>
```

---

## Master Assumption Table — Structural

### Isolated Footing

| Field | Default | Confidence |
|---|---|---|
| `fc_mpa` | 25 | medium |
| `fy_mpa` | 420 | medium |
| `soil_bearing_kpa` | 150 | low |
| `cover_mm` | 75 | high |
| `column_shape` | rectangular | medium |
| `footing_depth_m` | 1.5 | low |
| `load_dead_kn` | — | **MUST ASK** |
| `load_live_kn` | — | **MUST ASK** |

### Column

| Field | Default | Confidence |
|---|---|---|
| `fc_mpa` | 25 | medium |
| `fy_mpa` | 420 | medium |
| `cover_mm` | 40 | high |
| `tie_spacing_mm` | 300 | medium |
| `rho_target` | 0.02 | medium |
| `height_m` | 3.0 | low |
| `axial_load_kn` | — | **MUST ASK** |
| `moment_knm` | 0 | low |

### Beam

| Field | Default | Confidence |
|---|---|---|
| `fc_mpa` | 25 | medium |
| `fy_mpa` | 420 | medium |
| `cover_mm` | 40 | high |
| `width_mm` | span/12×1000 | low |
| `depth_mm` | span/10×1000 | medium |
| `support_type` | simply_supported | medium |
| `span_m` | — | **MUST ASK** |
| `dead_load_knm` | — | **MUST ASK** |
| `live_load_knm` | — | **MUST ASK** |

### Slab

| Field | Default | Confidence |
|---|---|---|
| `fc_mpa` | 25 | medium |
| `fy_mpa` | 420 | medium |
| `cover_mm` | 20 | high |
| `thickness_mm` | span/20×1000 | medium |
| `live_load_knm2` | 2.0 | low |
| `dead_load_sw` | auto (γ=24 kN/m³) | high |
| `slab_type` | one_way | medium |
| `span_m` | — | **MUST ASK** |

---

## Assumption ID Convention

```
ASM-{DOMAIN}-{ELEMENT}-{SEQ}

Examples:
  ASM-STR-FTG-001   cover defaulted to 75mm
  ASM-STR-COL-001   height defaulted to 3.0m
  ASM-STR-BM-001    depth from span rule
  ASM-DRAW-TYPE-001 drawing type defaulted to plan+section
  ASM-NORM-CODE-001 code defaulted to ACI 318-19
```

---

## Blocking vs Non-Blocking

| Type | Behavior |
|---|---|
| Non-blocking (safe default exists) | Fill, log AssumptionEntry, continue. |
| Blocking (safety-critical, no default) | Emit ClarificationQuestion, suspend. |

Loads (`axial_load_kn`, `dead_load_knm`, `live_load_knm`, `span_m`) are **always blocking**.

---

## Error Codes

| Code | Severity | Trigger |
|---|---|---|
| `ASM-BLOCKING-FIELD` | error | Safety-critical field missing |
| `ASM-LOW-CONFIDENCE` | warning | Default confidence = low |
| `ASM-OVERRIDE` | info | User input overrides registry default |
