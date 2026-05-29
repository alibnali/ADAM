# P1-T03 — Canonical Result Schema

## Status
- Phase: Phase 01 — Foundation and Contracts
- Task: P1-T03
- Depends on: P1-T02 ✅
- Next: P1-T04 — AssumptionLog and ClarificationRequest contracts

---

## Purpose

`CanonicalDesignResult` is the single unified output contract that ADAM returns after processing any `CanonicalDesignRequest`. Every domain engine — structural, architectural, civil, MEP — must produce this object as its final output. No domain engine may return a custom payload directly to the client.

This contract enforces:
- A consistent response shape regardless of discipline or element type.
- Clear separation between computed values, drawing artifacts, reports, and logs.
- Explicit status codes so the client always knows exactly what happened.
- A structured assumption log attached to every result.
- A structured error list when processing fails or is partial.

---

## JSON Schema (draft-07)

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "https://github.com/alibnali/ADAM/schemas/CanonicalDesignResult.json",
  "title": "CanonicalDesignResult",
  "description": "Unified output contract for all ADAM engineering results.",
  "type": "object",
  "required": [
    "request_id",
    "schema_version",
    "status",
    "discipline",
    "element_type",
    "assumption_log",
    "validation_log",
    "errors"
  ],
  "additionalProperties": false,
  "properties": {

    "request_id": {
      "type": "string",
      "description": "Echoes the request_id from the originating CanonicalDesignRequest."
    },

    "schema_version": {
      "type": "string",
      "enum": ["1.0"]
    },

    "status": {
      "type": "string",
      "description": "Top-level result status.",
      "enum": [
        "success",
        "success_with_warnings",
        "clarification_required",
        "partial",
        "failed"
      ]
    },

    "discipline": {
      "type": "string",
      "enum": ["structural", "architectural", "civil", "mep", "unknown"]
    },

    "element_type": {
      "type": "string",
      "description": "Echoes intent.element_type from the originating request."
    },

    "computed": {
      "type": "object",
      "description": "Domain-specific computed values. Structure varies by element_type. Present only when status is success or success_with_warnings.",
      "additionalProperties": true
    },

    "drawing": {
      "type": "object",
      "description": "Drawing artifact descriptor. Present only when required_outputs.drawing was true and drawing was produced.",
      "required": ["format", "content_base64"],
      "additionalProperties": false,
      "properties": {
        "format": {
          "type": "string",
          "enum": ["dxf", "svg", "pdf"],
          "description": "Drawing file format."
        },
        "content_base64": {
          "type": "string",
          "description": "Base64-encoded drawing file content."
        },
        "filename": {
          "type": "string",
          "description": "Suggested filename including extension."
        },
        "sheet_title": {
          "type": "string"
        },
        "scale": {
          "type": "string",
          "description": "Drawing scale, e.g. '1:50'."
        },
        "layers": {
          "type": "array",
          "description": "List of layer names present in the drawing.",
          "items": { "type": "string" }
        }
      }
    },

    "report": {
      "type": "object",
      "description": "Technical report descriptor. Present only when required_outputs.report was true.",
      "required": ["format", "content"],
      "additionalProperties": false,
      "properties": {
        "format": {
          "type": "string",
          "enum": ["markdown", "html", "pdf"],
          "description": "Report format."
        },
        "content": {
          "type": "string",
          "description": "Report content. Plain text (markdown/html) or base64 (pdf)."
        },
        "language": {
          "type": "string",
          "enum": ["ar", "en", "ar-en"]
        },
        "sections": {
          "type": "array",
          "description": "List of section titles in the report.",
          "items": { "type": "string" }
        }
      }
    },

    "assumption_log": {
      "type": "array",
      "description": "Every assumption ADAM made during processing. REQUIRED even if empty.",
      "items": {
        "type": "object",
        "required": ["field", "assumed_value", "reason"],
        "additionalProperties": false,
        "properties": {
          "field":          { "type": "string" },
          "assumed_value":  {},
          "reason":         { "type": "string" },
          "code_reference": { "type": "string" }
        }
      }
    },

    "validation_log": {
      "type": "array",
      "description": "Structured checks performed. Each entry is one design check. REQUIRED even if empty.",
      "items": {
        "type": "object",
        "required": ["check_id", "description", "result"],
        "additionalProperties": false,
        "properties": {
          "check_id":       { "type": "string" },
          "description":    { "type": "string" },
          "result": {
            "type": "string",
            "enum": ["pass", "fail", "warning", "skipped"]
          },
          "computed_value": {},
          "limit_value":    {},
          "unit":           { "type": "string" },
          "code_reference": { "type": "string" },
          "note":           { "type": "string" }
        }
      }
    },

    "clarification_questions": {
      "type": "array",
      "description": "Present only when status is clarification_required. Lists exactly what is missing.",
      "items": {
        "type": "object",
        "required": ["field", "question"],
        "additionalProperties": false,
        "properties": {
          "field":        { "type": "string" },
          "question":     { "type": "string" },
          "hint":         { "type": "string" },
          "example":      {}
        }
      }
    },

    "errors": {
      "type": "array",
      "description": "Structured error list. REQUIRED even if empty.",
      "items": {
        "type": "object",
        "required": ["code", "message"],
        "additionalProperties": false,
        "properties": {
          "code":    { "type": "string" },
          "message": { "type": "string" },
          "field":   { "type": "string" },
          "severity": {
            "type": "string",
            "enum": ["error", "warning", "info"]
          }
        }
      }
    },

    "metadata": {
      "type": "object",
      "additionalProperties": true,
      "properties": {
        "processed_at":    { "type": "string", "format": "date-time" },
        "engine_version":  { "type": "string" },
        "processing_ms":   { "type": "number" }
      }
    }

  }
}
```

---

## TypeScript Interface

```typescript
// CanonicalDesignResult — ADAM v1.0

export type ResultStatus =
  | 'success'
  | 'success_with_warnings'
  | 'clarification_required'
  | 'partial'
  | 'failed';

export type DrawingFormat = 'dxf' | 'svg' | 'pdf';
export type ReportFormat  = 'markdown' | 'html' | 'pdf';
export type CheckResult   = 'pass' | 'fail' | 'warning' | 'skipped';
export type ErrorSeverity = 'error' | 'warning' | 'info';

export interface AssumptionEntry {
  field:           string;   // REQUIRED — which field was assumed
  assumed_value:   unknown;  // REQUIRED — what value was used
  reason:          string;   // REQUIRED — why this assumption was made
  code_reference?: string;   // optional — ACI clause, table ref, etc.
}

export interface ValidationEntry {
  check_id:        string;       // REQUIRED — e.g. 'BEARING-001'
  description:     string;       // REQUIRED — human-readable check description
  result:          CheckResult;  // REQUIRED
  computed_value?: unknown;      // actual computed value
  limit_value?:    unknown;      // code-specified limit
  unit?:           string;       // unit for computed/limit values
  code_reference?: string;       // e.g. 'ACI 318-19 §13.2.1'
  note?:           string;       // extra context
}

export interface ClarificationQuestion {
  field:    string;   // REQUIRED — missing or ambiguous field name
  question: string;   // REQUIRED — question to ask the engineer
  hint?:    string;
  example?: unknown;
}

export interface ResultError {
  code:      string;         // REQUIRED — machine-readable error code
  message:   string;         // REQUIRED — human-readable message
  field?:    string;         // field that caused the error, if applicable
  severity?: ErrorSeverity;
}

export interface DrawingArtifact {
  format:         DrawingFormat;  // REQUIRED
  content_base64: string;         // REQUIRED — base64-encoded file
  filename?:      string;
  sheet_title?:   string;
  scale?:         string;         // e.g. '1:50'
  layers?:        string[];
}

export interface ReportArtifact {
  format:    ReportFormat;  // REQUIRED
  content:   string;        // REQUIRED — markdown/html text or base64 for pdf
  language?: 'ar' | 'en' | 'ar-en';
  sections?: string[];      // list of section titles
}

export interface ResultMetadata {
  processed_at?:   string;  // ISO 8601 datetime
  engine_version?: string;
  processing_ms?:  number;
  [key: string]:   unknown;
}

export interface CanonicalDesignResult {
  request_id:               string;                   // REQUIRED — echoes request
  schema_version:           '1.0';                    // REQUIRED
  status:                   ResultStatus;             // REQUIRED
  discipline:               string;                   // REQUIRED
  element_type:             string;                   // REQUIRED
  computed?:                Record<string, unknown>;  // domain-specific output values
  drawing?:                 DrawingArtifact;          // present if drawing was requested + produced
  report?:                  ReportArtifact;           // present if report was requested
  assumption_log:           AssumptionEntry[];        // REQUIRED — empty array if none
  validation_log:           ValidationEntry[];        // REQUIRED — empty array if none
  clarification_questions?: ClarificationQuestion[]; // present only when status = clarification_required
  errors:                   ResultError[];            // REQUIRED — empty array if none
  metadata?:                ResultMetadata;
}
```

---

## Field Reference Table

| Field | Required | Notes |
|---|---|---|
| `request_id` | ✅ | Must match originating request |
| `schema_version` | ✅ | Must be `"1.0"` |
| `status` | ✅ | One of 5 values — see Status Codes |
| `discipline` | ✅ | Echoed from request |
| `element_type` | ✅ | Echoed from request |
| `computed` | ⬜ | Domain-specific. Absent on failure or clarification_required |
| `drawing` | ⬜ | Present only if drawing was requested AND produced |
| `report` | ⬜ | Present only if report was requested |
| `assumption_log` | ✅ | Always present. Empty array `[]` if no assumptions |
| `validation_log` | ✅ | Always present. Empty array `[]` if no checks ran |
| `clarification_questions` | ⬜ | Present only when `status = clarification_required` |
| `errors` | ✅ | Always present. Empty array `[]` if no errors |
| `metadata` | ⬜ | Optional processing metadata |

---

## Status Codes

| Status | Meaning | `computed` | `drawing` | `errors` |
|---|---|---|---|---|
| `success` | All checks passed, all outputs produced | ✅ present | ✅ if requested | `[]` |
| `success_with_warnings` | Outputs produced but one or more checks returned `warning` | ✅ present | ✅ if requested | warnings only |
| `clarification_required` | Mandatory inputs missing — cannot proceed | ❌ absent | ❌ absent | ❌ absent — use `clarification_questions` |
| `partial` | Some outputs produced, some failed (e.g. design ok but drawing failed) | ✅ present | ⚠️ may be absent | errors describing what failed |
| `failed` | Fatal error — no outputs produced | ❌ absent | ❌ absent | ✅ non-empty |

---

## Example 1 — Successful Isolated Footing Result

```json
{
  "request_id": "req-footing-001",
  "schema_version": "1.0",
  "status": "success",
  "discipline": "structural",
  "element_type": "isolated_footing",
  "computed": {
    "footing_size_m": { "width": 2.4, "depth": 2.4 },
    "footing_thickness_mm": 450,
    "net_bearing_pressure_kPa": 138.9,
    "punching_shear_ratio": 0.62,
    "one_way_shear_ratio": 0.71,
    "rebar_bottom_x": "10T16@220mm",
    "rebar_bottom_y": "10T16@220mm",
    "rebar_area_provided_mm2": 2011,
    "rebar_area_required_mm2": 1840
  },
  "drawing": {
    "format": "dxf",
    "content_base64": "<base64-encoded-dxf-content>",
    "filename": "isolated_footing_req-footing-001.dxf",
    "sheet_title": "قاعدة منفصلة — مشروع المبنى الإداري",
    "scale": "1:50",
    "layers": ["OUTLINE", "REBAR", "DIMENSIONS", "TITLE_BLOCK", "ANNOTATIONS"]
  },
  "report": {
    "format": "markdown",
    "language": "ar",
    "content": "# تقرير تصميم قاعدة منفصلة\n\n## البيانات المدخلة\n...",
    "sections": [
      "البيانات المدخلة",
      "تحديد أبعاد القاعدة",
      "فحص ضغط التربة",
      "فحص القص المثقبي",
      "فحص القص الأحادي",
      "تصميم التسليح",
      "ملخص النتائج"
    ]
  },
  "assumption_log": [
    {
      "field": "soil_unit_weight_kNm3",
      "assumed_value": 18,
      "reason": "قيمة مقدمة من المستخدم — لم يتم افتراضها"
    }
  ],
  "validation_log": [
    {
      "check_id": "BEARING-001",
      "description": "فحص ضغط التربة الصافي",
      "result": "pass",
      "computed_value": 138.9,
      "limit_value": 150,
      "unit": "kPa",
      "code_reference": "ACI 318-19 §13.2"
    },
    {
      "check_id": "PUNCHING-001",
      "description": "فحص القص المثقبي حول العمود",
      "result": "pass",
      "computed_value": 0.62,
      "limit_value": 1.0,
      "unit": "ratio Vu/ϕVn",
      "code_reference": "ACI 318-19 §22.6"
    },
    {
      "check_id": "ONEWAY-001",
      "description": "فحص القص الأحادي الاتجاه",
      "result": "pass",
      "computed_value": 0.71,
      "limit_value": 1.0,
      "unit": "ratio Vu/ϕVn",
      "code_reference": "ACI 318-19 §22.5"
    },
    {
      "check_id": "FLEXURE-001",
      "description": "فحص التسليح — الحد الأدنى",
      "result": "pass",
      "computed_value": 2011,
      "limit_value": 1840,
      "unit": "mm2",
      "code_reference": "ACI 318-19 §7.6.1"
    }
  ],
  "errors": [],
  "metadata": {
    "processed_at": "2026-05-30T00:00:00Z",
    "engine_version": "0.1.0-alpha",
    "processing_ms": 312
  }
}
```

---

## Example 2 — Clarification Required

```json
{
  "request_id": "req-incomplete-003",
  "schema_version": "1.0",
  "status": "clarification_required",
  "discipline": "structural",
  "element_type": "isolated_footing",
  "assumption_log": [],
  "validation_log": [],
  "clarification_questions": [
    {
      "field": "column_load_kN",
      "question": "ما هو الحمل الرأسي على العمود (P) بالكيلو نيوتن؟",
      "hint": "يشمل الأحمال الميتة والحية المحسوبة على العمود",
      "example": 800
    },
    {
      "field": "bearing_capacity_kPa",
      "question": "ما هي قدرة تحمل التربة المسموح بها (qa) بالكيلو باسكال؟",
      "hint": "من تقرير التربة أو التقدير الميداني",
      "example": 150
    },
    {
      "field": "foundation_depth_m",
      "question": "ما هو عمق التأسيس المطلوب (Df) بالمتر؟",
      "example": 1.5
    },
    {
      "field": "concrete_fck_MPa",
      "question": "ما هي مقاومة الخرسانة المحددة (f'c أو fck) بالميغاباسكال؟",
      "hint": "مثال: 25 MPa = C25",
      "example": 25
    },
    {
      "field": "steel_fyk_MPa",
      "question": "ما هو حد الخضوع للحديد (fy أو fyk) بالميغاباسكال؟",
      "hint": "مثال: 420 MPa = Grade 60",
      "example": 420
    }
  ],
  "errors": []
}
```

---

## Example 3 — Failed Result

```json
{
  "request_id": "req-bad-input-099",
  "schema_version": "1.0",
  "status": "failed",
  "discipline": "structural",
  "element_type": "isolated_footing",
  "assumption_log": [],
  "validation_log": [],
  "errors": [
    {
      "code": "BEARING-OVERLOAD",
      "message": "ضغط التربة المحسوب (312 kPa) يتجاوز قدرة التحمل المسموح بها (150 kPa) بأكثر من 100%. لا يمكن إيجاد أبعاد قاعدة ضمن القيد المحدد (max_footing_side_m = 3.0 m).",
      "field": "bearing_capacity_kPa",
      "severity": "error"
    }
  ]
}
```

---

## Invariants (Non-Negotiable Rules)

- `assumption_log`, `validation_log`, and `errors` are **always present** in every result — even as empty arrays `[]`. A result without these three fields is invalid.
- `computed` is **absent** when `status` is `clarification_required` or `failed`. It must not be present with null values.
- `clarification_questions` is **only present** when `status = clarification_required`. It must not appear in success or failed results.
- `drawing` is **only present** when `required_outputs.drawing` was `true` in the request AND the drawing was successfully produced.
- `request_id` must exactly match the `request_id` from the originating `CanonicalDesignRequest`.
- `schema_version` must match the version of the schema used to validate the result.
- No domain engine may return raw computed data outside of the `computed` block.

---

## Request → Result Flow Summary

```
CanonicalDesignRequest
        │
        ▼
  Input Normalizer
        │
   ┌────┴────┐
   │ missing │  ──►  status: clarification_required
   │ inputs? │       + clarification_questions[]
   └────┬────┘
        │ all inputs present
        ▼
  Domain Engine
        │
   ┌────┴─────────────────────┐
   │  computed + checks run   │
   └────┬─────────────────────┘
        │
   ┌────┴────┐
   │ checks  │  ──►  warning  ──►  status: success_with_warnings
   │ result? │  ──►  all pass ──►  status: success
   │         │  ──►  fatal    ──►  status: failed
   └────┬────┘
        │
        ▼
  Drawing Engine (if requested)
        │
        ▼
  Report Engine (if requested)
        │
        ▼
CanonicalDesignResult
```
