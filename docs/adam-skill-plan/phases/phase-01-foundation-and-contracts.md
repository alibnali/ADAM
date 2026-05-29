# Phase 01 — Foundation and Contracts

## الهدف
تثبيت حدود المنتج، العقود، معجم الكيانات، وسياسات التشغيل. هذه المرحلة شرط سابق لكل ما بعدها.

## Tasks

### P1-T01 — Product Charter
- تعريف vision, users, job-to-be-done, in-scope, out-of-scope.
- اعتماد disciplines الأولى للإطلاق.
- تحديد KPI أساسي: accuracy, completion rate, redraw rate, validation pass rate.
- **الأولوية:** Critical
- **الاعتماديات:** لا يوجد
- **المخرج:** `phases/01-product-charter.md`
- **الحالة:** ✅ منجزة

### P1-T02 — Canonical Request Schema
- تصميم `CanonicalDesignRequest` موحد.
- تضمين: discipline, intent, units, constraints, references, required outputs.
- تعريف mandatory vs optional fields.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T01
- **المخرج:** schema draft + examples
- **الحالة:** 🔜 التالية

### P1-T03 — Canonical Result Schema
- تصميم `CanonicalDesignResult`, `DrawingInstructionSet`, `ValidationReport`.
- فصل domain result عن drawing result.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T02
- **المخرج:** result contracts
- **الحالة:** ⏳ معلقة

### P1-T04 — Units and Coordinate Policy
- توحيد mm/m/kN/MPa conventions.
- تعريف origin policy, scale policy, sheet space/model space assumptions.
- **الأولوية:** High
- **الاعتماديات:** P1-T02
- **المخرج:** unit-and-coordinate-policy.md
- **الحالة:** ⏳ معلقة

### P1-T05 — Layering & Annotation Standard
- بناء naming convention للطبقات: STR-CONC, STR-REBAR, ARCH-WALL, CIV-BOUNDARY, MEP-DUCT ...
- تحديد lineweights, colors, text heights, dimension style defaults.
- **الأولوية:** High
- **الاعتماديات:** P1-T03, P1-T04
- **المخرج:** CAD standard reference
- **الحالة:** ⏳ معلقة

### P1-T06 — Failure Mode Taxonomy
- تصنيف حالات الفشل: missing inputs, inconsistent geometry, code check failure, MCP call failure, invalid output file.
- ربط كل failure mode باستجابة تشغيلية محددة.
- **الأولوية:** High
- **الاعتماديات:** P1-T02, P1-T03
- **المخرج:** failure-mode-catalog.md
- **الحالة:** ⏳ معلقة

### P1-T07 — Prompt Governance
- تصميم قواعد ثابتة للـsystem prompt وسياسات tool-calling.
- تعريف متى يسأل النموذج، متى يوقف الرسم، متى يمرر إلى validation.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T02, P1-T03, P1-T06
- **المخرج:** prompt governance spec
- **الحالة:** ⏳ معلقة

## Exit Criteria
- العقود معتمدة ومراجعة.
- لا يوجد تضارب في الوحدات أو الطبقات.
- توجد أمثلة end-to-end لطلب واحد على الأقل في كل schema.
