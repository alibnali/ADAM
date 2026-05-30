# P4-T01 — CIVIL-SITE-01: Site Plan Engine

## Engine ID: `CIVIL-SITE-01`
## Drawing Output: Site Plan 1:500 or 1:1000

## Required Inputs

| Field | Type | Notes |
|---|---|---|
| `plot_width_m` | number | BLOCKING |
| `plot_depth_m` | number | BLOCKING |
| `setback_front_m` | number | Default 5.0 |
| `setback_side_m` | number | Default 2.5 |
| `setback_rear_m` | number | Default 3.0 |
| `road_width_m` | number | Default 12.0 |
| `north_angle_deg` | number | Default 0 |
| `buildings` | array | Building footprints |

### Building Object
```json
{ "tag": "B1", "x_m": 5.0, "y_m": 3.0, "width_m": 12.0, "depth_m": 15.0, "floors": 3 }
```

## Drawing Logic

- **Plot boundary**: thick closed `polyline` layer `C-PROP-LINE`
- **Setback lines**: dashed `polyline` offset inward, layer `C-PROP-SETB`
- **Road**: two parallel lines + asphalt hatch
- **Buildings**: `rect` per footprint + tag label
- **Annotations**: north arrow, scale bar, BCR label, total area label
- **Dims**: overall plot + setbacks + building positions

## DrawingPrimitive Emission

| Primitive | Layer | Description |
|---|---|---|
| polyline (closed) | C-PROP-LINE | Plot boundary |
| polyline (dashed) ×4 | C-PROP-SETB | Setback lines |
| line ×2 | C-ROAD-CNTR | Road edges |
| hatch | C-ROAD-CNTR | Road fill |
| rect per building | A-WALL-FULL | Building footprints |
| dim_linear ×n | S-ANNO-DIMS | Plot + setback + building dims |
| text ×n | S-ANNO-TEXT | Labels, BCR, area |
| symbol | A-ANNO-SYMB | North arrow |
| symbol | A-ANNO-SYMB | Scale bar |
| title_block | S-TTLB-FULL | Sheet title |

## CalculationSummary

```json
{
  "element": "site_plan",
  "tag": "SP-01",
  "plot_area_m2": 0.0,
  "building_footprint_m2": 0.0,
  "bcr_percent": 0.0,
  "setbacks": { "front": 5.0, "side": 2.5, "rear": 3.0 },
  "scale": "1:500"
}
```
