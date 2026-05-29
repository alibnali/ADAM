# Phase 04 — Multi-Discipline Expansion Packs

## الهدف
توسيع ADAM إلى عدة تخصصات بعد إثبات النواة والـstructural baseline.

## Streams
- Stream A: Architectural Pack
- Stream B: Civil/Site Pack
- Stream C: MEP Coordination Pack

## Tasks

### P4-T01 — Architectural Pack Definition
- floor plan schema, wall/door/window abstractions, room tags, dimensions, schedules.
- **الأولوية:** High
- **الاعتماديات:** P2-T04, P2-T05, P2-T06

### P4-T02 — Architectural Drawing Rules
- wall joins, openings, symbol library, annotation scale, title conventions.
- **الأولوية:** High
- **الاعتماديات:** P4-T01, P1-T05

### P4-T03 — Civil/Site Pack Definition
- parcel boundary, setbacks, roads, drainage lines, site furniture, grading notes.
- **الأولوية:** High
- **الاعتماديات:** P2-T04, P2-T06

### P4-T04 — Civil Coordinate & Survey Policy
- northing/easting support, coordinate precision, imported references, datum assumptions.
- **الأولوية:** High
- **الاعتماديات:** P1-T04, P4-T03

### P4-T05 — MEP Coordination Pack
- basic service routing, clash-prone zones, sleeve/opening annotations, equipment blocks.
- **الأولوية:** Medium
- **الاعتماديات:** P2-T04, P2-T05, P1-T05

### P4-T06 — Shared Symbol/Block Library
- doors, windows, fixtures, equipment, north arrow, section heads, grid bubbles, title blocks.
- **الأولوية:** High
- **الاعتماديات:** P2-T05, P1-T05

### P4-T07 — Cross-Discipline Output Packaging
- تجميع outputs متعددة داخل drawing set واحد عند الحاجة.
- **الأولوية:** Medium
- **الاعتماديات:** P4-T01, P4-T03, P4-T05, P4-T06

## Exit Criteria
- وجود Pack إضافي واحد على الأقل بجودة شبه إنتاجية بعد Structural.
- symbol library مشتركة ومستخدمة فعلياً.
- لا يوجد duplication واضح في macros أو layer policies.
