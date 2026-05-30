# P5-T01 — Test Cases Library

## Format
Each test case:
```
TC-[ENGINE]-[NNN]
Input: { ... }
Expected Output: { calculation fields + primitive count }
Pass Criteria: tolerances ±1% on numerical results
```

---

## STR-FOOTING-01 Test Cases

### TC-FOOT-001 — Square footing, light load
```
Input: { P_kn: 500, fc: 21, fy: 420, q_allow: 100, cover: 75 }
Expected:
  B_m: 2.25
  h_mm: 450
  As_mm2: ≥ 900
  phi_Vn_punch ≥ Vu_punch
  phi_Vn_one_way ≥ Vu_one_way
Primitives: rect×2, rebar×n, dim_linear×4, text×3
```

### TC-FOOT-002 — Heavy column load
```
Input: { P_kn: 3000, fc: 28, fy: 420, q_allow: 150, cover: 75 }
Expected:
  B_m: 4.5 ± 0.1
  h_mm ≥ 600
  Shear checks pass
```

### TC-FOOT-003 — Punching shear governs over flexure
```
Input: { P_kn: 2000, col_b: 600, col_d: 600, fc: 21, q_allow: 200 }
Expected: h driven by punching, not flexure
```

---

## STR-BEAM-01 Test Cases

### TC-BEAM-001 — Simply supported, uniform load
```
Input: { span: 6, b: 300, fc: 25, fy: 420, DL: 20, LL: 15 }
Expected:
  h_mm: 500 ± 25
  Mu: 348.75 kN·m ± 1%
  As_pos ≥ As_min
  stirrup_spacing ≤ d/2
```

### TC-BEAM-002 — Deep beam (span/d < 4)
```
Input: { span: 2, b: 400, h: 700, fc: 25, fy: 420, Vu: 800 }
Expected: WARN-DEEP-BEAM triggered, strut-and-tie note added
```

### TC-BEAM-003 — T-beam with slab
```
Input: { span: 8, b_web: 300, hf: 120, beff: 1200, fc: 30, fy: 420 }
Expected: compression block a < hf (acts as rectangular)
```

---

## STR-COLUMN-01 Test Cases

### TC-COL-001 — Short tied column
```
Input: { b: 400, d: 400, Pu: 2000, fc: 25, fy: 420 }
Expected:
  rho: 0.01–0.08
  tie_spacing ≤ min(16db, 48dt, 400)
  phi_Pn ≥ Pu
```

### TC-COL-002 — Slender column check
```
Input: { b: 300, d: 300, Pu: 800, height_m: 5, fc: 25 }
Expected: slenderness check, moment magnification if kL/r > 34
```

---

## STR-SLAB-01 Test Cases

### TC-SLAB-001 — One-way slab, short span
```
Input: { span: 4, fc: 21, fy: 420, LL: 3, SDL: 2 }
Expected:
  h_mm: 200
  As_main ≥ As_min = 0.0018 × b × h
  As_temp = 0.0018 × b × h
```

### TC-SLAB-002 — As_min governs
```
Input: { span: 2.5, fc: 21, fy: 420, LL: 2, SDL: 1 }
Expected: As_required < As_min → use As_min
```

---

## MEP-ELEC-01 Test Cases

### TC-ELEC-001 — Small apartment
```
Input: rooms: [{name:"Living", area:25, h:3}, {name:"Bed", area:16, h:3}, {name:"Kitchen", area:10, h:3}]
Expected:
  circuit_count: 4–6
  total_kva: 3–6 kVA
  main_breaker: 25A or 32A
```

---

## CIVIL-SITE-01 Test Cases

### TC-SITE-001 — Standard residential plot
```
Input: { plot_width: 20, plot_depth: 30, setback_front: 5, setback_side: 2.5, setback_rear: 3 }
Expected:
  plot_area: 600 m²
  buildable_area: (20-5) × (30-8) = 330 m²
  bcr: ≤ 60%
  primitive count: ≥ 15
```

---

## Pass Criteria (All Engines)

| Check | Tolerance |
|---|---|
| Numerical results | ±1% of hand-calculated value |
| Primitive count | Exact match to spec table |
| Layer names | Exact match to layer registry |
| BLOCKING inputs missing | ERR-INPUT-MISSING triggered |
| Code compliance assertions | All pass, zero silent failures |
