# L1 QA Workflow Output Format Template

This document defines the consistent output format for all L1 QA Workflow reports. All orchestrators and sub-agents should follow this structure.

---

## Final Report Structure (Orchestrator Output)

### Section 1: Overall Status
```
PASS
```
or
```
REJECT
```

### Section 2: Header Validation
```
PASS
```
or
```
FAIL — <brief explanation of validation issues>
```

### Section 3: Results Summary Table

A comprehensive single table showing ALL section pairs at a glance:

| Section | Pair | Status | Severity | Failed Checks |
|---------|------|--------|----------|---------------|
| 2.1/2.2 IDU Installation | Near End | PASS | None | - |
| 2.3/2.4 IDU Power | Near End | PASS | None | - |
| 2.5/2.6 IDU Grounding | Near End | PASS | None | - |
| 2.7/2.8 IF Cable | Near End | FAIL | Critical | - Waterproofing on connectors — Multiple connector terminations show incomplete waterproofing with exposed metal parts |
| 2.9/2.10 FE Cable | Near End | PASS | None | - |
| 2.11/2.12 MW/ODU | Near End | PASS | None | - |
| 3.2/3.3 IDU Installation | Far End | PASS | None | - |
| ... | ... | ... | ... | ... |

**Table columns explained:**
- **Section**: Section number and name (e.g., "2.1/2.2 IDU Installation")
- **Pair**: "Near End", "Far End", or "NMS" for non-paired sections
- **Status**: Overall result (PASS/FAIL/N_A)
- **Severity**: Highest severity among failures (Critical/Major/Minor/None)
- **Failed Checks**: List of FAIL findings (max 2-3 per cell; add "... and X more" if exceeds)

**Ordering**: Near End pairs (2.x/2.x), then Far End pairs (3.x/3.x), then NMS sections.

---

### Section 4: Critical Findings & Rejection Reasons

**Only include if there are FAIL findings.**

For each FAIL finding, create a numbered sub-section:

```markdown
#### 1. Section 2.7/2.8 IF Cable - FAIL (Critical)

**Requirement:** Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation. Key check: Verify that no metal parts, cable jackets, or connection points are exposed to the environment.

**Finding:** Multiple connector terminations on the IF cable show incomplete waterproofing with exposed metal parts, cracked tape, or loose sealant allowing potential moisture ingress. Specific locations: connector near IDU and connector near ODU both show insufficient coverage.

**Annotated:** s2.8_img5_annotated.png
**Source:** s2.8_img5.png
```

Format details:
- **Counter**: Use sequential numbering (1, 2, 3, ...) across all FAIL findings in the report
- **Section header**: `#### <counter>. Section <section_number> <section_name> - <STATUS> (<SEVERITY>)`
- **Requirement**: Full rule text as defined in checklist
- **Finding**: Specific observation and impact
- **Annotated**: Filename of the annotated image with bounding boxes
- **Source**: Filename of the original image

Ordering: List all FAIL findings by section order (Near End, then Far End, then NMS).

---

### Section 5: Detailed Section Analysis

For each section pair with images, include a compliance reference table:

```markdown
#### Detailed Analysis: Sections 2.7/2.8 (IF Cable)

| Requirement | BEFORE Status | AFTER Status | Details |
|-------------|---------------|--------------|---------|
| 200mm bending radius maintained | ✅ | ✅ | Cable routed properly with adequate radius in both installations. |
| Waterproofing on connectors | ✅ | ❌ | BEFORE: Connectors properly sealed with heat shrink. AFTER: Multiple connectors show cracked waterproofing with exposed metal. *Annotated:* s2.8_img5_annotated.png *Source:* s2.8_img5.png |
| Grounding kit 0.5-1m from entry | ✅ | ✅ | Both installations show grounding kit within acceptable distance. |
| Cable labeling | ⚠️ | ✅ | BEFORE: No label visible in this photo (likely on opposite end). AFTER: Clear yellow ID tag identifying cable endpoints. |

**Legend:**
- ✅ = PASS
- ❌ = FAIL
- ⚠️ = N/A (not verifiable in this photo)
```

---

### Section 6: Missing Sections

```
No missing sections found.
```

or

```
- 2.4 IDU Power (Far End) - Missing
- 3.15 Topology (Far End) - Missing
```

---

### Section 7: Severity Summary

```
**Critical:** 2
**Major:** 1
**Minor:** 0
**Total FAIL findings:** 3
```

---

### Section 8: Rejection Reasons

**Only include if Overall Status is REJECT.**

```
## Rejection Reasons

2.7/2.8 IF Cable: Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation. Key check: Verify that no metal parts, cable jackets, or connection points are exposed to the environment.

2.7/2.8 IF Cable: Grounding kit 0.5-1m from entry points.
```

---

## Sub-Agent Output Format (Section-by-Section Checks)

When an orchestrator spawns sub-agents to check individual sections, each sub-agent returns:

### 1. Structured `final_answer` payload

```json
{
  "section": "2.7/2.8 IF Cable",
  "status": "FAIL",
  "severity": "Critical",
  "legitimacy": {
    "status": "PASS",
    "reason": "Before and After photos show legitimate site conditions"
  },
  "not_verifiable_count": 1,
  "findings": [
    {
      "check": "Waterproofing on connectors",
      "requirement": "Waterproofing on connectors: the connection must be fully protected from moisture ingress...",
      "status": "FAIL",
      "severity": "Critical",
      "description": "Multiple connector terminations show incomplete waterproofing with exposed metal parts",
      "annotated_image": "s2.8_img5_annotated.png",
      "source_image": "s2.8_img5.png",
      "side": "after"
    },
    {
      "check": "Grounding kit placement",
      "requirement": "Grounding kit 0.5-1m from entry points",
      "status": "PASS",
      "description": "Grounding kit properly installed within acceptable distance",
      "side": "after"
    }
  ]
}
```

### 2. Brief Markdown Summary

```markdown
### Section: 2.7/2.8 IF Cable
**Status:** FAIL  |  **Severity:** Critical

**Legitimacy Check:** PASS — Before and After photos show legitimate site conditions
**Not Verifiable Count:** 1

#### Findings Summary:
- [FAIL] Waterproofing on connectors — Waterproofing on connectors: the connection must be fully protected from moisture ingress...
  - Observation: Multiple connector terminations show incomplete waterproofing with exposed metal parts
  - *Annotated:* `s2.8_img5_annotated.png`
  - *Source:* `s2.8_img5.png`
- [PASS] Grounding kit placement — Grounding kit 0.5-1m from entry points
  - Observation: Grounding kit properly installed within acceptable distance
- [N/A] Cable labeling — Physical ID tags (yellow or white) on every visible cable end
  - Observation: Cable end label not visible in this photo angle
```

---

## Key Consistency Rules

1. **Always present**: Results Table, Severity Summary, and Rejection Reasons (if applicable)
2. **Numbering**: Use sequential numbering for Critical Findings across the entire report
3. **Annotation format**: Every FAIL includes `*Annotated:*` and `*Source:*` lines
4. **Status symbols**: 
   - ✅ = PASS (in tables)
   - ❌ = FAIL (in tables)
   - ⚠️ = N/A (in tables)
   - `[PASS]`, `[FAIL]`, `[N/A]` (in findings lists)
5. **Section ordering**: Near End → Far End → NMS sections
6. **Requirement text**: Always include the full requirement, not just a summary
7. **Clarity**: Each finding should be independently understandable without cross-referencing

---

## Example Complete Output

See [EXAMPLE_OUTPUT.md](EXAMPLE_OUTPUT.md) for a full example report.
