# 00 — Master Roadmap

## Vision
ADAM هو Skill هندسية موحدة تعمل كطبقة أوركسترا فوق حزم اختصاصية متعددة، تستقبل الطلبات الهندسية النصية، تحولها إلى نماذج بيانات منضبطة، تنفذ المنطق الهندسي المناسب، ثم تنتج package يحتوي DXF وتقريراً وسجل تحقق عبر طبقة MCP.

## الخط الزمني

```
Phase 01 — Foundation & Contracts       [الأولوية العليا — لا يمكن تجاوزها]
Phase 02 — Core Kernel & Drawing        [تعتمد على Phase 01]
Phase 03 — Structural Domain Pack       [تعتمد على Phase 02]
Phase 04 — Multi-Discipline Expansion   [تعتمد على Phase 03]
Phase 05 — Validation, QA & Benchmarks  [تعتمد على Phase 04]
Phase 06 — Client Integration & Release [تعتمد على Phase 05]
```

## المراحل

### Phase 01 — Foundation & Contracts
- P1-T01: Product Charter ✅
- P1-T02: CanonicalDesignRequest schema
- P1-T03: CanonicalDesignResult + sub-contracts
- P1-T04: DrawingInstructionSet contract
- P1-T05: ReportContract schema
- P1-T06: Failure & error taxonomy
- P1-T07: Prompt governance & system prompt architecture

### Phase 02 — Core Kernel & Drawing Abstraction
- P2-T01: Orchestrator core loop
- P2-T02: Input parser & normalizer
- P2-T03: Clarification engine
- P2-T04: Domain router
- P2-T05: Drawing abstraction layer
- P2-T06: MCP drawing client wrapper
- P2-T07: Report generator
- P2-T08: Kernel integration tests

### Phase 03 — Structural Domain Pack
- P3-T01: Structural sub-schema
- P3-T02: Isolated footing design logic
- P3-T03: Footing drawing instruction generator
- P3-T04: Footing report template
- P3-T05: Strip footing logic
- P3-T06: Combined footing logic
- P3-T07: Structural pack integration tests

### Phase 04 — Multi-Discipline Expansion
- P4-T01: Architectural domain pack
- P4-T02: Civil/Site domain pack
- P4-T03: MEP coordination pack (basic)
- P4-T04: Cross-discipline drawing coordination

### Phase 05 — Validation, QA & Benchmarks
- P5-T01: Validation suite design
- P5-T02: Benchmark case library
- P5-T03: Regression test harness
- P5-T04: KPI measurement tooling

### Phase 06 — Client Integration & Release
- P6-T01: MCP client compatibility matrix
- P6-T02: User documentation
- P6-T03: Release packaging
- P6-T04: v1.0 release

## المبادئ العامة للتنفيذ
- Schema أولاً، ليس Prompt فقط.
- لا رسم بدون بيانات محققة.
- لا افتراضات صامتة.
- Domain Packs تعتمد على Kernel مستقر.
- MCP tools تُستدعى بعقود واضحة فقط.
