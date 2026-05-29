# 01 — Skill Scope & Operating Model

## ما هو ADAM
ADAM هو Skill هندسية تعمل كطبقة أوركسترا بين:
- المهندس (المستخدم)
- نماذج اللغة الكبيرة (LLM)
- أدوات MCP (CAD servers, calculation tools)
- نظام الإخراج (DXF + Reports)

## حدود المنتج في v1

### داخل النطاق
- استقبال طلبات هندسية نصية وتحويلها لبيانات منظمة.
- تنفيذ منطق التصميم والتحقق للمجال الهندسي المناسب.
- إنتاج DXF عبر MCP وتقارير هندسية.
- البنية التحتية: Structural foundations (isolated footing أولاً).
- نواة مشتركة: parsing, normalization, validation, drawing contract, reporting.

### خارج النطاق في v1
- BIM authoring أو نمذجة ثلاثية الأبعاد.
- اعتماد نهائي تلقائي للمخططات بدون مراجعة مهندس.
- دعم جميع الأكواد الدولية من البداية.
- توسع متعدد التخصصات قبل تثبيت الـkernel.

## نموذج التشغيل

```
[طلب نصي]
     ↓
[Parser & Normalizer]
     ↓
[Clarification Engine] ← إذا البيانات ناقصة
     ↓
[Domain Router]
     ↓
[Domain Logic: Design / Validation]
     ↓
[Drawing Instruction Generator]
     ↓
[MCP Drawing Client] → DXF Output
     ↓
[Report Generator] → Technical Report
     ↓
[Audit Log & Assumption Register]
```

## فصل المسؤوليات
| الطبقة | المسؤولية |
|---|---|
| Input Layer | Parser, Normalizer, Clarification |
| Domain Layer | Design logic, Validation, Calculations |
| Drawing Layer | DrawingInstructionSet, MCP client |
| Output Layer | Report, Audit log |

## قواعد الفصل الصارمة
- Domain Layer لا تعرف شيئاً عن DXF.
- Drawing Layer لا تنفذ حسابات هندسية.
- كل طبقة تتواصل مع الأخرى عبر contracts فقط.
- المراجعة البشرية إلزامية قبل الاستخدام التنفيذي.
