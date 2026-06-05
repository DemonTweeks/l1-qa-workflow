---
name: l1-qa-workflow
description: Orchestrate a Quality Review of an L1 Self-Check report for the ZTE CelcomDigi Microwave project, with annotated image feedback for failed items.
---

# L1 Self-Check Report QA Review Workflow v2

You are orchestrating a Quality Review of an L1 Self-Check report for the ZTE CelcomDigi Microwave project.
Failed items get annotated images posted directly to the conversation with explanations.

## Input
The user uploads a Self-Check report (PDF) to the workspace.

## Workflow Steps

### Step 1: Parse the PDF (direct tool call)

```
parse_pdf(workspace_filename="<filename>", extract_images=true, build_composites=false)
```

The result gives you:
- `sections[]` — each has: `number`, `title`, `page`, `image_count`, `images[]` (filenames)
- `pages[]` — `pages[0].text` is the header page

**Note: composites are NOT built during parsing. Sub-agents build their own composites as needed.**

**Save the parse_pdf result — you will use it for all subsequent steps.**

### Step 2: Build QA items from parse_pdf result (you do this — NO run_python needed)

From the sections array, match to Before/After pairs and NMS groups:

**Before/After pairs** (compare installation quality):
- Near End: 2.1/2.2 (IDU), 2.3/2.4 (IDU Power), 2.5/2.6 (IDU Grounding), 2.7/2.8 (IF Cable), 2.9/2.10 (FE Cable), 2.11/2.12 (MW/ODU)
- Far End: 3.2/3.3 (IDU), 3.4/3.5 (IDU Power), 3.6/3.7 (IDU Grounding), 3.8/3.9 (IF Cable), 3.10/3.11 (FE Cable), 3.12/3.13 (MW/ODU)

**NMS sections** (screenshot verification):
- Near End: 2.14 (Topology), 2.15 (Slot Layout), 2.16 (General Alarm), 2.17 (Link Budget), 2.18 (RSL), 2.19-2.26 (Link Performance)
- Far End: 3.15 (Topology), 3.16 (Slot Layout), 3.17 (General Alarm), 3.18 (Link Budget), 3.19 (RSL), 3.20-3.27 (Link Performance)

**Standalone after-only sections**: 2.13 (ODU Grounding), 3.14 (ODU Grounding)

For each pair/group, note: section numbers, image filenames.
Also note which sections are missing (no images = missing section).

### Step 3: Header Validation + Site ID Validation (spawn_agent)

**IMPORTANT: Include the full header text from pages[0].text in the task description
so the sub-agent can validate all fields without needing file access.**

The sub-agent checks:
- Scope of Work (must be specific, e.g. Microwave Swap)
- Dates logical sequence: Construction Date <= Self-check Date <= QA Check Date
- Subcon Company & Responsible Person mentioned
- Actual work description (IDU / ODU / Antenna / IF Cable)
- Site ID validation against iEPMS for Project code "P202211283695_D002" using `fishbone__validate_project` and `fishbone__get_du_list`
- Compare site name and region to report data

```
spawn_agent(
 task="Header validation for L1 report.
 Header text:
 <paste pages[0].text here>

 Validate: scope specificity, date logic, subcon/responsible person,
 and site ID against iEPMS project P202211283695_D002.",
 tools=["fishbone__validate_project", "fishbone__get_du_list"]
)
```

### Step 4: Visual QA — Before/After Sections (spawn_agent, PARALLEL)

For each B/A pair that has images, spawn a sub-agent with `skill="visual_qa_before_after"`.
**Call multiple spawn_agent in one response for parallelism.**

Pass the individual image filenames (not composites) — the sub-agent builds its own composites internally.

```
spawn_agent(
 task="QA: <pair name> (sections <before>/<after>).
 Before images: [<before_img1>, <before_img2>, ...]
 After images: [<after_img1>, <after_img2>, ...]
 Checks:
 <list the QA checks for this pair type from the table below>",
 tools=["analyze_image", "annotate_regions", "list_workspace_files", "build_composite"],
 skill="visual_qa_before_after"
)
```

**QA checks per pair type:**

**IDU Installation (2.1/2.2, 3.2/3.3):**
- Before shows old/existing equipment (if Before shows new/pristine condition, verify workmanship in After photo; do NOT auto-reject)
- After: IDU on floating nuts, 4 screws, 1U ventilation gap
- Cable ends must have yellow tags or other clearly legible end identification. If a cable end is visible in an image but the label is not shown in that particular image, mark N_A — the label may be visible in another image of the same section. Labels on both ends of a cable are verified across the Before/After pair; one end per photo is acceptable.
- Labels should identify NE and FE sites when the format is present, but other clear and valid cable/equipment identification is acceptable if NE/FE site IDs are not shown. If a label is visible and legible, do not fail simply because it uses a different naming style.

**IDU Power (2.3/2.4, 3.4/3.5):**
- Tubular terminals at breaker, cable lugs at busbar
- Breakers labeled with yellow ID tags when visible. If tags are in frame, verify they are readable. If the breaker is visible but no yellow ID tag is present, mark N_A (tags may be shown in other section images). If the breaker label area is not shown, mark N_A.
- Power cables should have clear functional labels such as MAIN/STBY or equivalent role identifiers; if the cable end is visible and the tag clearly indicates its purpose, do not fail because the exact text differs. If the exact connection point is not visible, mark N_A rather than FAIL.

**IDU Grounding (2.5/2.6, 3.6/3.7):**
- Yellow-green grounding cable: if visible, verify it is yellow-green and properly installed; if not visible, mark N_A
- Cable lugs at visible terminations (if termination visible, verify lug present; if termination not visible in photo, mark N_A)
- Termination method proper and secure: bare screw lugs on hardware are acceptable; heat shrink or other insulation protection is acceptable when used but not mandatory for screw lug connections; if termination is unclear or angle doesn't allow verification, mark N_A
- Labels visible where the IDU or busbar end is shown (labels should be visible at connection point if shown in photo; if angle/lighting prevents visibility, mark N_A)

**IF Cable (2.7/2.8, 3.8/3.9):**
- 200mm bending radius maintained (if visible in photo, verify maintained; if angle/setup doesn't allow verification, mark N_A)
- Waterproofing on connectors: the connection must be fully protected from moisture ingress. Acceptable methods include:
  - Heat shrink sleeves (yellow or other colors) that fully encapsulate the connector
  - Self-amalgamating tape or rubber sealing tape that provides complete coverage
  - Moulded rubber boots or boots with integrated sealing
  - Potting or epoxy encapsulation
  - Any other method that fully encloses the connector termination and prevents water entry

  **Key check:** Verify that no metal parts, cable jackets, or connection points are exposed to the environment. The waterproofing material should fully cover the connector and a short length of the adjacent cable.
  Do not fail based on material color, number of layers, or aesthetic appearance alone. Focus on functional completeness of coverage. If the connector or its protection is not clearly visible, mark N_A.
- Grounding kit 0.5-1m from entry points (if entry point visible, verify distance; if not visible, mark N_A)
- Cable labeling:
  - If the cable end is **NOT** encapsulated in a waterproofing system (exposed connector and cable), a yellow ID tag must be physically attached to the cable near the termination. Labels printed/stamped directly on the cable jacket are also acceptable.
  - If the cable end is encapsulated in a waterproofing system (e.g., heat shrink, molded boot), the labeling may be integrated into the waterproofing (printed text on heat shrink, molded markers). In this case, a separate yellow tag on the bare cable is **not required**.
  - **Do NOT count watermarks, metadata overlays, on-screen annotations, or IEPMS label stamps** as valid labels.
  If the labeling is not legible or the cable end/termination is not visible, mark N_A. Verify across the Before/After pair - one end per photo is acceptable.

**FE Cable (2.9/2.10, 3.10/3.11):**
- Physical ID tags (yellow or white) on every visible cable end: verify that the cable end shown in the photo has a legible physical tag attached to the cable near the termination. **Do NOT count watermarks, metadata overlays, on-screen annotations, or IEPMS label stamps** as cable labels. Only physical tags attached to the cable itself qualify. If a cable end is not visible/not in frame, mark N_A for that end.
- Cables securely seated in ports (verify if ports and cable connection are visible; if not visible, mark N_A)
- Neat bundling with secure cable management; black cable clamps are preferred but not mandatory when cables are otherwise well-supported and routed neatly. If bundling is visible, verify it is tidy; if not visible, mark N_A.
- **Cross-check:** FE cable labeling should roughly align with IF cable labeling conventions (format may differ but should identify endpoints consistently). Inconsistent naming across cable types in the same installation warrants N_A review, not automatic FAIL.
- **Port condition:** when visible, verify ports are undamged and connector fully inserted. If ports are hidden, N_A is acceptable.

**MW/ODU (2.11/2.12, 3.12/3.13):**
- ODU securely mounted, captive screws diagonal (if ODU and screws visible, verify secure mounting and diagonal screw placement; if not fully visible, mark N_A)
- Connector waterproofing: the connection must be fully protected from moisture ingress. Acceptable methods include:
  - Heat shrink sleeves (yellow or other colors) that fully encapsulate the connector
  - Self-amalgamating tape or rubber sealing tape that provides complete coverage
  - Moulded rubber boots or boots with integrated sealing
  - Potting or epoxy encapsulation
  - Any other method that fully encloses the connector termination and prevents water entry

  **Key check:** Verify that no metal parts, cable jackets, or connection points are exposed to the environment. The waterproofing material should fully cover the connector and a short length of the adjacent cable.
  Do not fail based on material color, number of layers, or aesthetic appearance alone. Focus on functional completeness of coverage. If the connector or its protection is not clearly visible, mark N_A.
- Grounding kit installed (if grounding connection visible, verify kit present; if not visible, mark N_A)
- Antenna label: alphabet stencil preferred; handwritten marker over a stencil is acceptable when the stencil form is present. If the antenna label is visible in the image, verify the stencil format or stamped character style. If the antenna is visible but the label is not shown in that particular image, mark N_A (the label may be visible in another image of the same section).

### Step 5: Visual QA — NMS Screenshots (spawn_agent, PARALLEL)

For each NMS section with images, spawn a sub-agent with `skill="visual_qa_screenshot"`.
Each section is checked individually — do NOT build composites for NMS sections.
Pass the individual image filenames from parse_pdf's sections array.

```
spawn_agent(
 task="NMS QA: <section name> (section <number>).
 Images: [<img1>, <img2>, ...]
 Checks:
 <list section-specific checks from the table below>",
 tools=["analyze_image", "annotate_regions", "list_workspace_files"],
 skill="visual_qa_screenshot"
)
```

**Section-specific checks:**
- Topology (2.14/3.15): attach the correct topology screenshot for the site/link showing topology layout and details. Laptop screenshot is acceptable. Do not reject based on resolution if the topology information (site IDs, links, modules) is legible enough to verify configuration. For 3.15, topology printscreen must be obtained from technical team.
- Slot Layout (2.15/3.16): attach the latest slot layout screenshot from site; full slot/module layout must be visible and readable. Check that chassis/slots/modules/ports are shown clearly. Laptop screenshot is acceptable.
- General Alarm (2.16/3.17): attach the latest general alarm screenshot from site; it must be clear and readable. Key QA rule: **make sure no new alarm before leaving site**.
- Link Budget (2.17/3.18): provide a clear screenshot/photo of the **latest link budget with all data visible**, **plus** a photo proving **1 printed copy is attached on top of the IDU at site**.
- RSL / Microwave Link Configuration (2.18/3.19): attach the latest site screenshot showing live microwave link data; it should clearly show the RSL section or live link configuration details (e.g. Tx/Rx frequency, Tx power, link state, bandwidth/modulation where visible). If the panel is present but the numeric value is small and hard to read, do not automatically FAIL; instead mark N_A if the required field is present but not clearly legible.
- Site Environment (2.29/3.30):
  - Provide **4 comprehensive site environment photos from different angles/views** (e.g., corner views for indoor spaces, overall site views for outdoor/shelter installations). Aim for at least 4 distinct perspectives; if fewer are provided, note N_A for missing angles but do not auto-fail if the available photos otherwise cover the installation context.
  - Photos must be clear, GPS-enabled, and include an accepted IEPMS watermark in each image. A blurry watermark is acceptable as long as there are visible traces of the IEPMS watermark. If an image completely lacks the watermark, mark N_A and note 'missing IEPMS watermark'. Multiple missing watermarks may affect overall section status.
  - Verify basic environmental compliance: no obvious hazards, proper clearances around equipment, proper grounding visibility when possible. If environmental aspects are not clearly shown, mark N_A rather than FAIL.
  - Check that GPS coordinates in photo metadata (if present) are consistent with the reported site location — significant discrepancies (>500m) should be flagged for review, not auto-rejected.
  - **Note:** If angle diversity is limited but the site and equipment are clearly captured, use N_A for missing angles rather than FAIL. Only FAIL for Site Environment if the photos are staged (e.g., empty room, no equipment) or completely fail to show the installation site.
- Link Performance (2.19-2.26/3.20-3.27): if present, attach the latest 15-minute and 24-hour performance statistics. Screenshots must be clear and readable.

  **Basic threshold checks:**
  - RSL values should fall within expected microwave link ranges (typically -30 to -70 dBm depending on band and distance). Extreme outliers (e.g., > -20 dBm or < -80 dBm) may indicate measurement error or configuration issue — mark N_A and request clarification rather than auto-FAIL.
  - Downtime percentage should be low (<5% for 15-min stats). Higher values warrant comment but not immediate FAIL without context.
  - Check for alarm correlation: if General Alarm section showed active alarms, Link Performance should reflect corresponding degraded periods. If alarms exist but performance metrics appear normal, flag as inconsistent (N_A with note) rather than FAIL.

  **Important:** These are screening checks; definitive performance assessment requires engineering review. Use N_A generously for unclear data, and only FAIL for obviously fabricated or missing performance screenshots when they are expected.

**Important note:**
- Site Environment images must each contain an accepted IEPMS watermark. Blurry or partial watermarks are acceptable if traces are visible. Missing watermarks should be recorded as N_A findings; if multiple images lack the watermark, consider the section insufficient evidence.
- Do not fail Site Environment for limited angle variety when the equipment and installation context are clearly captured; use N_A or minor comments instead unless the site view is completely insufficient.
- The KB evidence clearly confirms Topology, Slot Layout, General Alarm, Link Budget, RSL, Site Environment, and Link Performance sections.
- Link Performance sections (2.19-2.26/3.20-3.27) are valid report sections and should be checked if present.
- Based on current evidence, **ATPC** and **ACM** appear as fields inside the **RSL / Microwave Link Configuration** screenshot, not confirmed standalone QA sections.
- Use the parsed report section titles and screenshots as the authoritative source for NMS section behavior.

### Step 6: Present Results

After ALL sub-agents complete, present a structured summary message.

**How to read sub-agent results:** each visual QA sub-agent now returns its findings as a
structured payload via the `final_answer` tool. You will see this payload in the spawn_agent
result under `data.final_answer` with this shape:

```json
{
 "section": "2.1/2.2 IDU Installation",
 "status": "FAIL",
 "severity": "Major",
 "legitimacy": {"status": "PASS", "reason": "..."},
 "findings": [
 {
 "check": "Cable labeling",
 "requirement": "All visible cable ends must be labeled with yellow tags identifying NE and FE site IDs",
 "status": "FAIL",
 "severity": "Major",
 "description": "FE port cable end has no label visible",
 "annotated_image": "after_2_1_annotated.png"
 },
 ...
 ],
 "not_verifiable_count": 1
}
```

Use the `requirement` field to cite the exact rule that was violated for each FAIL.

Present:
1. **Overall status**: PASS or REJECT
2. **Header validation**: pass/fail + issues
3. **Section-by-section results table**:
 | Section | Pair | Status | Severity | Failed checks (with rule cited) |
 For all Near End and Far End pairs/NMS sections.
4. **Missing sections** (if any)
5. **Severity summary**: X Critical, Y Major, Z Minor
6. **Rejection reasons** (if REJECT) — cite the `requirement` text from the relevant findings

The annotated images for failed items are already posted by the sub-agents and the filenames
are recorded on each finding. You can list them inline with the corresponding rule violation.

## Rejection Criteria (Immediate REJECT)
- Missing mandatory photo sections (IDU, Power, Grounding, Cable, ODU Before/After)
- Missing or incorrect labeling on any cable or equipment (only FAIL if the label is missing, unreadable, or clearly wrong for the site/equipment)
- No grounding cable installed (no yellow/green grounding cable visible)
- Staged/fake Before photos (clear evidence of lab test setup or staging, NOT just new equipment being installed)
- Missing NMS screenshots (Topology, Slot Layout, RSL)
- Inconsistent site data across sections (site ID mismatch, impossible dates)
- Missing Far End entirely

**Additional triggers (use with judgment):**
- >3 N_A items in a single NMS section (indicates photo quality too poor to verify)
- FE Cable: >1 N_A on cable security/labeling in the same pair (suggests inadequate inspection)
- Site Environment: <3 distinct angles without justification (missing required views)
- Link Performance: all N_A when performance screenshots are expected (link configuration warrants them)

## Important Notes
- **Labeling rule**: Only FAIL a label if it is missing, unreadable, or identifies the wrong site/equipment. Extra descriptive text, additional info (IP, TX/RX), or minor format variations on an otherwise correct label are NOT grounds for FAIL.
- **Labeling visibility rule**: A single photo typically shows only ONE end of a cable. When the rule says "labels at both ends", verify only the labels VISIBLE in the photo — do NOT FAIL because the opposite end is not in frame. The Before/After photos cover both ends across the section pair (e.g. After IDU shows IDU end, After ODU/Busbar shows the other end).
- **Label format guidance**: Preferred label format uses NE Site ID–FE Site ID. However, other clear, legible labels that identify the cable/equipment end or function are acceptable if the exact NE/FE format is not shown.
- **Verification rule**: If a component (heat shrink, breaker tag, grounding kit, waterproofing, etc.) is NOT VISIBLE in the photo (due to angle, distance, or framing), mark it N_A (not verifiable), NOT FAIL. Only mark FAIL when the item is clearly visible but defective or missing.
- **N_A usage:** N_A (not verifiable) is appropriate when the photo angle, resolution, or framing prevents clear verification of a requirement. However, if an entire section consistently returns N_A for critical items (e.g., all cable connections, all labeling), that section should be flagged as "insufficient evidence" rather than given a passing grade. In such cases, the final status may be REJECT based on lack of verifiable compliance.
- The report covers TWO sites (Near End + Far End) — check BOTH
- Antenna labels: alphabet stencil at bottom of antenna (not handwritten) when visible
- Power cables: tubular at breaker, cable lug at busbar when visible
- Grounding: yellow/green cable and cable lug required; heat shrink required if the termination point is visible
- Link Performance sections (2.19-2.26, 3.20-3.27) may be NA depending on link config

- For Topology screenshots: resolution is secondary; if topology details (site IDs, links, modules) are legible, do not fail for modest resolution.
