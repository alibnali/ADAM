# Risk Register

## R1 — Over-expansion Before Kernel Stability
- **الوصف:** التوسع إلى عدة تخصصات قبل تثبيت النواة يؤدي إلى فوضى prompts وschemas.
- **الأثر:** High
- **الاحتمال:** High
- **المعالجة:** freeze kernel contracts before domain expansion.

## R2 — Prompt-Only Architecture
- **الوصف:** الاعتماد على البرومبت وحده سيؤدي إلى مخرجات غير متسقة وصعبة الاختبار.
- **الأثر:** High
- **الاحتمال:** High
- **المعالجة:** schema-driven contracts + validators + golden tests.

## R3 — CAD Tool Coupling
- **الوصف:** ربط الـSkill بأداة DXF معينة مباشرة سيصعّب النقل لاحقاً.
- **الأثر:** Medium
- **الاحتمال:** Medium
- **المعالجة:** canonical drawing layer + MCP adapter abstraction.

## R4 — Silent Assumptions
- **الوصف:** افتراضات غير معلنة قد تنتج رسماً صحيحاً شكلياً لكنه خاطئ هندسياً.
- **الأثر:** High
- **الاحتمال:** High
- **المعالجة:** AssumptionLog إلزامي + clarification gates.

## R5 — Engineering Validation Gaps
- **الوصف:** الرسم قد ينجح بينما المنطق الحسابي أو الأكواد غير محققة.
- **الأثر:** Critical
- **الاحتمال:** Medium
- **المعالجة:** domain validation specs + human review checklist.

## R6 — Annotation/Layer Drift
- **الوصف:** تفاوت الطبقات أو الأبعاد أو النصوص عبر الـpacks يضعف الجودة المهنية.
- **الأثر:** Medium
- **الاحتمال:** High
- **المعالجة:** shared CAD standard + drawing consistency validator.
