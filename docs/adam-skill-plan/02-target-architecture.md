# 02 — Target Architecture

## المعمارية المستهدفة

### المستوى الأول: Skill Core
```
ADAM Skill
├── kernel/
│   ├── orchestrator.py          # Main loop
│   ├── input_parser.py          # Parsing & normalization
│   ├── clarification_engine.py  # Missing data detection
│   ├── domain_router.py         # Route to correct domain
│   ├── drawing_abstraction.py   # DrawingInstructionSet builder
│   ├── mcp_drawing_client.py    # MCP calls wrapper
│   └── report_generator.py      # Report assembly
├── schemas/
│   ├── canonical_design_request.py
│   ├── canonical_design_result.py
│   ├── drawing_instruction_set.py
│   └── report_contract.py
├── domains/
│   ├── structural/
│   │   ├── schema.py
│   │   ├── isolated_footing.py
│   │   ├── strip_footing.py
│   │   └── combined_footing.py
│   ├── architectural/
│   ├── civil/
│   └── mep/
├── prompts/
│   ├── system/
│   ├── clarification/
│   └── report_templates/
└── tests/
    ├── kernel/
    ├── domains/
    └── integration/
```

### المستوى الثاني: MCP Integration
```
ADAM Skill
    ↕ (DrawingInstructionSet JSON)
MCP CAD Server
    → DXF File Output
```

### مبادئ المعمارية
- **Separation of Concerns**: كل module مسؤول عن شيء واحد فقط.
- **Contract-Driven**: التواصل بين المكونات عبر schemas محددة مسبقاً.
- **Validation Gates**: لا يمكن تجاوز مرحلة للمرحلة التالية بدون تحقق.
- **Testable by Design**: كل مكون قابل للاختبار بشكل مستقل.
- **MCP-First Drawing**: لا يولد ADAM DXF مباشرة، بل يعطي تعليمات لـMCP.

## تدفق البيانات
```
CanonicalDesignRequest
    → Domain Logic
    → CanonicalDesignResult
    → DrawingInstructionSet
    → MCP Client → DXF
    → ReportContract → Report
```

## معمارية الـPrompts
- System prompt: يعرّف ADAM كـSkill هندسية وليس كـchatbot عام.
- Domain prompts: مخصصة لكل تخصص هندسي.
- Clarification prompts: للاستفسار عن البيانات الناقصة.
- Report templates: لتوليد تقارير منسقة ومتسقة.
