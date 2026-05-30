# P5-T02 — Error Catalogue

## Error Format
```
Code:    ERR-XXXX-YYY
Trigger: condition that causes this error
Message: human-readable message shown to user
Action:  what ADAM does (block / warn / ask)
```

---

## Category: INPUT

### ERR-INPUT-MISSING
```
Trigger: Any BLOCKING field not provided
Message: "Field `{field_name}` is required to generate this drawing. Please provide it."
Action:  BLOCK — do not proceed, ask user
```

### ERR-INPUT-TYPE
```
Trigger: Field value is wrong type (string instead of number)
Message: "`{field}` must be a number (received: {value})"
Action:  BLOCK — ask for correction
```

### ERR-INPUT-RANGE
```
Trigger: Value outside engineering-plausible range
Examples:
  fc_mpa < 17 or > 70
  rho_required > 0.08 (column)
  riser_mm > 190 (IBC)
  BCR > 75%
Message: "`{field}` value {value} is outside the acceptable range [{min}–{max}]. Please verify."
Action:  WARN — continue with user confirmation
```

### ERR-INPUT-CONFLICT
```
Trigger: Two inputs contradict each other
Example: column_b > footing_B (column wider than footing)
Message: "Conflict: `{field_a}` ({va}) and `{field_b}` ({vb}) are incompatible."
Action:  BLOCK — ask user to resolve
```

---

## Category: STRUCTURAL

### ERR-STR-SHEAR-FAIL
```
Trigger: φVn < Vu after maximum steel
Message: "Shear capacity insufficient. Increase beam width or concrete strength."
Action:  BLOCK — suggest alternatives
```

### ERR-STR-PUNCH-FAIL
```
Trigger: Punching shear φVn < Vu (footing)
Message: "Punching shear fails. Increase footing depth h or column size."
Action:  BLOCK — auto-increment h and retry once
```

### ERR-STR-REBAR-CONGESTION
```
Trigger: Calculated bar spacing < 25mm (clear spacing min)
Message: "Bar spacing too tight. Use larger bar diameter or increase section."
Action:  WARN — show alternative bar layout
```

### ERR-STR-SLENDER
```
Trigger: Column kL/r > 100
Message: "Column is highly slender (kL/r = {val}). Detailed second-order analysis required. ADAM result is preliminary only."
Action:  WARN — add disclaimer to drawing
```

### ERR-STR-DEEP-BEAM
```
Trigger: Beam span/d < 4
Message: "Deep beam detected (span/d = {val}). Strut-and-tie method required per ACI 318-19 §9.9."
Action:  WARN — flag on drawing, add note
```

---

## Category: MEP

### ERR-MEP-VELOCITY-HIGH
```
Trigger: Duct/pipe velocity > maximum allowed
Message: "Velocity exceeds limit for {element}. Increasing duct/pipe size."
Action:  AUTO-FIX — select next standard size up
```

### ERR-MEP-CIRCUIT-OVERLOAD
```
Trigger: Circuit load > 80% × breaker × voltage
Message: "Circuit {n} overloaded. Redistributing fixtures."
Action:  AUTO-FIX — split into two circuits
```

---

## Category: DRAWING

### ERR-DWG-OVERLAP
```
Trigger: Two primitives occupy same coordinates
Message: "Drawing overlap detected at ({x},{y}). Adjusting layout."
Action:  AUTO-FIX — offset conflicting element
```

### ERR-DWG-SCALE-MISMATCH
```
Trigger: Element dimensions don't fit on selected sheet size
Message: "Drawing at 1:{scale} exceeds A1 sheet. Switching to 1:{new_scale}."
Action:  AUTO-FIX — adjust scale
```

### ERR-DWG-LAYER-UNKNOWN
```
Trigger: Primitive assigned to undefined layer
Message: "Layer `{layer}` not in registry. Defaulting to S-ANNO-TEXT."
Action:  AUTO-FIX — use fallback layer, log warning
```
