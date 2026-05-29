# Phase 02 — Core Kernel and Drawing Abstraction

## Status
- Depends on: Phase 01 ✅
- Tasks: P2-T01 → P2-T05
- Output: A fully specified kernel that can route, assume, compose, and render any engineering drawing.

---

## Phase Goal

Phase 01 defined **what** ADAM receives and returns (schemas, codes, layers).  
Phase 02 defines **how** ADAM processes a request — the execution pipeline.

The kernel is a pure, stateless pipeline:

```
CanonicalDesignRequest
        │
        ▼
  [IntentRouter]          P2-T01
        │
        ▼
  [AssumptionEngine]      P2-T02
        │
        ▼
  [DomainEngine]          Phase 03+
        │
        ▼
  [DrawingPrimitive]      P2-T03  ◄── domain engine emits primitives
        │
        ▼
  [DrawingComposer]       P2-T04
        │
        ▼
  [OutputRenderer]        P2-T05
        │
        ▼
  CanonicalDesignResult
```

Each stage is a pure function: same input → same output. No global state.

---

## Tasks

| Task | Title | File |
|---|---|---|
| P2-T01 | IntentRouter | `08-intent-router.md` |
| P2-T02 | AssumptionEngine | `09-assumption-engine.md` |
| P2-T03 | DrawingPrimitive Model | `10-drawing-primitive.md` |
| P2-T04 | DrawingComposer | `11-drawing-composer.md` |
| P2-T05 | OutputRenderer Contract | `12-output-renderer.md` |
