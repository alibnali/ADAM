# Phase 06 — Client Integration and Release

## الهدف
تجهيز ADAM كـSkill قابلة للاستخدام الفعلي داخل Cline أو عميل MCP مشابه.

## Tasks

### P6-T01 — Skill Packaging Layout
- تنظيم ملفات skill: system prompt, domain prompts, schemas, examples, release notes.
- **الأولوية:** Critical
- **الاعتماديات:** P1-T07, P2-T06, P3-T06

### P6-T02 — Client Config Templates
- إعدادات Cline/Claude Desktop، naming conventions للخادم والأدوات.
- **الأولوية:** High
- **الاعتماديات:** P2-T06

### P6-T03 — Example Prompt Library
- أمثلة جاهزة لكل Domain مع success/failure/clarification patterns.
- **الأولوية:** High
- **الاعتماديات:** P3-T06, P4-T07

### P6-T04 — Onboarding & Docs
- quick start, installation, troubleshooting, limits, review responsibilities.
- **الأولوية:** High
- **الاعتماديات:** P6-T01, P6-T02

### P6-T05 — Release Gates
- تعريف alpha → beta → production transition.
- **الأولوية:** High
- **الاعتماديات:** P5-T01, P5-T02, P5-T03, P5-T04, P5-T05

### P6-T06 — Versioning Strategy
- semantic versioning للـskill والschemas والdomain packs.
- **الأولوية:** Medium
- **الاعتماديات:** P6-T01

## Exit Criteria
- Skill package قابلة للنسخ/التشغيل.
- التوثيق كافٍ لمستخدم هندسي جديد.
- release gates محددة وغير مبهمة.
