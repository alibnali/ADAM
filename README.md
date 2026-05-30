# ADAM — Autonomous Drawing & Analysis Machine

> AI-powered preliminary engineering drawing assistant.
> Structural · Civil · MEP · Architectural

---

## What is ADAM?

ADAM is an AI Skill that enables any LLM to function as a preliminary engineering drawing assistant. Given natural-language input (e.g. "ارسم قاعدة عمود 400×400 مم بحمولة 750 كيلو نيوتن"), ADAM:

1. Identifies the correct engine
2. Collects all required inputs
3. Runs code-compliant calculations (ACI 318-19, NEC 2023, UPC 2021, etc.)
4. Emits structured `DrawingPrimitive` objects ready for CAD rendering
5. Returns a `CalculationSummary` JSON block
6. Appends an engineer review checklist

## Supported Engines

| Engine | Element | Code |
|---|---|---|
| STR-FOOTING-01 | Isolated footing | ACI 318-19 |
| STR-COLUMN-01 | RC column | ACI 318-19 |
| STR-BEAM-01 | RC beam | ACI 318-19 |
| STR-SLAB-01 | One-way slab | ACI 318-19 |
| STR-WALL-01 | Shear wall | ACI 318-19 Ch.18 |
| STR-STAIR-01 | RC stair | ACI 318-19 + IBC |
| ARCH-PLAN-01 | Architectural floor plan | — |
| CIVIL-SITE-01 | Site plan | Local + IBC |
| MEP-PLUMB-01 | Plumbing & drainage | UPC 2021 |
| MEP-HVAC-01 | HVAC / ventilation | ASHRAE 62.1 |
| MEP-ELEC-01 | Electrical single-line | NEC 2023 |

## Repository Structure

```
ADAM/
├── ADAM.md                          ← Master Skill File (start here)
├── README.md
└── docs/
    └── adam-skill-plan/
        ├── contracts/               ← Type contracts + layer registry
        └── phases/                  ← All engine specs + QA files
```

## Quick Start

To activate ADAM in any compatible LLM session:

```
تفعيل ADAM
```

or

```
Activate ADAM
```

Then describe what you want to draw.

## Disclaimer

All ADAM outputs are **preliminary** and must be reviewed and stamped by a licensed engineer before use in construction or permit applications.

---

*ADAM v1.0 | حقوق النشر محفوظة — alibnali*
