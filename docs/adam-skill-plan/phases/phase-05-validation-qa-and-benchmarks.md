# Phase 05 — Validation, QA, and Benchmarks

## الهدف
منع الأخطاء الصامتة وضبط الجودة قبل الإطلاق.

## Tasks

### P5-T01 — Schema Validation Suite
- اختبارات parsing, required fields, enum constraints, unit normalization.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T02, P1-T03, P2-T02

### P5-T02 — Engineering Golden Tests
- baseline expected outputs لحالات Structural ثم Architectural/Civil.
- **الأولوية:** Critical
- **الاعتماديات:** P3-T05, P4-T01, P4-T03

### P5-T03 — Drawing Consistency Validator
- التحقق من layers, missing dimensions, text height, entity count sanity, empty drawings.
- **الأولوية:** High
- **الاعتماديات:** P2-T06, P1-T05

### P5-T04 — MCP Failure Recovery Tests
- simulate timeout, malformed payload, save failure, invalid file.
- **الأولوية:** High
- **الاعتماديات:** P1-T06, P2-T06

### P5-T05 — Human Review Protocol
- checklist للمراجعة البشرية: correctness, clarity, constructability, notation, code alignment.
- **الأولوية:** High
- **الاعتماديات:** P3-T07

### P5-T06 — Benchmark Dashboard Spec
- تعريف مقاييس: first-pass success rate, clarification rate, design correction rate, redraw count, reviewer rejection rate.
- **الأولوية:** Medium
- **الاعتماديات:** P2-T08, P5-T05

## Exit Criteria
- كل golden tests تمر.
- failures الحرجة لها recovery paths واضحة.
- هناك baseline measurable quality numbers قبل release.
