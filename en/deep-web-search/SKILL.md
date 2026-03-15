---

name: deep-web-search
description: Uses an iterative think-search-think loop for multi-round deep web research. Supports both lightweight recon and formal research. Recon discovers hidden dimensions and generates better clarification questions. Formal research produces a v1/v2-compatible structured evidence pack. Use this for deep research, industry analysis, market reports, competitor intelligence, trend analysis, and any task requiring thorough multi-source information gathering.

---

Deep Web Search

An iterative multi-round web-research engine. Use a think → search → verify → fill gaps loop to build broad knowledge, cross-check facts, and synthesize findings into a structured research pack that can be consumed directly by the main skill and downstream generators.

Core mechanism: think → search → verify → fill gaps → build the research pack

---

Run modes

This skill supports two modes.

1. `mode=recon`
- lightweight reconnaissance before formal research
- objective: discover hidden dimensions, candidate directions, and premise risks
- does not replace formal research
- default to 1 round; continue only if needed and still keep it lightweight

2. `mode=full`
- formal research mode
- objective: produce a qualified `evidence_pack`
- may stop only after the budget and evidence threshold for the current tier are met
- a formal research deliverable must not replace `mode=full` with `mode=recon`

---

Research input protocol

Before execution, read the following upstream structure:

```yaml
mode: recon | full
intent: user's real objective
topic: research topic
scope: scope and time window
research_question:
  angle: topic angle
  time_range: time range
  comparables: comparables
  output_purpose: output purpose
constraints:
  time_budget: optional
  format: optional
  scope: optional
  permissions:
    network: true | false
clarification:
  research_type: user-confirmed research type
  selected_directions: []
  audience: target audience
  depth: report depth
  format: file or artifact format
  time_range: confirmed or default-completed time range
  geo_scope: confirmed or default-completed geographic scope
  comparables: []
  key_metrics: []
  research_tier: briefing | standard | deep | special-topic
  source_types: []
  site_constraints:
    include: []
    exclude: []
  language_scope:
    languages: []
    region: global | china | specified_region
  page_open_limit: Top N
  output_template: conclusion-evidence-risk-links | comparison-table | timeline-evolution | checklist-points | evidence-cards
  inherited_fields: []
  upgrade_options: []
existing_recon_findings:
  predicted_research_type: predicted research type
  time_range:
    value: known time range
    reason: one-sentence reason
  geo_scope:
    value: known geographic scope
    reason: one-sentence reason
  comparables:
    - label: known comparable
      reason: one-sentence reason
      priority: high | medium | low
  key_metrics:
    - label: known key metric
      reason: one-sentence reason
      priority: high | medium | low
  recommended_source_types:
    - type: official | docs | github | authoritative_media | paper
      reason: one-sentence reason
      priority: high | medium | low
  recommended_sites:
    - domain: recommended domain
      site_type: official | docs | github | media | paper
      reason: one-sentence reason
      priority: high | medium | low
artifact_registry: []
execution:
  stage:
    current: research | validation | resume
required_outputs:
  - source_matrix
  - key_findings
  - conflicting_claims
  - gaps
  - confidence
  - decision_log
  - validation_status
```

---

Recon mode

Use recon mode when the upstream goal is to inspect the outside world first and then ask better questions.

Recon rules
- query volume: 3–5 lightweight queries
- rounds: 1 by default, extend only if needed and still keep it lightweight
- goal: discover hidden dimensions, generate candidate directions, improve clarification questions
- output: produce only `recon_findings` and candidate directions; do not replace formal research
- recon may search broadly in parallel for clues, but should not deep-read like formal research
- special case: if the user premise is clearly off, challenge the premise directly and reshape the clarification
- stop recon when selectable directions exist, budget is reached, or new information gain becomes low

Recon return structure

```yaml
mode: recon
summary: 2-3 sentence recon judgment
recon_findings:
  predicted_research_type: system-predicted research type
  time_range:
    value: recommended time range
    reason: one-sentence reason
  geo_scope:
    value: recommended geography
    reason: one-sentence reason
  comparables:
    - label: recommended comparable
      reason: one-sentence reason
      priority: high | medium | low
  key_metrics:
    - label: recommended metric
      reason: one-sentence reason
      priority: high | medium | low
  candidate_directions:
    - title: candidate direction title
      reason: one-sentence reason
      priority: high | medium | low
  recommended_direction_count: 1 | 2 | 3 | 4 | 5
  recommended_source_types:
    - type: official | docs | github | authoritative_media | paper
      reason: one-sentence reason
      priority: high | medium | low
  recommended_sites:
    - domain: recommended domain
      site_type: official | docs | github | media | paper
      reason: one-sentence reason
      priority: high | medium | low
  assumption_risks:
    - premise risk
clarification_prompts:
  - suggested follow-up question
next_step: move to clarification | continue recon
status: completed | partial | blocked
```

---

Formal research tiers and budgets

When `mode=full`, execute according to `clarification.research_tier` or the recommended default. If unspecified, default to `standard`.

- `briefing`: `3 queries / 3 pages / 3–5 independent sources / 2 source-type categories / 1 high-trust cross-check group / 1 round`
- `standard`: `6 queries / 10 pages / 10–14 independent sources / 3 source-type categories / 2 high-trust cross-check groups / 2 rounds`
- `deep`: `9 queries / 17 pages / 17–23 independent sources / 4 source-type categories / 3 high-trust cross-check groups / 3 rounds`
- `special-topic`: `12 queries / 24 pages / 24–36 independent sources / 5 source-type categories / 5 high-trust cross-check groups / 4 rounds`

Completion definitions
- `briefing`: quick, reliable conclusions with basic support
- `standard`: complete structure, baseline comparison, fairly complete evidence
- `deep`: stronger cross-validation for key conclusions
- `special-topic`: wide coverage, heavy validation, and complete conflict / gap handling

Shared requirements
- The page-open limit follows the tier by default; if `clarification.page_open_limit` is set, obey that instead.
- Filter search results first, then deep-read only the most relevant `Top N` pages.
- Key conclusions should be cross-checked by 2+ high-trust sources where possible, but avoid low-value repetitive validation.
- If sources conflict, preserve `conflicting_claims`.
- If key questions remain unverified, record them in `gaps`.
- Always output overall `confidence`.
- Default source priority: official site → docs → GitHub → authoritative media → paper. Prefer not to rely on aggregators.

Allowed downgrade conditions
- the user explicitly wants an ultra-fast overview
- the topic has very little public information and the limitation is made explicit
- the task is only for internal reference and not a formal deliverable

Even when downgraded, explicitly state the downgraded standard used.

---

Formal research execution constraints

When `mode=full` and upstream initial clarification is complete, obey these constraints:
- Filter search results first, then open only highly relevant `Top N` pages. `Top N` defaults to `clarification.page_open_limit` or the tier default.
- Source types, site constraints, languages, and region should follow `clarification` first. If those constraints are too strict and make results sparse or obviously off-target, return a relaxation suggestion instead of silently widening scope.
- Default source priority remains: official site → docs → GitHub → authoritative media → paper.
- Recon may fan out in parallel for clues. Formal research should deep-read only a smaller set of high-relevance targets.
- Keep only necessary cross-checks for the same conclusion. Avoid stacking same-origin or low-gain sources.
- Prefer `clarification.output_template` if provided. Otherwise recommend a generic template by task type.
- `clarification.format` is artifact format, while `clarification.output_template` is content organization. Do not confuse them.
- If `clarification.research_tier` is high, produce that tier directly instead of degrading first.

---

Think-search loop

The number of rounds and deep-read pages should follow `research_tier` and `page_open_limit`. The following is the default ceiling template, not a hard requirement for every task.

```text
Round 1: broad scan
- generate 3–5 multi-angle queries
- mix Chinese and English queries when useful
- establish topic frame and major gaps quickly

Round 2: fill core gaps
- add 2–3 targeted queries for gaps and weak conclusions
- use WebFetch for deep reading on authoritative pages
- begin building source matrix and cross-verification links

Round 3: verify and resolve conflicts
- cross-check critical conclusions
- look for opposing views, framing differences, and newer data
- judge whether there is conflict, staleness, or single-source risk
```

Stop conditions
- the current tier budget is satisfied
- key questions are covered
- key conclusions have enough support or their conflicts / limits are explicitly recorded
- a qualified `evidence_pack` is formed
- the selected `research_tier` is deliverable

Forbidden stop conditions
- only scattered source excerpts and no source matrix
- only a summary and no finding-to-source mapping
- conflict discovered but not recorded
- major gaps remain but are not written into `gaps`

---

Source evaluation rules

Authority tiers
- `S`: government / official data, peer-reviewed papers, financial filings, official docs
- `A`: mainstream media, industry reports, official blogs, authoritative institutions
- `B`: professional blogs, verified expert opinions, vertical media
- `C`: social media, anonymous posts, marketing-heavy content

Filtering rules
- keep the most authoritative version and remove repeated paraphrases
- single-source conclusions must be marked as weak-evidence risk
- when sources conflict, present both sides and explain why they differ
- attach dates to key information
- discard clearly outdated or unattributed sources unless only used for historical context

---

Formal research output protocol

After each round, normalize findings internally. When formal research is complete, return:

```yaml
mode: full
summary: 2-3 sentence research conclusion
research_tier: briefing | standard | deep | special-topic
queries_by_round:
  round_1: [initial queries]
  round_2: [gap-filling queries]
  round_3: [verification queries]
  round_4: [special-topic extra queries]
evidence_pack:
  source_matrix:
    - id: S1
      title: source title
      url: source URL
      date: published or accessed date
      tier: S | A | B | C
      type: official | media | report | paper | blog | forum
  key_findings:
    - claim: key conclusion
      sources: [S1, S2]
      status: verified | single_source | conflicting
  conflicting_claims:
    - topic: conflicting topic
      sides:
        - claim: framing or data version A
          sources: [S3]
        - claim: framing or data version B
          sources: [S4]
  gaps:
    - still unverified information
  confidence: high | medium | low
  decision_log:
    - why research is allowed to stop or move downstream
  validation_status:
    - item: validated item
      status: passed | partial | failed
upgrade_options:
  - stop at current version | standard | deep | special-topic
compatibility_aliases:
  research_artifact: compatibility mapping of evidence_pack
produced_artifacts:
  - artifact_id: A1
    kind: research_pack
    title: research pack
    path: object reference or path
progress:
  current_step: current action
  percent: 0-100
watchouts:
  - reminder about single-source risk, staleness, or framing mismatch
recommended_next_action: move to decomposition | move to generation | continue research | request confirmation
status: completed | partial | blocked
```

Requirements
- All `evidence_pack` fields are mandatory.
- `research_tier` must match the actual research level produced.
- `key_findings` should map to `source_matrix` whenever possible.
- Do not return only a natural-language summary without structured output.
- If the user upgrades within the same task, continue from the current research result by default. Do not return to recon or initial clarification unless upgrade-required fields are missing.

---

Delivery template

If this skill returns a summary directly upstream, default to:

```markdown
## Research Summary
[2-3 sentence summary]

## Key Findings
- [finding + source IDs]

## Conflicts and Watchouts
- [conflict point, timeliness risk, single-source risk]

## Unresolved Questions
- [gap that still needs verification]

## Source Matrix
- [source ID, title, link, tier]
```

---

Error handling

Scenario | Action
--- | ---
Sparse search results | try alternative keywords; if caused by site / language / region limits, offer a relaxation suggestion first
Critical content behind paywall | state the limitation and use verifiable free alternatives
Outdated information | flag the date and prioritize updated replacement sources
Source conflict | preserve both sides and record them in `conflicting_claims`
Topic too broad | return to better clarification instead of forcing a conclusion

---

Quality standards

- Every key factual claim should carry source IDs where possible.
- Key statistics should be cross-checked by 2+ high-trust sources when possible, while avoiding low-gain repetitive validation.
- Clearly distinguish facts, estimates, forecasts, and opinions.
- Always include dates or time windows.
- If formal research does not produce a complete `evidence_pack`, it is not complete and must not be handed to downstream formal writing.
