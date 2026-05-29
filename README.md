# ADAM – AI Design & Automation for Civil Engineering

مستودع **ADAM** يقدّم خطة معمارية (Blueprint) مفصلة لبناء نظام آلي يساعد المهندس المدني في:
- تصميم قاعدة منفصلة (Isolated Footing).

الإصدار الأول (MVP) مبني على:
- **LLM Orchestrator** (نموذج لغوي كبير لإدارة المنطق والحسابات).
- **Model Context Protocol (MCP)** لربط النموذج بالأدوات الخارجية.
- **خادم CAD / DXF** مثل `text2dxf` لتوليد ملفات DXF من أوامر نصية.

## هيكل المستودع

- `docs/blueprint-overview.md`  — نظرة عامة على الهدف ونطاق النظام.
- `docs/system-architecture.md` — المعمارية العامة ومكوّنات النظام وتدفّق البيانات.
- `docs/modules-and-apis.md`   — تعريف الوحدات (Modules) وواجهاتها (APIs / Contracts).
- `docs/prompt-design.md`      — تصميم البرومبتات (System / User / Tools) لمهمة القاعدة المنفصلة.
- `docs/mcp-integration.md`    — دمج MCP وخادم `text2dxf` مع الـLLM.
