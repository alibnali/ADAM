# Dependency Matrix

## Phase Dependencies
- Phase 02 depends on Phase 01
- Phase 03 depends on Phase 01 + Phase 02
- Phase 04 depends on stable outputs from Phase 02 and lessons from Phase 03
- Phase 05 depends on executable slices from Phases 03/04
- Phase 06 depends on validated outputs from Phase 05

## Critical Path (Task Level)
```
P1-T01 → P1-T02 → P1-T03 → P2-T04 → P2-T06 → P3-T04 → P5-T03 → P6-T02
P1-T02 → P2-T02 → P2-T03 → P2-T07 → P3-T06 → P6-T03
P3-T01 → P3-T02 → P3-T03 → P3-T05 → P5-T02 → P6-T05
P1-T04 → P1-T05 → P4-T02 / P4-T04 / P4-T06
```

## Blocking Rules
- لا يبدأ أي Domain Pack قبل تثبيت canonical request/result schemas.
- لا يعتمد أي drawing macro على أداة MCP غير معرفة contractually.
- لا release قبل golden tests + human review protocol + failure recovery tests.
