# P2-T05 — OutputRenderer Contract

## Purpose

Final stage of the ADAM kernel. Converts a `DrawingDocument` to one or more output formats. The **only** component that knows about target formats.

---

## Supported Output Formats v0.1

| Format | MIME Type | Notes |
|---|---|---|
| `svg` | `image/svg+xml` | Primary. Browser-renderable. Inline in AI response. |
| `dxf` | `application/dxf` | AutoCAD DXF R2010 (AC1024). |
| `pdf` | `application/pdf` | ISO A-series, embedded fonts. |
| `json` | `application/json` | Raw DrawingDocument — debug/serialization. |
| `png` | `image/png` | Raster 150/300 DPI. |

Default: `svg`.

---

## Input / Output Contract

```
Input:   DrawingDocument  +  OutputRendererConfig
Output:  RenderedOutput
```

### OutputRendererConfig

```json
{
  "formats":        ["svg", "dxf"],
  "scale_factor":   1.0,
  "dpi":            150,
  "embed_metadata": true,
  "line_quality":   "precise"
}
```

---

## SVG Renderer Rules

| Rule | Detail |
|---|---|
| ViewBox | `viewBox="0 0 {W_mm} {H_mm}"` in mm at 1:1 |
| Units | mm as SVG user units |
| Layers | `<g id="{layer}" class="adam-layer">` |
| Colors | ACI color → hex. BYLAYER resolved before render. |
| Linetypes | Hidden: `stroke-dasharray="5 3"`. Center: `stroke-dasharray="15 3 3 3"`. |
| Text | `<text font-family="Arial,sans-serif">` |
| Rebar (section) | Filled `<circle>` black |
| Rebar (elevation) | Two parallel `<line>` |
| Hatch | `<pattern>` ANSI31 = 45° lines 2mm spacing |
| Title block | `<rect>` + `<text>` fields, bottom-right |

---

## DXF R2010 Renderer Rules

| Primitive | DXF Entity |
|---|---|
| `line` | `LINE` |
| `polyline` | `LWPOLYLINE` |
| `rect` | `LWPOLYLINE` closed 4 vertices |
| `circle` | `CIRCLE` |
| `arc` | `ARC` |
| `text` | `MTEXT` |
| `dim_linear` | `DIMENSION` type=0 |
| `hatch` | `HATCH` with boundary loop |
| `rebar` section | `CIRCLE` + solid `HATCH` |
| Units | `$INSUNITS = 4` (mm) |

---

## Error Codes

| Code | Severity | Trigger |
|---|---|---|
| `RENDER-FORMAT-UNSUPPORTED` | error | Format not in supported list |
| `RENDER-PRIMITIVE-UNKNOWN` | error | Primitive type has no renderer |
| `RENDER-LAYER-UNRESOLVED` | warning | Layer not found → fallback layer `0` |
| `RENDER-TEXT-OVERFLOW` | warning | Text longer than dimension line |
| `RENDER-SCALE-OVERFLOW` | error | Content exceeds sheet bounds |
