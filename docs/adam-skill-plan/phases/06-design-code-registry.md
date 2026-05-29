# P1-T06 — Design Code Registry

## Status
- Phase: Phase 01 — Foundation and Contracts
- Task: P1-T06 — **FINAL TASK of Phase 01**
- Depends on: P1-T05 ✅
- Next: Phase 02 — Core Kernel and Drawing Abstraction

---

## Purpose

The Design Code Registry is a machine-readable catalogue of every design standard ADAM supports. It defines:
- Supported codes per discipline.
- Default parameters (phi factors, safety factors, exposure classes) per code.
- Conflict rules when a request references multiple codes.
- How ADAM selects a default code when none is specified.

No domain engine may hardcode design parameters. All code-dependent constants must be resolved by querying this registry.

---

## Registry Structure

Each entry in the registry is a `DesignCodeEntry` object.

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "https://github.com/alibnali/ADAM/schemas/DesignCodeEntry.json",
  "title": "DesignCodeEntry",
  "type": "object",
  "required": ["code_id", "name", "edition", "discipline", "region", "unit_system", "parameters"],
  "additionalProperties": false,
  "properties": {
    "code_id":      { "type": "string", "description": "Machine key. No spaces.", "pattern": "^[A-Z0-9_-]+$" },
    "name":         { "type": "string", "description": "Full human-readable code name." },
    "edition":      { "type": "string", "description": "Year or version label." },
    "discipline":   { "type": "string", "enum": ["structural", "architectural", "civil", "mep", "geotechnical", "general"] },
    "region":       { "type": "string", "description": "Primary geographic region of use.", "examples": ["USA", "EU", "UK", "Saudi Arabia", "Global"] },
    "unit_system":  { "type": "string", "enum": ["SI", "Imperial", "both"] },
    "parameters":   { "type": "object", "description": "Default design parameters for this code.", "additionalProperties": true },
    "notes":        { "type": "string" },
    "supported":    { "type": "boolean", "description": "Whether ADAM fully supports this code in the current version.", "default": false }
  }
}
```

---

## Supported Codes — v0.1 (Structural Focus)

### ACI 318-19 (Primary)

```json
{
  "code_id": "ACI_318_19",
  "name": "ACI 318-19: Building Code Requirements for Structural Concrete",
  "edition": "2019",
  "discipline": "structural",
  "region": "USA / Middle East",
  "unit_system": "both",
  "supported": true,
  "parameters": {
    "phi_flexure":             0.90,
    "phi_shear":               0.75,
    "phi_compression_tied":    0.65,
    "phi_compression_spiral":  0.75,
    "phi_bearing":             0.65,
    "phi_torsion":             0.75,
    "lambda_normal_weight":    1.00,
    "lambda_lightweight":      0.75,
    "min_cover_footing_mm":    75,
    "min_cover_column_mm":     40,
    "min_cover_beam_mm":       40,
    "min_cover_slab_mm":       20,
    "min_cover_wall_mm":       20,
    "rho_min_flexure":         0.0018,
    "rho_max_flexure":         0.0206,
    "beta1_fc25":              0.85,
    "beta1_fc30":              0.83,
    "beta1_fc35":              0.81,
    "load_combo_strength_1":   "1.2D + 1.6L",
    "load_combo_strength_2":   "1.4D",
    "punching_shear_vc_coeff": 0.33
  },
  "notes": "Default code for all structural elements unless overridden in request."
}
```

### EC2 (Eurocode 2)

```json
{
  "code_id": "EC2_2004",
  "name": "EN 1992-1-1:2004 Eurocode 2: Design of Concrete Structures",
  "edition": "2004",
  "discipline": "structural",
  "region": "EU / International",
  "unit_system": "SI",
  "supported": true,
  "parameters": {
    "gamma_c":                 1.50,
    "gamma_s":                 1.15,
    "alpha_cc":                1.00,
    "min_cover_cmin_b":        "max(bar_diameter, 10mm)",
    "min_cover_cmin_dur_xc1":  15,
    "min_cover_cmin_dur_xc2":  25,
    "min_cover_cmin_dur_xc3":  30,
    "min_cover_cmin_dur_xc4":  40,
    "delta_cdev":              10,
    "rho_min_flexure":         0.0013,
    "lambda_factor":           0.80,
    "eta_factor":              1.00,
    "load_combo_persistent":   "1.35*Gk + 1.5*Qk",
    "punching_vrd_c_coeff":    0.18,
    "k_size_factor":           "1 + sqrt(200/d) <= 2.0"
  },
  "notes": "Used when request specifies EC2 or region is EU. Nationally Determined Parameters (NDPs) may override these values."
}
```

### BS 8110

```json
{
  "code_id": "BS_8110_1997",
  "name": "BS 8110-1:1997 Structural Use of Concrete",
  "edition": "1997",
  "discipline": "structural",
  "region": "UK / Former British Commonwealth",
  "unit_system": "SI",
  "supported": true,
  "parameters": {
    "gamma_c":                 1.50,
    "gamma_s":                 1.05,
    "min_cover_mild_mm":       25,
    "min_cover_moderate_mm":   35,
    "min_cover_severe_mm":     40,
    "min_cover_very_severe_mm":50,
    "rho_min_flexure":         0.0013,
    "load_combo_uls":          "1.4*Gk + 1.6*Qk",
    "vc_coeff":                0.79,
    "k1":                      1.25
  },
  "notes": "Legacy code. Use EC2 for new UK projects post-2010."
}
```

### SBC 304 (Saudi Building Code)

```json
{
  "code_id": "SBC_304_2018",
  "name": "SBC 304: Saudi Building Code — Concrete Structures",
  "edition": "2018",
  "discipline": "structural",
  "region": "Saudi Arabia",
  "unit_system": "SI",
  "supported": true,
  "parameters": {
    "phi_flexure":             0.90,
    "phi_shear":               0.75,
    "phi_compression_tied":    0.65,
    "phi_compression_spiral":  0.75,
    "phi_bearing":             0.65,
    "min_cover_footing_mm":    75,
    "min_cover_column_mm":     40,
    "min_cover_beam_mm":       40,
    "min_cover_slab_mm":       20,
    "rho_min_flexure":         0.0018,
    "load_combo_strength_1":   "1.2D + 1.6L",
    "load_combo_strength_2":   "1.4D",
    "punching_shear_vc_coeff": 0.33
  },
  "notes": "SBC 304 is largely aligned with ACI 318. Phi factors and load combinations are identical to ACI 318-19. Regional exposure classes apply for cover."
}
```

### IBC 2021 (International Building Code)

```json
{
  "code_id": "IBC_2021",
  "name": "International Building Code 2021",
  "edition": "2021",
  "discipline": "general",
  "region": "Global / USA",
  "unit_system": "both",
  "supported": false,
  "parameters": {},
  "notes": "Planned for Phase 04. IBC references ACI 318 for concrete design."
}
```

---

## Default Code Selection Rules

When `design_code` is absent or `design_code.primary` is null in the request, ADAM applies this selection algorithm:

```
1. If discipline = 'structural' AND region hint = 'Saudi Arabia'  →  SBC_304_2018
2. If discipline = 'structural' AND region hint = 'EU'            →  EC2_2004
3. If discipline = 'structural' AND region hint = 'UK'            →  BS_8110_1997
4. If discipline = 'structural' (no region / default)            →  ACI_318_19
5. If discipline = 'civil'                                        →  ACI_318_19 (structural elements) + AASHTO (roads) [AASHTO = Phase 04]
6. If no rule matches                                             →  ACI_318_19 + assumption logged as ASM-NORM-CODE-001
```

All default code selections are recorded in the `assumption_log` with `confidence: "medium"` and `requires_engineer_confirmation: true`.

---

## Multi-Code Conflict Rules

A request may specify both `design_code.primary` and `design_code.supplementary`. The following rules govern conflicts:

| Situation | Rule |
|---|---|
| Primary and supplementary use same phi factors | Use primary. Log supplementary as reference only. |
| Primary and supplementary have different phi factors for same check | Use primary. Log conflict as warning `CODE-CONFLICT-PHI`. |
| Supplementary provides a parameter primary does not define | Use supplementary value. Log as assumption `ASM-STR-CODE-SUP-*`. |
| Two supplementary codes conflict with each other | Use primary defaults. Emit error `CODE-CONFLICT-SUPPLEMENTARY`. |
| Requested code is `supported: false` | Reject with error `CODE-UNSUPPORTED`. List supported codes in error message. |

---

## Parameter Override Hierarchy

Parameters are resolved in this order (highest priority wins):

```
1. request.inputs (explicit engineer input)        ← highest
2. request.constraints (explicit constraint)  
3. design_code.primary parameters (this registry)
4. design_code.supplementary parameters
5. ADAM built-in safe defaults                     ← lowest
```

Any value resolved from level 3 or below is recorded in `assumption_log`.

---

## Error Codes Introduced

| Code | Severity | Trigger |
|---|---|---|
| `CODE-UNSUPPORTED` | error | Requested code not in registry or `supported: false` |
| `CODE-CONFLICT-PHI` | warning | Primary and supplementary have different phi for same check |
| `CODE-CONFLICT-SUPPLEMENTARY` | error | Two supplementary codes conflict for same parameter |
| `CODE-DEFAULT-APPLIED` | info | No code specified — default applied per selection rules |

---

## Phase 01 Completion Checklist

With P1-T06 complete, all Phase 01 contracts are defined:

| Contract | File | Status |
|---|---|---|
| Product Charter | `phases/01-product-charter.md` | ✅ |
| CanonicalDesignRequest | `schemas/CanonicalDesignRequest.json` | ✅ |
| CanonicalDesignResult | `schemas/CanonicalDesignResult.json` | ✅ |
| AssumptionEntry | `schemas/AssumptionEntry.json` | ✅ |
| ClarificationQuestion | `schemas/ClarificationQuestion.json` | ✅ |
| Drawing Layer Standard | `schemas/drawing-layers-registry.json` | ✅ |
| Design Code Registry | `schemas/design-code-registry.json` | ✅ |

**Phase 01 is complete. Phase 02 begins: Core Kernel and Drawing Abstraction.**
