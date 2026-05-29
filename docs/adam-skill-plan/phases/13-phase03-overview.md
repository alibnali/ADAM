# Phase 03 — Domain Engines

## Status
- Depends on: Phase 02 ✅
- Tasks: P3-T01 → P3-T05
- Output: Five fully specified domain engines that perform real engineering computation and emit DrawingPrimitive lists.

---

## Phase Goal

Each Domain Engine:
1. Receives an `EnrichedRequest` (from AssumptionEngine) + `RouterDecision`.
2. Runs engineering calculations per the resolved design code.
3. Emits an ordered `List<DrawingPrimitive>` + a `CalculationSummary`.
4. Never touches SVG, DXF, or any output format — format-agnostic by contract.

---

## Pipeline Position

```
EnrichedRequest + RouterDecision
        │
        ▼
  [Domain Engine]   ◄── this phase
        │
        ├── List<DrawingPrimitive>
        └── CalculationSummary
        │
        ▼
  [DrawingComposer]  (Phase 02)
```

---

## Engines in This Phase

| Task | Engine ID | Element | Code |
|---|---|---|---|
| P3-T01 | STR-FOOTING-01 | Isolated Footing | ACI 318-19 |
| P3-T02 | STR-COLUMN-01 | RC Column | ACI 318-19 |
| P3-T03 | STR-BEAM-01 | RC Beam | ACI 318-19 |
| P3-T04 | STR-SLAB-01 | One-Way Slab | ACI 318-19 |
| P3-T05 | ARCH-PLAN-01 | Floor Plan | Architectural |
