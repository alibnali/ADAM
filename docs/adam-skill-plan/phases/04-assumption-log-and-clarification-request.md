# P1-T04 — AssumptionLog & ClarificationRequest Contracts

## Status
- Phase: Phase 01 — Foundation and Contracts
- Task: P1-T04
- Depends on: P1-T03 ✅
- Next: P1-T05 — Drawing Layer Standard

---

## Purpose

P1-T02 and P1-T03 embedded `assumption_log` and `clarification_questions` as inline arrays. This task promotes them to **first-class reusable contracts** with their own schemas, builder rules, and merge logic.

Every domain engine uses these contracts directly. No engine invents its own assumption format or clarification shape.

### Why separate contracts?

- Multiple engines (structural, civil, MEP) run in the same request pipeline. Each engine appends its own assumptions. The final result merges all assumption logs into one ordered list.
- ClarificationRequests from multiple engines must be deduplicated and prioritized before being presented to the engineer.
- Both contracts need consistent `source` tagging so the orchestrator knows which engine produced which entry.

---

## Contract 1 — AssumptionEntry

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "https://github.com/alibnali/ADAM/schemas/AssumptionEntry.json",
  "title": "AssumptionEntry",
  "description": "A single assumption made by ADAM during processing.",
  "type": "object",
  "required": ["entry_id", "field", "assumed_value", "reason", "source", "confidence"],
  "additionalProperties": false,
  "properties": {
    "entry_id": {
      "type": "string",
      "description": "Unique identifier for this assumption entry within the result.",
      "pattern": "^ASM-[A-Z0-9_-]+$"
    },
    "field": {
      "type": "string",
      "description": "The input field that was assumed. Must match a key in CanonicalDesignRequest.inputs or a known derived field."
    },
    "assumed_value": {
      "description": "The value ADAM used. May be any type matching the field's expected type."
    },
    "original_value": {
      "description": "The value provided by the engineer, if any. Present when ADAM overrides or adjusts a provided value."
    },
    "reason": {
      "type": "string",
      "description": "Human-readable explanation of why this assumption was made."
    },
    "code_reference": {
      "type": "string",
      "description": "Design code clause, table, or section that supports this assumption.",
      "examples": ["ACI 318-19 Table 20.6.1.3", "EC2 cl. 4.4.1"]
    },
    "source": {
      "type": "string",
      "description": "Which engine or module made this assumption.",
      "enum": ["input_normalizer", "structural_engine", "civil_engine", "mep_engine", "drawing_engine", "report_engine", "orchestrator"]
    },
    "confidence": {
      "type": "string",
      "description": "Confidence level of this assumption.",
      "enum": ["high", "medium", "low"]
    },
    "requires_engineer_confirmation": {
      "type": "boolean",
      "description": "If true, ADAM recommends the engineer explicitly confirms this assumption before using the result in construction documents.",
      "default": false
    }
  }
}
```

### TypeScript Interface

```typescript
export type AssumptionSource =
  | 'input_normalizer'
  | 'structural_engine'
  | 'civil_engine'
  | 'mep_engine'
  | 'drawing_engine'
  | 'report_engine'
  | 'orchestrator';

export type AssumptionConfidence = 'high' | 'medium' | 'low';

export interface AssumptionEntry {
  entry_id:                       string;               // REQUIRED — e.g. 'ASM-COVER-001'
  field:                          string;               // REQUIRED — matched to inputs key
  assumed_value:                  unknown;              // REQUIRED
  original_value?:                unknown;              // present if overriding engineer's value
  reason:                         string;               // REQUIRED
  code_reference?:                string;
  source:                         AssumptionSource;     // REQUIRED
  confidence:                     AssumptionConfidence; // REQUIRED
  requires_engineer_confirmation?: boolean;             // default: false
}
```

### Confidence Levels

| Level | Meaning | Example |
|---|---|---|
| `high` | Standard code default, universally accepted | Cover = 50mm per ACI 318-19 Table 20.6.1.3 for footing |
| `medium` | Common practice, but varies by region or project | Soil unit weight = 18 kN/m³ |
| `low` | ADAM had to guess due to ambiguous or absent data | Column load estimated from column size |

**Rule:** Any `confidence: "low"` entry automatically sets `requires_engineer_confirmation: true`.

---

## Contract 2 — ClarificationQuestion

### JSON Schema

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "https://github.com/alibnali/ADAM/schemas/ClarificationQuestion.json",
  "title": "ClarificationQuestion",
  "description": "A structured question ADAM asks the engineer when a mandatory input is missing or ambiguous.",
  "type": "object",
  "required": ["question_id", "field", "question", "source", "priority", "blocking"],
  "additionalProperties": false,
  "properties": {
    "question_id": {
      "type": "string",
      "description": "Unique identifier for this question.",
      "pattern": "^CLQ-[A-Z0-9_-]+$"
    },
    "field": {
      "type": "string",
      "description": "The input field this question is about."
    },
    "question": {
      "type": "string",
      "description": "The question text presented to the engineer. Must be specific and actionable."
    },
    "hint": {
      "type": "string",
      "description": "Optional guidance on where to find this value or what it means."
    },
    "example": {
      "description": "A representative example value to help the engineer understand the expected input."
    },
    "expected_type": {
      "type": "string",
      "description": "The expected data type of the answer.",
      "enum": ["number", "string", "boolean", "object", "array"]
    },
    "expected_unit": {
      "type": "string",
      "description": "The unit expected for the answer, consistent with CanonicalDesignRequest.units."
    },
    "valid_range": {
      "type": "object",
      "description": "Optional numeric range for validation of the answer.",
      "properties": {
        "min": { "type": "number" },
        "max": { "type": "number" }
      }
    },
    "source": {
      "type": "string",
      "description": "Which engine raised this question.",
      "enum": ["input_normalizer", "structural_engine", "civil_engine", "mep_engine", "drawing_engine", "orchestrator"]
    },
    "priority": {
      "type": "integer",
      "description": "Display order. Lower number = higher priority. Questions are presented in ascending priority order.",
      "minimum": 1
    },
    "blocking": {
      "type": "boolean",
      "description": "If true, ADAM cannot produce ANY output until this question is answered. If false, ADAM may produce partial outputs."
    },
    "depends_on": {
      "type": "array",
      "description": "List of question_ids that must be answered before this question becomes relevant.",
      "items": { "type": "string" }
    }
  }
}
```

### TypeScript Interface

```typescript
export type QuestionSource =
  | 'input_normalizer'
  | 'structural_engine'
  | 'civil_engine'
  | 'mep_engine'
  | 'drawing_engine'
  | 'orchestrator';

export interface ValidRange {
  min?: number;
  max?: number;
}

export interface ClarificationQuestion {
  question_id:    string;          // REQUIRED — e.g. 'CLQ-LOAD-001'
  field:          string;          // REQUIRED
  question:       string;          // REQUIRED
  hint?:          string;
  example?:       unknown;
  expected_type?: 'number' | 'string' | 'boolean' | 'object' | 'array';
  expected_unit?: string;
  valid_range?:   ValidRange;
  source:         QuestionSource;  // REQUIRED
  priority:       number;          // REQUIRED — 1 = highest
  blocking:       boolean;         // REQUIRED
  depends_on?:    string[];        // question_ids that must come first
}
```

---

## Contract 3 — AssumptionLog (Collection)

The `AssumptionLog` is an ordered array of `AssumptionEntry` objects attached to every `CanonicalDesignResult`.

### Builder Rules

1. **Each engine appends to its own log segment.** The orchestrator merges all segments in pipeline order: `input_normalizer` → `structural_engine` → `drawing_engine` → `report_engine`.
2. **entry_id must be unique** within the merged log. Format: `ASM-{SOURCE_ABBREV}-{SEQ}` e.g. `ASM-NORM-001`, `ASM-STR-003`.
3. **No duplicate fields** from the same source. If two entries from the same source cover the same field, merge them into one.
4. **Cross-engine deduplication:** If two different engines assume the same field with the same value, keep the first occurrence and discard the duplicate.
5. **Conflicting cross-engine assumptions** (same field, different values) must be escalated as an error — not silently merged.
6. **Low-confidence entries** automatically set `requires_engineer_confirmation: true`. This flag triggers a confirmation section in the report.

### Merge Order Example

```
Input Normalizer Log:   [ASM-NORM-001, ASM-NORM-002]
Structural Engine Log:  [ASM-STR-001, ASM-STR-002, ASM-STR-003]
Drawing Engine Log:     [ASM-DRW-001]

Merged AssumptionLog:   [
  ASM-NORM-001,
  ASM-NORM-002,
  ASM-STR-001,
  ASM-STR-002,
  ASM-STR-003,
  ASM-DRW-001
]
```

---

## Contract 4 — ClarificationRequest (Collection)

A `ClarificationRequest` is a structured collection of `ClarificationQuestion` objects emitted when the orchestrator or any engine detects missing mandatory inputs.

### Builder Rules

1. **Blocking questions are always presented first**, regardless of `priority` value.
2. **Non-blocking questions** are presented after all blocking questions, sorted by `priority` ascending.
3. **Dependent questions** (`depends_on`) are hidden until their dependencies are answered.
4. **Deduplication:** If two engines raise a question about the same `field`, keep the one with lower `priority` (higher importance). Discard the duplicate.
5. **A `ClarificationRequest` blocks ALL domain logic** if any question has `blocking: true`. Partial processing only proceeds if all remaining questions are non-blocking.

### Presentation Order Algorithm

```
Step 1: Collect all questions from all engines.
Step 2: Deduplicate by field — keep lowest priority number per field.
Step 3: Partition: blocking_questions = [q where q.blocking == true]
                   optional_questions = [q where q.blocking == false]
Step 4: Sort blocking_questions by priority ascending.
Step 5: Sort optional_questions by priority ascending.
Step 6: Filter out questions whose depends_on fields are not yet answered.
Step 7: Present: blocking_questions + optional_questions.
```

---

## Full Example — Isolated Footing with Assumptions

### AssumptionLog (merged)

```json
[
  {
    "entry_id": "ASM-NORM-001",
    "field": "units.area",
    "assumed_value": "mm2",
    "reason": "units.area omitted — inferred from units.length = 'm' → 'mm2' per ADAM inference rule IR-UNIT-01",
    "source": "input_normalizer",
    "confidence": "high",
    "requires_engineer_confirmation": false
  },
  {
    "entry_id": "ASM-STR-001",
    "field": "cover_mm",
    "assumed_value": 50,
    "reason": "Minimum cover for footings in contact with soil per ACI 318-19 Table 20.6.1.3",
    "code_reference": "ACI 318-19 Table 20.6.1.3",
    "source": "structural_engine",
    "confidence": "high",
    "requires_engineer_confirmation": false
  },
  {
    "entry_id": "ASM-STR-002",
    "field": "soil_unit_weight_kNm3",
    "assumed_value": 18,
    "original_value": 18,
    "reason": "Value provided by engineer — no assumption made",
    "source": "structural_engine",
    "confidence": "high",
    "requires_engineer_confirmation": false
  },
  {
    "entry_id": "ASM-STR-003",
    "field": "phi_flexure",
    "assumed_value": 0.90,
    "reason": "Strength reduction factor for flexure (tension-controlled) per ACI 318-19",
    "code_reference": "ACI 318-19 Table 21.2.1",
    "source": "structural_engine",
    "confidence": "high",
    "requires_engineer_confirmation": false
  },
  {
    "entry_id": "ASM-DRW-001",
    "field": "drawing.scale",
    "assumed_value": "1:50",
    "reason": "Default scale for isolated footing plan and section drawings",
    "source": "drawing_engine",
    "confidence": "high",
    "requires_engineer_confirmation": false
  }
]
```

### ClarificationRequest (for incomplete request)

```json
[
  {
    "question_id": "CLQ-LOAD-001",
    "field": "column_load_kN",
    "question": "ما هو الحمل الرأسي على العمود (P) بالكيلو نيوتن؟",
    "hint": "يشمل الأحمال الميتة والحية المحسوبة على العمود من جميع الطوابق",
    "example": 800,
    "expected_type": "number",
    "expected_unit": "kN",
    "valid_range": { "min": 10, "max": 50000 },
    "source": "input_normalizer",
    "priority": 1,
    "blocking": true
  },
  {
    "question_id": "CLQ-SOIL-001",
    "field": "bearing_capacity_kPa",
    "question": "ما هي قدرة تحمل التربة المسموح بها (qa) بالكيلو باسكال؟",
    "hint": "من تقرير التربة أو التقدير الميداني",
    "example": 150,
    "expected_type": "number",
    "expected_unit": "kPa",
    "valid_range": { "min": 50, "max": 1000 },
    "source": "input_normalizer",
    "priority": 2,
    "blocking": true
  },
  {
    "question_id": "CLQ-DEPTH-001",
    "field": "foundation_depth_m",
    "question": "ما هو عمق التأسيس المطلوب (Df) بالمتر؟",
    "example": 1.5,
    "expected_type": "number",
    "expected_unit": "m",
    "valid_range": { "min": 0.5, "max": 10 },
    "source": "input_normalizer",
    "priority": 3,
    "blocking": true
  },
  {
    "question_id": "CLQ-CONC-001",
    "field": "concrete_fck_MPa",
    "question": "ما هي مقاومة الخرسانة المحددة (f'c أو fck) بالميغاباسكال؟",
    "hint": "مثال: 25 MPa = C25",
    "example": 25,
    "expected_type": "number",
    "expected_unit": "MPa",
    "valid_range": { "min": 17, "max": 100 },
    "source": "input_normalizer",
    "priority": 4,
    "blocking": true
  },
  {
    "question_id": "CLQ-STEEL-001",
    "field": "steel_fyk_MPa",
    "question": "ما هو حد الخضوع للحديد (fy أو fyk) بالميغاباسكال؟",
    "hint": "مثال: 420 MPa = Grade 60",
    "example": 420,
    "expected_type": "number",
    "expected_unit": "MPa",
    "valid_range": { "min": 200, "max": 700 },
    "source": "input_normalizer",
    "priority": 5,
    "blocking": true
  }
]
```

---

## Invariants

### AssumptionLog Invariants
- `assumption_log` is **always present** in every `CanonicalDesignResult` — even as `[]`.
- `entry_id` values must be unique within the merged log.
- `confidence: "low"` → `requires_engineer_confirmation: true` (enforced by builder, not optional).
- Conflicting assumptions (same field, different values, different sources) → escalate as error `ASSUMPTION-CONFLICT`.

### ClarificationRequest Invariants  
- If any `blocking: true` question exists → `status: "clarification_required"` and no domain logic runs.
- `question_id` values must be unique within the collection.
- `depends_on` references must point to valid `question_id` values within the same collection.
- Questions about the same `field` from different engines are deduplicated — lower `priority` wins.
- An empty `ClarificationRequest` (`[]`) means all mandatory inputs are present and processing may proceed.

---

## Error Codes Introduced

| Code | Severity | Trigger |
|---|---|---|
| `ASSUMPTION-CONFLICT` | error | Two engines assumed same field with different values |
| `CLQ-DUPLICATE-FIELD` | warning | Two engines raised questions about same field (auto-resolved by dedup) |
| `CLQ-INVALID-DEPENDS` | error | `depends_on` references a non-existent `question_id` |
| `ASM-INVALID-ID` | error | `entry_id` does not match `^ASM-[A-Z0-9_-]+$` pattern |
