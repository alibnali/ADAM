# P5-T03 — Edge Cases Registry

## Purpose
Document all known dangerous edge cases where standard formulas produce unexpected results or fail silently.

---

## Structural Edge Cases

### EC-STR-001 — Zero axial load on column
```
Condition: Pu = 0 (pure flexure)
Risk: φ factor flips from 0.65 to 0.90 → different design path
Action: Detect Pu/Ag < 0.10×fc, switch to beam-column path, notify user
```

### EC-STR-002 — Very short beam (span < 2×h)
```
Condition: span_m / h_mm < 2.0
Risk: Deep beam governs, standard shear formula invalid
Action: Trigger ERR-STR-DEEP-BEAM, block normal shear calc
```

### EC-STR-003 — Footing on rock (q_allow > 500 kPa)
```
Condition: q_allow_kpa > 500
Risk: Footing becomes very small, punching perimeter may exceed footing perimeter
Action: Check bo ≤ 4×B, warn if footing unusually small
```

### EC-STR-004 — Single rebar in beam tension zone
```
Condition: Calculated As requires only 1 bar
Risk: ACI requires min 2 bars in tension zone for stability
Action: Force minimum 2 bars, recalculate spacing
```

### EC-STR-005 — Slab span exceeds 6m (one-way)
```
Condition: span > 6m for one-way slab
Risk: Deflection governs, L/20 minimum thickness may be unconservative
Action: Trigger deflection check warning, suggest two-way or beam-supported
```

### EC-STR-006 — Shear wall aspect ratio > 2 (hw/lw > 2)
```
Condition: height_m / length_m > 2
Risk: Flexure controls over shear, boundary elements almost certainly required
Action: Mandatory boundary element check, flag in output
```

---

## Geometry Edge Cases

### EC-GEOM-001 — Odd number of risers
```
Condition: n_risers is odd
Risk: Landing at mid-flight falls at non-standard height
Action: Acceptable — just label correctly; warn if actual_riser deviates > 5mm from input
```

### EC-GEOM-002 — Room with zero area
```
Condition: area_m2 = 0 or width/depth = 0
Risk: Division by zero in load calculations
Action: ERR-INPUT-RANGE, block
```

### EC-GEOM-003 — Building footprint outside plot boundary
```
Condition: building x + width > plot_width - setback
Risk: Violates setback, illegal drawing
Action: ERR-INPUT-CONFLICT, show violation dimension
```

---

## MEP Edge Cases

### EC-MEP-001 — No fixtures on a floor
```
Condition: A floor level has zero fixtures
Risk: Riser diagram shows dead branch
Action: Warn user, skip branch in riser but keep stack continuous
```

### EC-MEP-002 — Single-phase with load > 15 kVA
```
Condition: phases=1 AND total_kva > 15
Risk: Technically feasible but unusual, may need utility approval
Action: WARN — suggest upgrading to 3-phase
```

### EC-MEP-003 — HVAC in unoccupied room (occupants=0)
```
Condition: occupants = 0
Risk: CFM formula gives partial result (area component only)
Action: Use area-only ventilation, label "unoccupied" on drawing
```

---

## Drawing Edge Cases

### EC-DWG-001 — Extremely long rebar list (> 20 rows)
```
Condition: rebar_schedule has > 20 rows
Risk: Table overflows sheet
Action: Split into continuation sheet, add sheet reference
```

### EC-DWG-002 — Scale produces line < 0.5mm on paper
```
Condition: Dimension × scale_factor < 0.5mm
Risk: Invisible line in print
Action: Increase scale or suppress element with note
```
