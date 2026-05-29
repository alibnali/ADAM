# P1-T05 — Drawing Layer Standard

## Status
- Phase: Phase 01 — Foundation and Contracts
- Task: P1-T05
- Depends on: P1-T04 ✅
- Next: P1-T06 — Design Code Registry

---

## Purpose

Every DXF file produced by ADAM must follow a deterministic, consistent layer structure. This document defines:
- The **mandatory layer set** shared across all disciplines.
- **Discipline-specific layers** for structural, architectural, civil, and MEP drawings.
- Layer properties: name, ACI color index, linetype, lineweight.
- **Invariants** the Drawing Engine must enforce before emitting any DXF file.

Any DXF that violates this standard is considered malformed output and must not be returned to the client.

---

## Layer Naming Convention

```
{DISCIPLINE}-{CATEGORY}-{SUBCATEGORY}
```

| Segment | Rule | Example |
|---|---|---|
| DISCIPLINE | 3-letter uppercase code | `STR`, `ARC`, `CIV`, `MEP` |
| CATEGORY | Uppercase noun | `OUTLINE`, `REBAR`, `DIMS` |
| SUBCATEGORY | Uppercase qualifier (optional) | `MAIN`, `STIRRUP`, `EXIST` |

**Shared/global layers** use no discipline prefix: `TITLE_BLOCK`, `GRID`, `BORDER`, `NOTES`.

---

## Color Index Reference (AutoCAD ACI)

| ACI | Color | Use |
|---|---|---|
| 7 | White/Black | Primary outlines, borders, title block |
| 1 | Red | Rebar, reinforcement, structural steel |
| 2 | Yellow | Dimensions, annotations |
| 3 | Green | Grid lines, reference lines |
| 4 | Cyan | Existing elements, as-built |
| 5 | Blue | Section cuts, section marks |
| 6 | Magenta | Hatching, fill patterns |
| 8 | Dark Gray | Construction lines, hidden lines |
| 30 | Orange | MEP — plumbing, water supply |
| 140 | Violet | MEP — electrical conduit |
| 150 | Light Blue | MEP — HVAC ducts |
| 251 | Light Gray | Centerlines, symmetry axes |

---

## Linetype Reference

| Name | Use |
|---|---|
| `CONTINUOUS` | All solid outlines, rebar, dimensions |
| `DASHED` | Hidden elements, sub-surface elements |
| `CENTER` | Centerlines, axes of symmetry |
| `PHANTOM` | Future elements, proposed elements |
| `DOT` | Construction reference lines |

---

## Lineweight Reference

| Weight | Use |
|---|---|
| `0.50 mm` | Primary outlines (element boundary) |
| `0.35 mm` | Secondary outlines, rebar |
| `0.25 mm` | Dimensions, annotations, notes |
| `0.18 mm` | Grid lines, centerlines, hatching |
| `0.13 mm` | Construction lines, hidden lines |

---

## Mandatory Shared Layers (All Disciplines)

These layers must exist in every DXF file regardless of discipline.

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `BORDER` | 7 | CONTINUOUS | 0.50 | Drawing sheet border |
| `TITLE_BLOCK` | 7 | CONTINUOUS | 0.35 | Title block frame and text |
| `TITLE_TEXT` | 7 | CONTINUOUS | 0.25 | Project name, drawing title, date, engineer |
| `GRID` | 3 | CENTER | 0.18 | Structural grid or reference grid |
| `GRID_LABELS` | 3 | CONTINUOUS | 0.18 | Grid labels (A, B, C / 1, 2, 3) |
| `NOTES` | 7 | CONTINUOUS | 0.25 | General notes and specifications |
| `NORTH_ARROW` | 7 | CONTINUOUS | 0.25 | North arrow symbol (site/plan drawings) |
| `SCALE_BAR` | 7 | CONTINUOUS | 0.25 | Graphic scale bar |
| `REVISION_TABLE` | 7 | CONTINUOUS | 0.25 | Revision history block |

---

## Structural Layers (`STR-*`)

### Foundations

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `STR-FND-OUTLINE` | 7 | CONTINUOUS | 0.50 | Footing/foundation outer boundary |
| `STR-FND-COLUMN` | 7 | CONTINUOUS | 0.50 | Column section on foundation plan |
| `STR-FND-REBAR-BOT` | 1 | CONTINUOUS | 0.35 | Bottom reinforcement bars |
| `STR-FND-REBAR-TOP` | 1 | CONTINUOUS | 0.35 | Top reinforcement bars (if any) |
| `STR-FND-REBAR-STIRRUP` | 1 | CONTINUOUS | 0.25 | Stirrups / transverse reinforcement |
| `STR-FND-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions |
| `STR-FND-HATCH` | 6 | CONTINUOUS | 0.18 | Concrete hatch pattern |
| `STR-FND-SOIL` | 8 | DASHED | 0.18 | Soil boundary / ground line |
| `STR-FND-CENTERLINE` | 251 | CENTER | 0.18 | Centerlines |
| `STR-FND-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Rebar callouts, cover notes |

### Columns

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `STR-COL-OUTLINE` | 7 | CONTINUOUS | 0.50 | Column section outline |
| `STR-COL-REBAR-MAIN` | 1 | CONTINUOUS | 0.35 | Main longitudinal bars |
| `STR-COL-REBAR-TIES` | 1 | CONTINUOUS | 0.25 | Ties / spirals |
| `STR-COL-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions |
| `STR-COL-HATCH` | 6 | CONTINUOUS | 0.18 | Concrete hatch |
| `STR-COL-CENTERLINE` | 251 | CENTER | 0.18 | Centerlines |
| `STR-COL-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Bar schedule callouts |

### Beams

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `STR-BM-OUTLINE` | 7 | CONTINUOUS | 0.50 | Beam section / elevation outline |
| `STR-BM-REBAR-TOP` | 1 | CONTINUOUS | 0.35 | Top (negative moment) bars |
| `STR-BM-REBAR-BOT` | 1 | CONTINUOUS | 0.35 | Bottom (positive moment) bars |
| `STR-BM-REBAR-STIRRUP` | 1 | CONTINUOUS | 0.25 | Stirrups |
| `STR-BM-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions |
| `STR-BM-HATCH` | 6 | CONTINUOUS | 0.18 | Concrete hatch |
| `STR-BM-CENTERLINE` | 251 | CENTER | 0.18 | Beam axis / centerline |
| `STR-BM-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Span annotations, support notes |

### Slabs

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `STR-SLB-OUTLINE` | 7 | CONTINUOUS | 0.50 | Slab boundary |
| `STR-SLB-REBAR-TOP` | 1 | CONTINUOUS | 0.35 | Top mesh / bars |
| `STR-SLB-REBAR-BOT` | 1 | CONTINUOUS | 0.35 | Bottom mesh / bars |
| `STR-SLB-OPENINGS` | 5 | CONTINUOUS | 0.35 | Slab openings / voids |
| `STR-SLB-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions |
| `STR-SLB-HATCH` | 6 | CONTINUOUS | 0.18 | Concrete hatch |
| `STR-SLB-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Thickness, span direction notes |

### Walls

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `STR-WL-OUTLINE` | 7 | CONTINUOUS | 0.50 | Wall section outline |
| `STR-WL-REBAR-VERT` | 1 | CONTINUOUS | 0.35 | Vertical reinforcement |
| `STR-WL-REBAR-HORIZ` | 1 | CONTINUOUS | 0.35 | Horizontal reinforcement |
| `STR-WL-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions |
| `STR-WL-HATCH` | 6 | CONTINUOUS | 0.18 | Concrete hatch |
| `STR-WL-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Annotations |

### Stairs

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `STR-STAIR-OUTLINE` | 7 | CONTINUOUS | 0.50 | Stair slab and landing outline |
| `STR-STAIR-REBAR` | 1 | CONTINUOUS | 0.35 | All reinforcement |
| `STR-STAIR-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions |
| `STR-STAIR-HATCH` | 6 | CONTINUOUS | 0.18 | Concrete hatch |
| `STR-STAIR-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Riser/tread callouts |

---

## Architectural Layers (`ARC-*`)

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `ARC-WALL-EXT` | 7 | CONTINUOUS | 0.50 | External walls |
| `ARC-WALL-INT` | 7 | CONTINUOUS | 0.35 | Internal partition walls |
| `ARC-WALL-EXIST` | 4 | CONTINUOUS | 0.25 | Existing walls (renovation) |
| `ARC-WALL-DEMO` | 1 | DASHED | 0.25 | Walls to be demolished |
| `ARC-DOOR` | 7 | CONTINUOUS | 0.35 | Door symbols and swings |
| `ARC-WINDOW` | 7 | CONTINUOUS | 0.35 | Window symbols |
| `ARC-STAIR` | 7 | CONTINUOUS | 0.35 | Stair symbols in plan |
| `ARC-COLUMN` | 7 | CONTINUOUS | 0.50 | Column outlines in floor plan |
| `ARC-HATCH-WALL` | 6 | CONTINUOUS | 0.18 | Wall hatch (cut section) |
| `ARC-HATCH-FLOOR` | 6 | CONTINUOUS | 0.18 | Floor finish hatch |
| `ARC-DIMS` | 2 | CONTINUOUS | 0.25 | All dimensions |
| `ARC-ROOM-LABELS` | 7 | CONTINUOUS | 0.25 | Room names and areas |
| `ARC-AREA-BOUNDARY` | 3 | CENTER | 0.18 | Room area boundary lines |
| `ARC-FURNITURE` | 8 | CONTINUOUS | 0.13 | Furniture (optional) |
| `ARC-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | General annotations |
| `ARC-SECTION-MARK` | 5 | CONTINUOUS | 0.35 | Section cut marks and arrows |
| `ARC-ELEVATION-MARK` | 5 | CONTINUOUS | 0.35 | Elevation view marks |

---

## Civil / Site Layers (`CIV-*`)

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `CIV-BOUNDARY` | 7 | CONTINUOUS | 0.50 | Site/plot boundary |
| `CIV-SETBACK` | 3 | DASHED | 0.25 | Building setback lines |
| `CIV-ROAD` | 7 | CONTINUOUS | 0.35 | Road edges and centerlines |
| `CIV-ROAD-CENTER` | 251 | CENTER | 0.18 | Road centerlines |
| `CIV-CONTOUR-MAJOR` | 2 | CONTINUOUS | 0.35 | Major contour lines |
| `CIV-CONTOUR-MINOR` | 8 | CONTINUOUS | 0.18 | Minor contour lines |
| `CIV-DRAINAGE` | 4 | CONTINUOUS | 0.35 | Drainage channels and pipes |
| `CIV-RETAINING-WALL` | 7 | CONTINUOUS | 0.50 | Retaining wall outline |
| `CIV-PARKING` | 8 | CONTINUOUS | 0.18 | Parking stall lines |
| `CIV-LANDSCAPE` | 3 | CONTINUOUS | 0.18 | Landscape and planting areas |
| `CIV-EASEMENT` | 6 | DASHED | 0.18 | Utility easements |
| `CIV-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions and chainage |
| `CIV-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Spot levels, coordinates, labels |
| `CIV-EXISTING` | 4 | DASHED | 0.18 | Existing site features |
| `CIV-PROPOSED` | 7 | PHANTOM | 0.25 | Proposed new elements |

---

## MEP Layers (`MEP-*`)

### Plumbing / Water Supply

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `MEP-PLB-SUPPLY-COLD` | 5 | CONTINUOUS | 0.35 | Cold water supply pipes |
| `MEP-PLB-SUPPLY-HOT` | 1 | CONTINUOUS | 0.35 | Hot water supply pipes |
| `MEP-PLB-DRAIN` | 30 | DASHED | 0.35 | Drainage and waste pipes |
| `MEP-PLB-FIXTURES` | 7 | CONTINUOUS | 0.25 | Sanitary fixtures (WC, basin, etc.) |
| `MEP-PLB-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions and pipe sizes |
| `MEP-PLB-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Pipe labels, invert levels |

### HVAC

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `MEP-HVAC-SUPPLY` | 150 | CONTINUOUS | 0.35 | Supply air ducts |
| `MEP-HVAC-RETURN` | 4 | DASHED | 0.35 | Return air ducts |
| `MEP-HVAC-EQUIP` | 7 | CONTINUOUS | 0.25 | HVAC equipment |
| `MEP-HVAC-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions and duct sizes |
| `MEP-HVAC-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Labels, CFM values |

### Electrical

| Layer Name | ACI Color | Linetype | Lineweight | Purpose |
|---|---|---|---|---|
| `MEP-ELEC-CONDUIT` | 140 | CONTINUOUS | 0.35 | Electrical conduit runs |
| `MEP-ELEC-PANEL` | 7 | CONTINUOUS | 0.50 | Panels and distribution boards |
| `MEP-ELEC-OUTLETS` | 7 | CONTINUOUS | 0.25 | Outlets, switches, fixtures |
| `MEP-ELEC-LIGHTING` | 2 | CONTINUOUS | 0.25 | Lighting layout |
| `MEP-ELEC-DIMS` | 2 | CONTINUOUS | 0.25 | Dimensions and circuit numbers |
| `MEP-ELEC-ANNOTATIONS` | 7 | CONTINUOUS | 0.25 | Circuit labels, load notes |

---

## Layer Set per Drawing Type

The Drawing Engine selects the correct layer set based on `intent.element_type`.

| Element Type | Required Layer Groups |
|---|---|
| `isolated_footing` | Shared + `STR-FND-*` |
| `strip_footing` | Shared + `STR-FND-*` |
| `combined_footing` | Shared + `STR-FND-*` |
| `grade_beam` | Shared + `STR-FND-*` + `STR-BM-*` |
| `column` | Shared + `STR-COL-*` |
| `beam` | Shared + `STR-BM-*` |
| `slab` | Shared + `STR-SLB-*` |
| `shear_wall` | Shared + `STR-WL-*` |
| `stair` | Shared + `STR-STAIR-*` |
| `floor_plan` | Shared + `ARC-*` + `STR-COL-OUTLINE` |
| `site_layout` | Shared + `CIV-*` |
| `mep_routing` | Shared + `MEP-PLB-*` or `MEP-HVAC-*` or `MEP-ELEC-*` |

---

## DXF File Invariants

The Drawing Engine must enforce all of the following before emitting a DXF:

1. **All mandatory shared layers exist** in the file's layer table, even if empty.
2. **All discipline-specific layers for the element_type** exist in the layer table.
3. **No layer name contains spaces** — use underscores only.
4. **No geometry placed on layer `0`** — layer `0` is reserved for DXF blocks only.
5. **No geometry placed on `DEFPOINTS`** — this layer is reserved by AutoCAD.
6. **All dimension entities are on a `*-DIMS` layer** — never on an outline layer.
7. **All text annotations are on a `*-ANNOTATIONS` or `*-LABELS` or `TITLE_TEXT` layer**.
8. **Rebar entities are on a `*-REBAR-*` layer** — never on an outline layer.
9. **Hatch entities are on a `*-HATCH` layer**.
10. **Title block geometry is on `TITLE_BLOCK`; title block text is on `TITLE_TEXT`**.
11. **`BORDER` layer contains only the drawing sheet boundary rectangle**.
12. **Layer colors must match the ACI values defined in this standard** — no arbitrary color overrides.

---

## Title Block Mandatory Fields

Every DXF must include a title block on layers `TITLE_BLOCK` and `TITLE_TEXT` with these fields:

| Field | Source |
|---|---|
| Project Name | `request.metadata.project_name` or `"—"` |
| Drawing Title | Derived from `intent.element_type` + element ID |
| Drawing Number | Auto-generated: `{discipline}-{element_type}-{request_id}` |
| Scale | From `drawing.scale` in result |
| Date | ISO 8601 date of generation |
| Revision | `request.metadata.revision` or `"R0"` |
| Engineer | `request.metadata.engineer` or `"—"` |
| ADAM Version | `result.metadata.engine_version` |

---

## Dimension Style Standard

| Property | Value |
|---|---|
| Arrow type | Architectural tick (oblique 45°) |
| Text height | 2.5 mm at plot scale |
| Dimension line spacing | 8 mm at plot scale |
| Extension line offset | 1.5 mm |
| Text position | Above dimension line, centered |
| Units | Match `request.units.length` |
| Precision | 0 decimal places for mm; 2 for m |

---

## Text Style Standard

| Style Name | Font | Height | Use |
|---|---|---|---|
| `ADAM_STANDARD` | `isocp.shx` or `Arial` | 2.5 mm | General annotations, dimensions |
| `ADAM_TITLE` | `isocp.shx` or `Arial Bold` | 5.0 mm | Drawing title in title block |
| `ADAM_NOTES` | `isocp.shx` or `Arial` | 2.0 mm | General notes |
| `ADAM_REBAR` | `isocp.shx` or `Arial` | 2.5 mm | Rebar callouts (e.g. `8T16@150`) |
