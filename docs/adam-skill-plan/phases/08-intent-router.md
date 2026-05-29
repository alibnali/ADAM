# P2-T01 — IntentRouter

## Purpose

The IntentRouter is the first stage of the ADAM kernel. It receives a `CanonicalDesignRequest` and resolves:

1. **Discipline** — structural / architectural / civil / mep / geotechnical
2. **Element type** — footing / column / beam / slab / wall / stair / site-plan / …
3. **Drawing type** — plan / section / elevation / detail / schedule / 3D
4. **Domain engine** — which Phase 03 engine handles this request
5. **Ambiguity score** — confidence 0.0 → 1.0

If confidence < 0.80, the IntentRouter emits a `ClarificationQuestion` instead of forwarding.

---

## Input / Output Contract

```
Input:   CanonicalDesignRequest
Output:  RouterDecision | ClarificationQuestion
```

### RouterDecision Schema (→ schemas/RouterDecision.json)

```json
{
  "discipline":     "structural",
  "element_type":   "isolated_footing",
  "drawing_types":  ["plan", "section"],
  "engine_id":      "STR-FOOTING-01",
  "confidence":     0.95,
  "resolved_code":  "ACI_318_19",
  "assumptions_made": []
}
```

---

## Routing Table v0.1

| Keyword signals | discipline | element_type | engine_id |
|---|---|---|---|
| footing, isolated, spread, foundation | structural | isolated_footing | STR-FOOTING-01 |
| combined footing | structural | combined_footing | STR-FOOTING-02 |
| column, col, pillar | structural | column | STR-COLUMN-01 |
| beam, girder | structural | beam | STR-BEAM-01 |
| slab, one-way, two-way, flat plate | structural | slab | STR-SLAB-01 |
| shear wall, wall, sw | structural | shear_wall | STR-WALL-01 |
| stair, staircase | structural | stair | STR-STAIR-01 |
| floor plan, room, layout | architectural | floor_plan | ARCH-PLAN-01 |
| site plan, plot, land | civil | site_plan | CIVIL-SITE-01 |
| pipe, plumbing, drain | mep | plumbing | MEP-PLUMB-01 |
| HVAC, duct, ventilation | mep | hvac | MEP-HVAC-01 |
| electrical, panel, circuit | mep | electrical | MEP-ELEC-01 |

---

## Ambiguity Rules

| Condition | Action |
|---|---|
| No element type keyword found | Ask: "What type of structural element?" |
| Multiple disciplines match equally | Ask: "Is this structural or architectural?" |
| drawing_type not specified | Default: ["plan", "section"]. Log ASM-DRAW-TYPE-001. |
| confidence < 0.80 | Emit ClarificationQuestion before routing. |

---

## Error Codes

| Code | Severity | Trigger |
|---|---|---|
| `ROUTER-NO-MATCH` | error | No engine found |
| `ROUTER-AMBIGUOUS` | warning | confidence < 0.80 |
| `ROUTER-CODE-FALLBACK` | info | Code resolved via default rules |
