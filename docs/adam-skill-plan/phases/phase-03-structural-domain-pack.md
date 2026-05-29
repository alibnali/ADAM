# Phase 03 — Structural Domain Pack (Production Baseline)

## الهدف
بناء أول Domain Pack إنتاجي كامل ليكون baseline لباقي التخصصات. الاختيار: **Foundations & Core Structural Details**.

## Tasks

### P3-T01 — Structural Scope Slice
- ترتيب الأولويات داخل structural pack:
  1. isolated footing
  2. strip footing
  3. combined footing
  4. grade beam details
  5. slab opening/detail sheets
- **الأولوية:** Critical
- **الاعتماديات:** P1-T01

### P3-T02 — Structural Input Schemas
- تصميم input contracts لكل type مع mandatory data ونقاط النقص الشائعة.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T02, P3-T01

### P3-T03 — Structural Calculation/Validation Specs
- تعريف checks المطلوبة لكل type: bearing, punching, one-way shear, flexure, detailing minima, cover, spacing.
- **الأولوية:** Critical
- **الاعتماديات:** P3-T02

### P3-T04 — Structural Drawing Specs
- plan, section, rebar notes, dimensions, callouts, schedules.
- mapping لكل نتيجة حسابية إلى عنصر رسم.
- **الأولوية:** Critical
- **الاعتماديات:** P2-T04, P2-T05, P3-T03

### P3-T05 — Footing Golden Scenario Set
- إعداد أمثلة مرجعية متنوعة: safe case, edge case, failure case, incomplete-input case.
- **الأولوية:** High
- **الاعتماديات:** P3-T03, P3-T04

### P3-T06 — Structural Reporting Templates
- Arabic + English report templates.
- جداول checks + assumptions + reinforcement summary.
- **الأولوية:** Medium
- **الاعتماديات:** P2-T07, P3-T03

### P3-T07 — Reviewer Feedback Loop
- تعريف workflow لمراجعة مهندس بشري على المخرجات baseline وتسجيل العيوب التصنيفية والهندسية والرسمية.
- **الأولوية:** High
- **الاعتماديات:** P3-T05, P3-T06

## Exit Criteria
- isolated footing يعمل end-to-end بشكل موثوق.
- توجد golden tests ناجحة على الأقل لسيناريوهات متعددة.
- output drawing وreport وvalidation log متسقة من نفس المصدر.
