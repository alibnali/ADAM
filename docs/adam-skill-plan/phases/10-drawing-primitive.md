# P2-T03 — DrawingPrimitive Model

## Purpose

The DrawingPrimitive is the atomic unit of every ADAM drawing. Domain engines emit primitives; the OutputRenderer converts them to SVG/DXF/PDF. This separation makes domain engines format-agnostic.

---

## Primitive Types

| type | Description | Key fields |
|---|---|---|
| `line` | Straight segment | x1, y1, x2, y2 |
| `polyline` | Multi-segment open path | points: [[x,y],…] |
| `polygon` | Closed shape | points, closed: true |
| `rect` | Axis-aligned rectangle | x, y, width, height |
| `circle` | Full circle | cx, cy, radius |
| `arc` | Circular arc | cx, cy, radius, start_angle_deg, end_angle_deg |
| `text` | Label / annotation | x, y, content, font_size_mm, anchor |
| `dim_linear` | Linear dimension | x1, y1, x2, y2, offset, text_override |
| `dim_angular` | Angular dimension | center, p1, p2, radius, text_override |
| `hatch` | Fill pattern | boundary_points, pattern_id, scale, angle_deg |
| `rebar` | Rebar symbol | x, y, diameter_mm, orientation, count |
| `symbol` | Named block insert | symbol_id, x, y, scale, rotation_deg |
| `viewport` | Drawing area | x, y, width, height, view_name |
| `title_block` | Sheet title block | project, title, drawn_by, date, scale, sheet |

---

## Schema (→ schemas/DrawingPrimitive.json)

```json
{
  "id":       "PRM-RECT-001",
  "type":     "rect",
  "layer":    "S-COLS-POCH",
  "geometry": { "x": 100, "y": 100, "width": 300, "height": 500 },
  "style":    { "color": "BYLAYER", "linetype": "BYLAYER", "lineweight": 0 },
  "metadata": { "element_ref": "C1", "tag": "C1" },
  "z_order":  5,
  "visible":  true
}
```

---

## Coordinate System

- Origin `(0,0)` = bottom-left of sheet.
- X → right, Y → up.
- All units: **millimetres** at model scale 1:1.
- Scale applied by OutputRenderer at render time.
- Angles: degrees, counter-clockwise from positive X.

---

## Standard Geometry Examples

```json
// line
{ "x1": 0, "y1": 0, "x2": 500, "y2": 0 }

// text
{ "x": 100, "y": 50, "content": "B1 300×600", "font_size_mm": 3.5, "anchor": "left" }

// dim_linear
{ "x1": 0, "y1": 0, "x2": 500, "y2": 0, "offset": 50, "text_override": null }

// rebar (section cut)
{ "x": 150, "y": 150, "diameter_mm": 16, "orientation": "longitudinal", "count": 4 }

// hatch
{ "boundary_points": [[0,0],[500,0],[500,600],[0,600]], "pattern_id": "ANSI31", "scale": 1.0, "angle_deg": 45 }
```
