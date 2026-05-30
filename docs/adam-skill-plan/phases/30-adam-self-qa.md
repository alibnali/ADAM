# P5-T05 — ADAM Self-QA Protocol

## Purpose
Before emitting any drawing or calculation, ADAM must execute this internal QA pass.

---

## Stage 1 — Input Validation

```
FOR EACH field IN engine.required_inputs:
  IF field.blocking AND value IS NULL:
    RAISE ERR-INPUT-MISSING
    STOP — ask user
  IF value IS NOT correct_type:
    RAISE ERR-INPUT-TYPE
    STOP — ask user
  IF value NOT IN plausible_range:
    RAISE ERR-INPUT-RANGE
    WARN user — await confirmation before continuing

FOR EACH pair IN engine.conflict_rules:
  IF conflict_condition IS TRUE:
    RAISE ERR-INPUT-CONFLICT
    STOP — ask user to resolve
```

---

## Stage 2 — Calculation Verification

```
RUN calculation_sequence(engine)

FOR EACH assertion IN engine.design_assertions:
  IF assertion FAILS:
    IF auto_fix AVAILABLE:
      APPLY auto_fix
      RE-RUN affected calculations
      LOG fix applied
    ELSE:
      RAISE error_code
      STOP or WARN per severity

FOR EACH edge_case IN EC_REGISTRY:
  IF edge_case.condition(inputs) IS TRUE:
    APPLY edge_case.action
```

---

## Stage 3 — Drawing Validation

```
FOR EACH primitive IN drawing.primitives:
  ASSERT primitive.layer IN layer_registry        → else ERR-DWG-LAYER-UNKNOWN
  ASSERT primitive.coordinates WITHIN sheet_bounds → else ERR-DWG-SCALE-MISMATCH

FOR EACH pair (pA, pB) IN primitives:
  IF pA.bounds INTERSECTS pB.bounds:
    IF overlap IS significant:
      APPLY offset_fix                              → ERR-DWG-OVERLAP (auto-fixed)

ASSERT dim_count MATCHES spec_table[engine]
ASSERT text_labels NOT EMPTY
ASSERT title_block IS PRESENT
```

---

## Stage 4 — CalculationSummary Completeness

```
FOR EACH field IN engine.summary_schema:
  ASSERT field IS NOT NULL
  ASSERT field IS NOT zero UNLESS zero_is_valid

ASSERT code_reference IS PRESENT
ASSERT element_tag IS PRESENT
```

---

## Stage 5 — Final Output Gate

```
IF any BLOCK-level error IS unresolved:
  DO NOT emit drawing
  PRESENT error list to user
  AWAIT corrected inputs

IF only WARN-level errors exist:
  EMIT drawing WITH warning annotations
  PREPEND disclaimer block to output

IF all checks PASS:
  EMIT drawing + CalculationSummary + EngineerChecklist reference
  APPEND: "Review with engineer checklist (29-engineer-checklist.md) before use."
```

---

## Disclaimer Block Template

```
⚠ ADAM PRELIMINARY OUTPUT
Engine:  {engine_id} | Code: {code_ref}
Date:    {date}
Warnings: {warn_list}

This output is AI-generated and preliminary.
Must be reviewed and stamped by a licensed engineer before construction use.
```

---

## QA Summary Checklist (fast-path)

- [ ] All BLOCKING inputs received
- [ ] All design assertions pass
- [ ] All edge cases evaluated
- [ ] All primitives in valid layers
- [ ] Primitive count matches spec
- [ ] CalculationSummary complete
- [ ] Title block present
- [ ] Disclaimer present (if warnings exist)
- [ ] Engineer checklist reference appended
