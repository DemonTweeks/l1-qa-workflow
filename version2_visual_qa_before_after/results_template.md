# Results Template

Use this template as the starting point for agent-generated per-section results. Replace `<section_number>_<slug>` with the section identifier.

## Results (Section: <section_number> - <section name>)

| Section | Check Type | Check (requirement) | Status | Proof Images | Explanation |
|---|---:|---|---:|---|---|
|  |  |  |  |  |  |

### Summary (JSON)

Place a `summary.json` file alongside this document. Example structure:

{
  "section": "",
  "findings": [],
  "not_verifiable_count": 0,
  "status": "PASS",
  "severity": "None"
}

### Location

Save this file to: `workspace/results/<section_number>_<slug>/results.md`
