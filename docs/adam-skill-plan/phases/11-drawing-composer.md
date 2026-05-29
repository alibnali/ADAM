# P2-T04 — DrawingComposer

## Purpose

Assembles an ordered list of `DrawingPrimitive` objects into a complete `DrawingDocument` ready for rendering.

Responsibilities:
1. Verify each primitive's layer exists in `drawing-layers-registry`.
2. Resolve `BYLAYER` styles to actual values.
3. Arrange viewports on the sheet.
4. Insert title block, north arrow, scale bar.
5. Validate geometry (no degenerate primitives).
6. Assign z-order.

---

## Input / Output Contract

```
Input:   List<DrawingPrimitive>  +  DrawingComposerConfig
Output:  DrawingDocument
```

### DrawingComposerConfig

```json
{
  "sheet_size":  "A1",
  "orientation": "landscape",
  "scale":       "1:50",
  "viewports": [
    { "view_name": "PLAN",       "position": "top-left",     "width_mm": 420, "height_mm": 297 },
    { "view_name": "SECTION-AA", "position": "top-right",    "width_mm": 210, "height_mm": 297 },
    { "view_name": "DETAIL-1",   "position": "bottom-right", "width_mm": 210, "height_mm": 148 }
  ],
  "title_block": {
    "project":  "ADAM Demo",
    "title":    "Isolated Footing F1",
    "drawn_by": "ADAM v0.1",
    "date":     "2026-05-30",
    "scale":    "1:50",
    "sheet":    "S-101"
  }
}
```

---

## Sheet Size Reference

| Size | W × H (mm) | Typical Use |
|---|---|---|
| A4 | 210 × 297 | Details, schedules |
| A3 | 297 × 420 | Sections, details |
| A2 | 420 × 594 | Beam/column drawings |
| A1 | 594 × 841 | Floor plans, foundations |
| A0 | 841 × 1189 | Site plans |
| 24×36 | 610 × 914 | US engineering |
| 30×42 | 762 × 1067 | US large format |

---

## Z-Order Stack (top → bottom)

```
9  text
8  dim_linear, dim_angular
7  rebar
6  symbol, title_block
5  line, polyline, polygon, rect, circle, arc
4  hatch
3  viewport borders
2  sheet border
1  background
```

---

## Validation Rules

| Rule | Error Code |
|---|---|
| Layer not in registry | `COMPOSE-LAYER-UNKNOWN` |
| Zero-dimension geometry | `COMPOSE-GEOM-DEGENERATE` |
| Viewports overflow sheet | `COMPOSE-OVERFLOW` |
| Missing title block field | `COMPOSE-TITLEBLOCK-INCOMPLETE` |
| Duplicate primitive ID | `COMPOSE-ID-DUPLICATE` |
