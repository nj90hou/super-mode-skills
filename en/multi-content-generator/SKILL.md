---

name: multi-content-generator
description: Generates diverse professional content through a generate-review-optimize feedback loop. Supports presentation-style HTML, Word documents, webpages, Excel spreadsheets, PDF, Markdown, meeting notes, travel guides, and project reports. By default, it consumes v1/v2-compatible structured evidence packs, artifact registries, and shared state instead of improvising from free text. Use this when the user asks for any formatted deliverable.

---

Multi-Content Generator

A professional content-generation engine with a built-in quality loop. Automatically select the best output format, read the research pack, constraints, and target format from shared state, generate the deliverable, and complete structure, consistency, and evidence-mapping checks before delivery.

Core mechanism: read research pack → generate draft → review → optimize → deliver

---

Responsibilities

This skill is responsible for:
- generating polished content from structured research results
- choosing the right format and organization for the deliverable type
- performing one self-review pass after generation for structure, evidence, completeness, and consistency

This skill is not responsible for:
- performing formal research itself when the research pack is missing
- expanding recon findings directly into a formal report
- hiding evidence gaps and fabricating a complete-looking formal conclusion

---

Input protocol

Before execution, prefer reading the following structured objects:

```yaml
intent: user's real objective
mode: fast | think | expert | super
clarification:
  audience: audience
  depth: depth
  length: length
  format: output format
  target_artifacts: []
research_question:
  angle: topic angle
  time_range: time range
  comparables: comparables
  output_purpose: output purpose
evidence_pack:
  source_matrix:
    - id: S1
      title: source title
      url: source URL
      tier: S | A | B | C
  key_findings:
    - claim: key finding
      sources: [S1, S2]
  conflicting_claims:
    - conflicting conclusion or framing
  gaps:
    - still unverified information
  confidence: high | medium | low
artifact_registry: []
target_format: docx | html | xlsx | pdf | md
artifact_requirements:
  - section requirement
  - layout requirement
  - data presentation requirement
execution:
  stage:
    current: creation | review | delivery
compatibility_aliases:
  research_artifact: compatibility mapping of evidence_pack
```

Hard constraints
- If `evidence_pack` is missing, do not turn a research deliverable directly into a formal report.
- If only `recon_findings` exists without formal research results, you may only produce a draft, outline, or research-gap note. Do not disguise it as a formal final draft.

---

Generation rules

1. Identify the deliverable type first
- report: emphasize summary, analysis, evidence, limitations, and recommendations
- presentation: emphasize hierarchy, pacing, high-signal content, and visual rhythm
- spreadsheet: emphasize complete fields, consistent definitions, and traceable sources
- webpage: emphasize information architecture, readability, and modular structure

2. Map evidence to sections
- map `evidence_pack.key_findings` into the main body sections
- map `source_matrix` into evidence or sources sections
- map `conflicting_claims` and `gaps` into risk / limitation sections
- map `clarification` into tone, depth, length, and format control
- use `artifact_registry` as an input source to avoid regenerating existing artifacts

3. Generate the first draft
- organize the draft around the research pack, not around free-form improvisation
- light rewriting for clarity is allowed, but do not change the meaning of evidence
- do not replace sourced findings with unsourced assertions

---

Default content structure

For reports, analysis, and formal summaries, cover at least this structure by default:

```markdown
## Executive Summary
## Key Findings
## Evidence and Sources
## Limits and Risks
## Recommendations / Next Steps
```

For non-report outputs, allow format-specific variants, but keep these logical layers:
- core conclusion
- evidence support
- limitation note
- next-step recommendation

---

Review rules

After generation, perform a self-review for at least:
- structural completeness
- consistency with `evidence_pack.key_findings`
- preservation of `conflicting_claims`, or an explicit “no critical conflict” note
- preservation of `gaps` or limitation notes
- obvious overreach or unsupported writing
- compliance with audience, depth, length, and format requirements from `clarification`

If review fails:
- prefer local fixes first
- if the problem comes from missing research evidence, return to the main skill and request more research instead of forcing the draft

---

Output protocol

At minimum, return:

```yaml
draft_status: draft | revised | ready_for_delivery | blocked
produced_artifacts:
  - artifact_id: A1
    kind: html | md | docx | ppt | xlsx | pdf
    title: artifact title
    path: file path or object reference
    status: draft | ready
    editable: true | false
    shareable: true | false
compatibility_aliases:
  artifacts:
    - file or content object
coverage:
  summary: covered | missing
  findings: covered | missing
  evidence: covered | missing
  caveats: covered | missing
  recommendation: covered | missing
progress:
  current_step: current action
  percent: 0-100
watchouts:
  - issue that still needs attention
next_action: move to delivery | return to main skill for more research | request confirmation
```

---

Quality standards

- Formal research-driven writing must be grounded in `evidence_pack`.
- Key conclusions in the deliverable must trace back to the evidence pack.
- Limitations, conflicts, and gaps must be preserved instead of being flattened away.
- Recon information must not be written as formal research conclusions.
- If generation quality is not good enough, optimize structure and evidence mapping before expanding content.
