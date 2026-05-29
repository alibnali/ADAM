# P3-T05 — ARCH-PLAN-01: Architectural Floor Plan Engine

## Engine ID: `ARCH-PLAN-01`
## Type: Architectural (no structural calculation)
## Drawing Output: Floor Plan at 1:100

---

## Required Inputs

| Field | Type | Notes |
|---|---|---|
| `rooms` | array | List of room objects — BLOCKING |
| `wall_thickness_mm` | number | Default 200mm |
| `door_width_mm` | number | Default 900mm |
| `window_width_mm` | number | Default 1200mm |
| `scale` | string | Default "1:100" |

### Room Object Schema
```json
{
  "name": "Living Room",
  "width_m": 5.0,
  "depth_m": 4.0,
  "x_origin_m": 0.0,
  "y_origin_m": 0.0,
  "doors": [{"wall": "south", "position_m": 2.0, "swing": "inward"}],
  "windows": [{"wall": "north", "position_m": 1.5}]
}
```

---

## Drawing Logic

### Walls
- Outer wall = `wall_thickness_mm`
- Partition = `wall_thickness_mm / 2`
- Wall primitive: two parallel `polyline` at offset = wall_thickness, filled hatch between.

### Doors
- Opening gap in wall = `door_width_mm`
- Door leaf: `line` from hinge to 90° arc end.
- Swing: `arc` quarter-circle radius = door_width_mm.

### Windows
- Opening gap in wall = `window_width_mm`
- Three parallel lines across opening (standard plan symbol).

### Room Labels
- `text` at room centroid: ROOM NAME (bold), below it: width×depth m.

### Dimensions
- Overall building width + depth.
- Each room width + depth.
- Wall thickness at 1 location.

---

## DrawingPrimitive Emission

| Primitive | Layer | Description |
|---|---|---|
| polyline ×2 per wall | A-WALL-FULL | Wall outlines (outer + inner face) |
| hatch | A-WALL-FULL | Wall hatch ANSI31 |
| line + arc | A-DOOR-FULL | Door leaf + swing arc |
| line ×3 | A-GLAZ-FULL | Window symbol |
| text | A-ANNO-TEXT | Room name + dimensions |
| dim_linear | A-ANNO-DIMS | Overall + room dims |
| symbol | A-ANNO-SYMB | North arrow (top-right) |
| symbol | A-ANNO-SYMB | Scale bar |
| title_block | S-TTLB-FULL | Sheet title block |

---

## CalculationSummary Fields

```json
{
  "element": "floor_plan",
  "tag": "FP-01",
  "total_area_m2": 0.0,
  "room_count": 0,
  "rooms": [
    { "name": "Living Room", "area_m2": 0.0 }
  ],
  "scale": "1:100"
}
```
