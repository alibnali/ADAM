# P1-T01 — Product Charter

## Product Name
ADAM — AI Engineering Drawing and Design Orchestration Skill

## Mission
Build a professional engineering skill that converts natural-language engineering requests into validated, reviewable drawing packages and technical reports through a controlled orchestration flow that separates input parsing, engineering reasoning, drawing abstraction, MCP tool execution, and reporting.

## Vision
ADAM becomes a unified engineering copilot for producing structured 2D engineering drawings and supporting design outputs across multiple disciplines, starting from structural foundations and expanding gradually into architectural, civil/site, and selected MEP coordination workflows.

## Problem Statement
Engineers often spend significant effort translating loosely structured requirements into repeatable design inputs, performing checks, formatting outputs, and manually redrawing standardized details in CAD. Existing LLM flows can help with reasoning, but without fixed schemas, validation gates, and drawing contracts, their outputs become inconsistent, hard to audit, and unsafe to rely on.

## Product Goal
Enable a disciplined path from:
1. engineering request,
2. structured normalized data,
3. domain-specific design or validation logic,
4. canonical drawing instructions,
5. MCP-driven CAD generation,
6. report and audit output.

The product is not intended to replace licensed engineering judgment. It is intended to accelerate drafting, early-stage design packaging, and repeatable documentation workflows under explicit human review.

## Primary Users
- Structural engineers preparing footing and foundation design packages.
- Civil engineers preparing site-related drawing outputs and layout-driven packages.
- Engineering teams using AI-assisted CAD drafting workflows via MCP-compatible clients such as Cline or Claude Desktop.
- Technical reviewers who need traceable assumptions, validation summaries, and consistent drawing standards.

## Core Job To Be Done
"When I provide an engineering request in natural language, ADAM should ask for missing information, normalize the inputs, apply the right engineering workflow, and generate a structured drawing/report package that is technically reviewable and CAD-ready."

## Product Principles
- Schema first, not prompt only.
- No drawing without explicit validated inputs.
- No silent assumptions; each assumption must be logged.
- No direct handwritten DXF generation inside the model.
- MCP tools are called only after validation gates pass.
- Drawing logic must remain separate from domain engineering logic.
- Every important output must be auditable.
- Human engineering review remains mandatory before construction or execution use.

## In Scope for v1
### Platform scope
- A single ADAM skill operating as an engineering orchestration layer.
- Support for structured request parsing, normalization, clarification, validation, drawing instruction generation, and report generation.
- A canonical contract between engineering result objects and drawing-generation tools.
- Compatibility with MCP-based CAD workflows for DXF-oriented outputs.

### Domain scope
- Structural domain as the first production baseline.
- First implemented production slice: isolated square footing.
- Expansion-ready architecture for strip footing, combined footing, grade beams, and related structural details after baseline stabilization.

### Output scope
- Structured JSON design/result payloads.
- DXF drawing outputs through MCP-connected CAD tooling.
- Arabic and/or English technical report output.
- Validation summary and assumption log.

## Out of Scope for v1
- Full BIM authoring or 3D parametric modeling.
- Automatic final approval of engineering drawings.
- Support for all international design codes from day one.
- Complex multidisciplinary coordination packs before the structural baseline is stable.
- Free-form artistic drafting or unconstrained CAD generation.
- Detailed execution drawings that bypass human review.

## First Release Disciplines
### Release baseline
1. Structural — foundations and footing-related drawing/design workflows.

### Planned expansion after kernel stabilization
2. Architectural — floor-plan and annotation-oriented outputs.
3. Civil/Site — boundaries, layout, drainage/site annotation workflows.
4. MEP Coordination — basic routing and opening/sleeve coordination, only after shared kernel maturity.

## Success Criteria
ADAM v1 is considered successful when it can reliably handle a focused set of structural foundation requests end-to-end with consistent outputs, clear assumptions, stable drawing contracts, and low reviewer rejection.

## Key Performance Indicators
- First-pass validation rate.
- Clarification rate before execution.
- Drawing generation success rate.
- Reviewer rejection rate.
- Average redraw count per accepted case.
- Percentage of outputs with complete assumption and validation logs.

## Risks This Charter Intentionally Controls
- Over-expanding the product before stabilizing the kernel.
- Treating prompts alone as architecture.
- Mixing engineering logic with CAD execution logic.
- Allowing opaque assumptions to leak into drawing outputs.
- Expanding into multi-discipline support before establishing a reusable canonical contract.

## Operating Constraints
- All production outputs must remain reviewable by a human engineer.
- Domain expansion must not bypass shared schemas and validation gates.
- MCP tool usage must remain minimal, explicit, and contract-driven.
- CAD outputs are preliminary unless explicitly reviewed and accepted by a qualified engineer.

## Dependencies Created by This Charter
- P1-T02 CanonicalDesignRequest
- P1-T03 CanonicalDesignResult and related contracts
- P1-T06 failure taxonomy
- P1-T07 prompt governance

## Approval Status
- Status: Drafted for execution baseline
- Phase: Phase 01 — Foundation and Contracts
- Task: P1-T01
- Next task on critical path: P1-T02 — Canonical Request Schema
