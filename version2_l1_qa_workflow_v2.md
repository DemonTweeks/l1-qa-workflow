# L1 QA Workflow v2 — Agent Checklists

**Purpose:** Convert skill requirements into actionable, image‑driven checklists for the agent.

---

## Table of Contents

1. [Header Validation](#header-validation)
2. [IDU Installation (2.1/2.2, 3.2/3.3)](#idu-installation)
3. [IDU Power (2.3/2.4, 3.4/3.5)](#idu-power)
4. [IDU Grounding (2.5/2.6, 3.6/3.7)](#idu-grounding)
5. [IF Cable (2.7/2.8, 3.8/3.9)](#if-cable)
6. [FE Cable (2.9/2.10, 3.10/3.11)](#fe-cable)
7. [MW/ODU (2.11/2.12, 3.12/3.13)](#mwodu)
8. [NMS Sections](#nms-sections)

---

## Header Validation

### To Do's
- [ ] Verify Scope of Work is specific (e.g., Microwave Swap)
- [ ] Check date logic: Construction Date ≤ Self‑check Date ≤ QA Check Date
- [ ] Confirm Subcon Company & Responsible Person are mentioned
- [ ] Confirm actual work description includes IDU/ODU/Antenna/IF Cable
- [ ] Validate Site ID against iEPMS project `P202211283695_D002` using `fishbone__validate_project` and `fishbone__get_du_list`
- [ ] Compare site name and region to report data

### To Don'ts
- [ ] Do NOT accept vague scope (e.g., "general maintenance")
- [ ] Do NOT ignore date inconsistencies
- [ ] Do NOT proceed if Site ID fails iEPMS validation

---

## IDU Installation

### To Do's
- [ ] Verify After photo shows IDU mounted on floating nuts with 4 screws (if visible)
- [ ] Verify 1U ventilation gap exists (above or below IDU)
- [ ] Accept any clearly legible physical label on cable ends (yellow tag, jacket printing/stamping)
- [ ] Accept labels that identify NE/FE site IDs **or** other clear endpoint identification (circuit names, port numbers, role labels)
- [ ] Accept different naming styles/abbreviations as long as meaning is clear
- [ ] Accept verification of one cable end per image; cross‑pair verification across Before/After is sufficient
- [ ] If Before shows new/pristine equipment, do NOT auto‑reject; check After workmanship instead

### To Don'ts
- [ ] Do NOT fail for missing label in one image if it may appear in another → use N_A
- [ ] Do NOT require exact NE/FE format; other clear IDs are acceptable
- [ ] Do NOT count watermarks/metadata/annotations as labels
- [ ] Do NOT require both cable ends in the same photo
- [ ] Do NOT reject because Before appears new; focus on After quality

### Image Extraction Checklist (After photos)
- [ ] IDU mounting: floating nuts + 4 screws visible?
- [ ] Ventilation: 1U gap visible?
- [ ] Cable ends: at least one termination visible (connector + jacket)?
- [ ] For each visible cable end:
  - [ ] Physical label present (yellow tag or jacket print/stamp)?
  - [ ] Label readable?
  - [ ] Label meaningful (identifies endpoint)?
  - [ ] If label not visible in this image → mark N_A and check other images

---

## IDU Power

### To Do's
- [ ] Verify tubular terminals at breaker (if visible)
- [ ] Verify cable lugs at busbar (if visible)
- [ ] Breaker must have yellow ID tag attached; it can be on any surface (front, side, bottom) as long as clearly visible and readable
- [ ] Accept any functional power cable label (MAIN/STBY or equivalent role identifier)
- [ ] If cable end is visible and tag indicates purpose, do not fail over exact text differences
- [ ] If connection point not visible, mark N_A rather than FAIL

### To Don'ts
- [ ] Do NOT fail if breaker tag is on a non‑front surface but still clearly visible
- [ ] Do NOT fail for minor text variation on power cable labels if purpose is clear
- [ ] Do NOT require breaker tag if breaker itself is not shown → N_A
- [ ] Do NOT count watermarks/metadata/annotations as labels

### Image Extraction Checklist (After photos)
- [ ] Breaker visible?
  - [ ] Yellow ID tag present? (any surface, readable)
- [ ] Power cable end visible?
  - [ ] Terminal type correct (tubular at breaker, lug at busbar)?
  - [ ] Label readable and meaningful?
- [ ] If breaker/cable not fully visible → mark N_A per item

---

## IDU Grounding

### To Do's
- [ ] Verify yellow‑green grounding cable if visible:
  - [ ] Accept cable that is predominantly yellow with a clearly present green stripe/tint
  - [ ] Accept green/yellow spiral
  - [ ] If lighting/angle causes ambiguity → mark N_A rather than FAIL
  - [ ] FAIL only if clearly solid yellow with no green indication
- [ ] Verify cable lugs at visible terminations
- [ ] Verify termination method is proper/secure:
  - [ ] Accept bare screw lugs
  - [ ] Accept heat shrink or other insulation if present (not mandatory)
- [ ] Verify labels visible at connection points when those points are shown

### To Don'ts
- [ ] Do NOT fail for minor color perception issues; use N_A when uncertain
- [ ] Do NOT require heat shrink on screw lug connections
- [ ] Do NOT fail if termination unclear due to angle → N_A
- [ ] Do NOT require label if angle/lighting hides it → N_A

### Image Extraction Checklist
- [ ] Grounding cable visible?
  - [ ] Color: yellow with green stripe / green‑yellow spiral? (if ambiguous → N_A)
  - [ ] Cable lug present at termination?
  - [ ] Termination secure?
- [ ] Connection point (IDU/busbar end) visible?
  - [ ] Label readable at that point?

---

## IF Cable

### To Do's
- [ ] Verify 200mm bending radius maintained (if visible)
- [ ] Verify waterproofing on connectors:
  - [ ] No metal parts, cable jackets, or connection points exposed
  - [ ] Waterproofing fully covers connector + short length of adjacent cable
  - [ ] Accept methods: heat shrink, self‑amalgamating tape, moulded boot, potting/epoxy, sealant compounds (e.g., white sealing material)
  - [ ] For sealant: accept uneven application or minor gaps as long as coverage is present and no significant exposed parts
  - [ ] Do NOT fail based on color, layers, or aesthetics
- [ ] Verify grounding kit 0.5‑1m from entry points (if entry point visible)
- [ ] Cable labeling:
  - [ ] If cable end is **NOT** encapsulated: yellow tag or jacket printing required
  - [ ] If cable end **IS** encapsulated: **no label required** → mark N_A for labeling, verify waterproofing separately
  - [ ] Do NOT count watermarks/metadata/annotations/IEPMS stamps
- [ ] If cable end/termination not visible → N_A

### To Don'ts
- [ ] Do NOT fail for imperfect sealant application if coverage exists
- [ ] Do NOT require label on fully encapsulated connectors
- [ ] Do NOT fail if bending radius not verifiable due to angle → N_A
- [ ] Do NOT count overlays as labels

### Image Extraction Checklist (After photos)
- [ ] Connector visible?
  - [ ] Bending radius check (if possible)
  - [ ] Waterproofing method identified?
  - [ ] Coverage complete? (no exposed metal/jacket)
  - [ ] If sealed: label not required → N_A for labeling
  - [ ] If not sealed: label present and legible?
- [ ] Entry point visible?
  - [ ] Grounding kit present within 0.5‑1m?
- [ ] Cable end visible?
  - [ ] If not visible → N_A for related checks

---

## FE Cable

### To Do's
- [ ] Verify physical ID tag (yellow or white) on every visible cable end
- [ ] Verify tag is legible and attached to cable near termination
- [ ] Verify cables securely seated in ports (if visible)
- [ ] Verify neat bundling and secure cable management (if visible)
- [ ] Cross‑check FE labeling aligns with IF labeling conventions (format may differ but should identify endpoints consistently)
- [ ] Verify port condition: undamaged and connector fully inserted (if visible)

### To Don'ts
- [ ] Do NOT count watermarks/metadata/annotations/IEPMS stamps as labels
- [ ] Do NOT fail for bundling style differences if cables are neat and supported
- [ ] Do NOT require specific label format; content must identify endpoint
- [ ] Do NOT fail for missing label in one image → N_A (may be visible elsewhere)
- [ ] Do NOT auto‑fail on inconsistent naming across cable types → use N_A review
- [ ] Do NOT fail for hidden ports → N_A

### Image Extraction Checklist (After photos)
- [ ] Cable end visible?
  - [ ] Physical tag present? (yellow/white, legible)
  - [ ] Tag identifies endpoint clearly?
- [ ] Port visible?
  - [ ] Cable fully inserted?
  - [ ] Port undamaged?
- [ ] Bundling visible?
  - [ ] Neat and secure?
- [ ] If any item not visible → N_A per item

---

## MW/ODU

### To Do's
- [ ] Verify ODU securely mounted
- [ ] Verify captive screws are diagonal (if ODU and screws visible)
- [ ] Verify connector waterproofing:
  - [ ] No metal parts, cable jackets, or connection points exposed
  - [ ] Waterproofing fully covers connector + short length of adjacent cable
  - [ ] Accept methods: heat shrink, self‑amalgamating tape, moulded boot, potting/epoxy, any full enclosure method
  - [ ] Do NOT fail based on color, layers, aesthetics
- [ ] Verify grounding kit installed (if grounding connection visible)
- [ ] Verify antenna label:
  - [ ] Alphabet stencil preferred
  - [ ] Handwritten marker over stencil acceptable
  - [ ] If antenna visible but label not in this image → N_A

### To Don'ts
- [ ] Do NOT fail if screws not fully visible due to angle → N_A
- [ ] Do NOT fail for waterproofing aesthetics
- [ ] Do NOT require label unless antenna itself is shown with label area
- [ ] Do NOT fail if grounding connection not visible → N_A

### Image Extraction Checklist (After photos)
- [ ] ODU visible?
  - [ ] Mounted securely?
  - [ ] Captive screws diagonal? (if visible)
- [ ] Connector visible?
  - [ ] Waterproofing method identified?
  - [ ] Coverage complete?
- [ ] Grounding connection visible?
  - [ ] Grounding kit present?
- [ ] Antenna visible?
  - [ ] Label present? (stencil or stamped)

---

## NMS Sections

### To Do's

**Topology (2.14/3.15):**
- [ ] Attach correct topology screenshot for the site/link
- [ ] Ensure topology layout and details are visible/legible
- [ ] Accept laptop screenshots
- [ ] Do NOT reject based on resolution if information is legible
- [ ] For 3.15: topology printscreen must be from technical team

**Slot Layout (2.15/3.16):**
- [ ] Attach latest slot layout from site
- [ ] Full slot/module layout visible and readable
- [ ] Accept laptop screenshots

**General Alarm (2.16/3.17):**
- [ ] Attach latest general alarm screenshot
- [ ] Must be clear and readable
- [ ] Key rule: no new alarm before leaving site

**Link Budget (2.17/3.18):**
- [ ] Provide clear screenshot/photo of latest link budget with all data visible
- [ ] Provide photo proving 1 printed copy is attached on top of IDU at site

**RSL / Microwave Link Configuration (2.18/3.19):**
- [ ] Attach latest site screenshot showing live microwave link data
- [ ] Should clearly show RSL section or live link configuration (Tx/Rx frequency, Tx power, link state, bandwidth/modulation where visible)
- [ ] If panel present but numeric value hard to read → mark N_A, do NOT auto‑FAIL

**Site Environment (2.29/3.30):**
- [ ] Provide 4 comprehensive site environment photos from different angles/views (corner views for indoor, overall site views for outdoor/shelter)
- [ ] Each photo must be clear, GPS‑enabled, and include accepted IEPMS watermark
  - [ ] Blurry/partial watermark acceptable if traces visible
  - [ ] If image completely lacks watermark → mark N_A 'missing IEPMS watermark'
- [ ] Verify basic environmental compliance: no obvious hazards, proper clearances, grounding visibility when possible
- [ ] Check GPS coordinates consistency with reported site location (flag >500m discrepancy for review, not auto‑reject)
- [ ] If angle diversity limited but site/equipment clearly captured → use N_A for missing angles, not FAIL
- [ ] FAIL only if photos are staged (empty room, no equipment) or completely fail to show installation site

**Link Performance (2.19‑2.26/3.20‑3.27):**
- [ ] Attach latest 15‑minute and 24‑hour performance statistics (if expected)
- [ ] Screenshots must be clear and readable
- [ ] Basic threshold checks:
  - [ ] RSL values typically between -30 and -70 dBm (flag extreme outliers > -20 or < -80 as N_A with note)
  - [ ] Downtime percentage <5% for 15‑min stats (higher → comment, not immediate FAIL)
  - [ ] Check alarm correlation: if General Alarm showed active alarms, Link Performance should reflect degraded periods; if alarms exist but metrics normal → flag inconsistent (N_A with note)
- [ ] Use N_A generously for unclear data; only FAIL for obviously fabricated/missing screenshots

### To Don'ts
- [ ] Do NOT fail Site Environment for <4 angles if equipment/context are clearly captured → use N_A instead
- [ ] Do NOT auto‑reject resolution issues in Topology if legible
- [ ] Do NOT fail RSL for slightly unclear numbers if panel is present → N_A
- [ ] Do NOT fail Link Performance for outliers without context → N_A with note
- [ ] Do NOT reject GPS discrepancy >500m outright → flag for review

---

## Cross‑Cutting Rules (All Sections)

### To Do's
- [ ] Use N_A whenever a component is not visible due to angle, distance, or framing
- [ ] Require visible evidence for any FAIL (with bbox when localizable)
- [ ] Accept integrated labeling on waterproofed cable ends; label not required when encapsulation is complete
- [ ] Exclude watermarks, metadata overlays, on‑screen annotations, IEPMS stamps from valid labels
- [ ] Allow one visible end per photo; pair‑level consistency handled across Before/After
- [ ] For Before legitimacy: reject only on clear staging/fraud (lab setup, shipping boxes, unchanged identical photos)

### To Don'ts
- [ ] Do NOT fail for unverifiable items
- [ ] Do NOT require both ends in same photo
- [ ] Do NOT penalize format variations on otherwise correct labels
- [ ] Do NOT auto‑reject new Before equipment; focus on After workmanship

---

## Rejection Triggers (Immediate REJECT)

- [ ] Missing mandatory photo sections (IDU, Power, Grounding, Cable, ODU Before/After)
- [ ] Missing or incorrect labeling on any cable/equipment (only if label is missing/unreadable/wrong)
- [ ] No grounding cable installed (no yellow‑green cable visible when expected)
- [ ] Clear staging/fake Before photos
- [ ] Missing required NMS screenshots (Topology, Slot Layout, RSL)
- [ ] Inconsistent site data (site ID mismatch, impossible dates)
- [ ] Missing Far End entirely
- [ ] >3 N_A items in a single NMS section (poor photo quality)
- [ ] FE Cable: >1 N_A on security/labeling in same pair (inadequate inspection)
- [ ] Site Environment: <3 distinct angles without justification
- [ ] Link Performance: all N_A when screenshots are expected

---

**End of checklists.** Agent should tick these items based on image content during QA.
