# ADAM Skill Plan

هذه الحزمة توثّق خطة تنفيذ احترافية ومتكاملة لتحويل ADAM من MVP يركز على **Isolated Footing** إلى **Skill هندسية عامة** قادرة على فهم الطلبات النصية، اختيار المجال الهندسي الصحيح، تنفيذ منطق التصميم/التحقق، ثم إنتاج مخططات CAD وتقارير مهنية عبر MCP.

## الهدف
بناء Skill واحدة باسم **ADAM** تعمل كطبقة أوركسترا هندسية فوق عدة حزم اختصاصية:
- Structural Drawings
- Architectural Drawings
- Civil/Site Drawings
- MEP Drawings
- Calculation & Validation Packs
- CAD Generation & Annotation Packs

## مكونات الخطة
| الملف | الوصف |
|---|---|
| `00-master-roadmap.md` | الرؤية التنفيذية والخط الزمني والأولويات |
| `01-skill-scope-and-operating-model.md` | حدود المنتج ونموذج تشغيل الـSkill |
| `02-target-architecture.md` | المعمارية المستهدفة |
| `phases/` | مراحل التنفيذ المتسلسلة (6 مراحل) |
| `tasks/priority-backlog.md` | backlog شامل بالأولوية |
| `tasks/dependency-matrix.md` | مصفوفة الاعتماديات بين المراحل والمهام |
| `tasks/risk-register.md` | سجل المخاطر وخطط التخفيف |
| `tasks/acceptance-and-release-gates.md` | معايير القبول والانتقال بين المراحل |
| `tasks/execution-sequence.mmd` | مخطط Mermaid للمسار الحرج |

## مبدأ التنفيذ
لا يتم توسيع ADAM دفعة واحدة. التنفيذ يسير عبر **Kernel أولاً ثم Domain Packs**، بحيث يتم تثبيت طبقة النواة (schema, orchestration, validation, drawing contract, reporting contract) قبل إضافة كل نوع مخطط جديد.

## حالة التنفيذ
| المرحلة | الحالة |
|---|---|
| Phase 01 — Foundation & Contracts | 🔄 قيد التنفيذ |
| Phase 02 — Core Kernel & Drawing Abstraction | ⏳ لم تبدأ |
| Phase 03 — Structural Domain Pack | ⏳ لم تبدأ |
| Phase 04 — Multi-Discipline Expansion | ⏳ لم تبدأ |
| Phase 05 — Validation, QA & Benchmarks | ⏳ لم تبدأ |
| Phase 06 — Client Integration & Release | ⏳ لم تبدأ |
