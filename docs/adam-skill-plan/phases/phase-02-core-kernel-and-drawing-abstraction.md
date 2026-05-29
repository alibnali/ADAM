# Phase 02 — Core Kernel and Drawing Abstraction

## الهدف
بناء النواة المشتركة التي ستُستخدم عبر كل التخصصات دون تكرار المنطق.

## Tasks

### P2-T01 — Request Classifier
- تصميم منطق تصنيف الطلب إلى discipline + deliverable type.
- إضافة confidence threshold وسياسة fallback عند الغموض.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T02, P1-T07

### P2-T02 — Input Normalizer
- تحويل اللغة الطبيعية إلى request structured object.
- كشف البيانات الناقصة وتوليد clarification questions دقيقة.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T02, P1-T06

### P2-T03 — Assumption Manager
- يمنع الافتراضات الصامتة.
- يبني `AssumptionLog` قابل للعرض في التقرير.
- **الأولوية:** High
- **الاعتماديات:** P2-T02

### P2-T04 — Drawing Primitive Library
- تعريف primitive set موحد: line, polyline, rect, circle, arc, hatch, text, leader, dim, block.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T03, P1-T05

### P2-T05 — Drawing Macro Library
- تحويل patterns المتكررة إلى macros: foundation_plan, section_marker, grid_bubble, room_tag, rebar_mat, title_block.
- **الأولوية:** High
- **الاعتماديات:** P2-T04

### P2-T06 — MCP Adapter Contract
- تصميم طبقة binding بين canonical drawing set وأدوات MCP الحقيقية.
- توحيد اسم الأداة، payload contract، retry policy، validation after save.
- **الأولوية:** Critical
- **الاعتماديات:** P2-T04, P1-T06

### P2-T07 — Report Builder Contract
- بناء صيغة موحدة للتقرير: inputs, assumptions, design logic, output artifacts, validation summary.
- **الأولوية:** Medium
- **الاعتماديات:** P1-T03, P2-T03

### P2-T08 — Observability Hooks
- تعريف logging points: request received, clarification triggered, validation result, drawing call made, report generated.
- **الأولوية:** Medium
- **الاعتماديات:** P2-T06, P2-T07

## Exit Criteria
- النواة المشتركة تعمل مستقلة عن domain محدد.
- MCP adapter contract موثق قبل أي domain pack.
- drawing primitives + macros تُنتج output صحيحاً على مدخلات وهمية.
