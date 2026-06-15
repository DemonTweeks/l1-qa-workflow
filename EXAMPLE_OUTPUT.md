# Example L1 QA Report - Refined Output Format

This document shows an example of the consistent output format that will be produced by the refined skills.

---

## PASS Report Example

---

### PASS

### PASS

| Section | Pair | Status | Severity | Failed Checks |
|---------|------|--------|----------|---------------|
| 2.1/2.2 IDU Installation | Near End | PASS | None | - |
| 2.3/2.4 IDU Power | Near End | PASS | None | - |
| 2.5/2.6 IDU Grounding | Near End | PASS | None | - |
| 2.7/2.8 IF Cable | Near End | PASS | None | - |
| 2.9/2.10 FE Cable | Near End | PASS | None | - |
| 2.11/2.12 MW/ODU | Near End | PASS | None | - |
| 3.2/3.3 IDU Installation | Far End | PASS | None | - |
| 3.4/3.5 IDU Power | Far End | PASS | None | - |
| 3.6/3.7 IDU Grounding | Far End | PASS | None | - |
| 3.8/3.9 IF Cable | Far End | PASS | None | - |
| 3.10/3.11 FE Cable | Far End | PASS | None | - |
| 3.12/3.13 MW/ODU | Far End | PASS | None | - |

(No critical findings — all sections passed validation)

### Missing Sections
No missing sections found.

### Severity Summary
- **Critical:** 0
- **Major:** 0
- **Minor:** 0
- **Total Findings:** 0

---

## REJECT Report Example

---

### REJECT

### PASS

| Section | Pair | Status | Severity | Failed Checks |
|---------|------|--------|----------|---------------|
| 2.1/2.2 IDU Installation | Near End | PASS | None | - |
| 2.3/2.4 IDU Power | Near End | PASS | None | - |
| 2.5/2.6 IDU Grounding | Near End | PASS | None | - |
| 2.7/2.8 IF Cable | Near End | FAIL | Critical | - Waterproofing on connectors — Multiple connector terminations show incomplete waterproofing with exposed metal parts |
| 2.9/2.10 FE Cable | Near End | PASS | None | - |
| 2.11/2.12 MW/ODU | Near End | PASS | None | - |
| 3.2/3.3 IDU Installation | Far End | PASS | None | - |
| 3.4/3.5 IDU Power | Far End | PASS | None | - |
| 3.6/3.7 IDU Grounding | Far End | PASS | None | - |
| 3.8/3.9 IF Cable | Far End | PASS | None | - |
| 3.10/3.11 FE Cable | Far End | PASS | None | - |
| 3.12/3.13 MW/ODU | Far End | PASS | None | - |

### Critical Findings & Rejection Reasons

#### 1. Section 2.7/2.8 IF Cable - FAIL (Critical)

**Requirement:** Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation. Key check: Verify that no metal parts, cable jackets, or connection points are exposed to the environment.

**Finding:** Multiple connector terminations on the IF cable show incomplete waterproofing with exposed metal parts, cracked tape, or loose sealant allowing potential moisture ingress. Specific locations: connector near IDU shows significant cracking in heat shrink, revealing exposed connector pins underneath. Connector near ODU exhibits loose sealant with visible cable jacket.

**Annotated:** s2.8_img5_annotated.png
**Source:** s2.8_img5.png

### Missing Sections
No missing sections found.

### Severity Summary
- **Critical:** 1
- **Major:** 0
- **Minor:** 0
- **Total Findings:** 1

## Rejection Reasons

2.7/2.8 IF Cable: Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation. Key check: Verify that no metal parts, cable jackets, or connection points are exposed to the environment.

---

## Complex REJECT Report Example (Multiple Findings)

---

### REJECT

### FAIL — Header validation failed: Site ID mismatch between report (P123) and iEPMS (P456)

| Section | Pair | Status | Severity | Failed Checks |
|---------|------|--------|----------|---------------|
| 2.1/2.2 IDU Installation | Near End | FAIL | Major | - Cable labeling — All visible cable ends must be labeled with yellow tags identifying NE and FE site IDs. FE port cable end shows only partial label |
| 2.3/2.4 IDU Power | Near End | PASS | None | - |
| 2.5/2.6 IDU Grounding | Near End | PASS | None | - |
| 2.7/2.8 IF Cable | Near End | FAIL | Critical | - Waterproofing on connectors — Multiple connector terminations show incomplete waterproofing with exposed metal parts<br>- Grounding kit placement — Grounding kit installation not visible or too far from entry point |
| 2.9/2.10 FE Cable | Near End | FAIL | Major | - Physical ID tags on cable ends — Cable port ends lack proper identification tags<br>- Neat bundling — Cable management shows loose routing without secure support |
| 2.11/2.12 MW/ODU | Near End | PASS | None | - |
| 3.2/3.3 IDU Installation | Far End | PASS | None | - |
| 3.4/3.5 IDU Power | Far End | PASS | None | - |
| 3.6/3.7 IDU Grounding | Far End | PASS | None | - |
| 3.8/3.9 IF Cable | Far End | PASS | None | - |
| 3.10/3.11 FE Cable | Far End | PASS | None | - |
| 3.12/3.13 MW/ODU | Far End | PASS | None | - |

### Critical Findings & Rejection Reasons

#### 1. Section 2.1/2.2 IDU Installation - FAIL (Major)

**Requirement:** Cable ends must have yellow tags or other clearly legible end identification. If a cable end is visible in an image but the label is not shown in that particular image, mark N_A — the label may be visible in another image of the same section.

**Finding:** FE port cable end visible in After photo shows only partial label (roughly 40% visible). Label text is cut off by image framing, making complete identification impossible. While a label attempt is present, it does not fully identify the cable endpoint as required.

**Annotated:** after_2_2_annotated.png
**Source:** after_2_2.png

#### 2. Section 2.7/2.8 IF Cable - FAIL (Critical)

**Requirement:** Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation. Key check: Verify that no metal parts, cable jackets, or connection points are exposed to the environment.

**Finding:** Multiple connector terminations show incomplete waterproofing with exposed metal parts, cracked tape, and loose sealant. IDU-side connector has significant heat shrink deterioration exposing connector pins. ODU-side connector shows loose sealant with visible cable jacket and underlying insulation.

**Annotated:** s2.8_img5_annotated.png
**Source:** s2.8_img5.png

#### 3. Section 2.7/2.8 IF Cable - FAIL (Critical)

**Requirement:** Grounding kit 0.5-1m from entry points (if entry point visible, verify distance; if not visible, mark N_A).

**Finding:** Grounding kit installation not clearly visible in provided After photos. Based on available angles, grounding connection appears to be more than 1m from the cable entry point at the IDU, exceeding the specified distance requirement. Cable routing obscures exact measurement, but estimated distance appears to be 1.5-2m.

**Annotated:** s2.8_img7_annotated.png
**Source:** s2.8_img7.png

#### 4. Section 2.9/2.10 FE Cable - FAIL (Major)

**Requirement:** Physical ID tags (yellow or white) on every visible cable end: verify that the cable end shown in the photo has a legible physical tag attached to the cable near the termination.

**Finding:** FE cable port connections visible in After photo lack physical ID tags. Two cable ends are clearly visible entering the FE port enclosure, but neither has a visible yellow or white identification tag attached to the cable near the termination point.

**Annotated:** after_2_10_annotated.png
**Source:** after_2_10.png

#### 5. Section 2.9/2.10 FE Cable - FAIL (Major)

**Requirement:** Neat bundling with secure cable management; black cable clamps are preferred but not mandatory when cables are otherwise well-supported and routed neatly.

**Finding:** FE cable routing shows loose bundling with inadequate support. Cables are routed without proper cable management clips or ties, resulting in cables sagging and crossing in an untidy manner. Multiple cable runs lack any support structure, creating a disorganized installation.

**Annotated:** after_2_9_annotated.png
**Source:** after_2_9.png

### Missing Sections
No missing sections found.

### Severity Summary
- **Critical:** 2 (IF Cable waterproofing, IF Cable grounding kit placement)
- **Major:** 3 (IDU Installation cable labeling, FE Cable identification tags, FE Cable bundling)
- **Minor:** 0
- **Total Findings:** 5

## Rejection Reasons

2.1/2.2 IDU Installation: Cable ends must have yellow tags or other clearly legible end identification. If a cable end is visible in an image but the label is not shown in that particular image, mark N_A — the label may be visible in another image of the same section.

2.7/2.8 IF Cable: Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation. Key check: Verify that no metal parts, cable jackets, or connection points are exposed to the environment.

2.7/2.8 IF Cable: Grounding kit 0.5-1m from entry points (if entry point visible, verify distance; if not visible, mark N_A).

2.9/2.10 FE Cable: Physical ID tags (yellow or white) on every visible cable end: verify that the cable end shown in the photo has a legible physical tag attached to the cable near the termination point.

2.9/2.10 FE Cable: Neat bundling with secure cable management; black cable clamps are preferred but not mandatory when cables are otherwise well-supported and routed neatly.

---

## Sub-Agent Section-by-Section Output Example

When checking a single section (e.g., "2.7/2.8 IF Cable"), the sub-agent returns:

### Section: 2.7/2.8 IF Cable
**Status:** FAIL  |  **Severity:** Critical

**Legitimacy Check:** PASS — Before and After photos show legitimate site conditions
**Not Verifiable Count:** 1

#### Findings Summary:
- [PASS] 200mm bending radius maintained — 200mm bending radius maintained (if visible in photo, verify maintained; if angle/setup doesn't allow verification, mark N_A)
  - Observation: Cable routed properly with adequate radius in both installations
- [FAIL] Waterproofing on connectors — Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include heat shrink sleeves, self-amalgamating tape, moulded rubber boots, or potting/epoxy encapsulation.
  - Observation: Multiple connector terminations show incomplete waterproofing with exposed metal parts and cracked tape
  - *Annotated:* `s2.8_img5_annotated.png`
  - *Source:* `s2.8_img5.png`
- [FAIL] Grounding kit 0.5-1m from entry points — Grounding kit 0.5-1m from entry points (if entry point visible, verify distance; if not visible, mark N_A)
  - Observation: Grounding connection appears more than 1m from entry point, exceeding specification
  - *Annotated:* `s2.8_img7_annotated.png`
  - *Source:* `s2.8_img7.png`
- [N/A] Cable labeling (non-waterproofed ends) — If the cable end IS encapsulated in a waterproofing system, no label check is required. If NOT encapsulated (exposed), a yellow ID tag must be present.
  - Observation: Cable labeling not verifiable due to waterproofing encapsulation on most connectors

---

## Key Format Elements Demonstrated

✅ Results Summary Table with all sections at a glance
✅ Critical Findings with sequential numbering and formatted headers
✅ Each finding includes: Requirement | Finding | Annotated | Source
✅ Severity Summary with counts
✅ Rejection Reasons listing full requirement text
✅ Section-by-section checks with structured findings
✅ Consistent Annotation/Source references
✅ Clear PASS/FAIL/N_A indicators throughout
