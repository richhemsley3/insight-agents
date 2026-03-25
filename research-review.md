# Research Review: Prototype Alignment with Research Findings

**Date:** March 25, 2026
**Reviewer:** Design evaluation against research-report.html, market-research.md, user-research-evaluation.md, journey-map.html
**Artifact reviewed:** prototype-final.html (Strata AI Insight Agents)

---

## Research Alignment Scorecard

| # | Research Finding | Addressed? | Score |
|---|-----------------|------------|-------|
| 1 | Alert fatigue (75 mentions) | Strong | 9/10 |
| 2 | Stale metadata | Strong | 9/10 |
| 3 | Manual classification bottleneck | Strong | 9/10 |
| 4 | Schema drift with blast radius | Excellent | 10/10 |
| 5 | Impact analysis is manual | Strong | 9/10 |
| 6 | Fragmented tooling | Strong | 8/10 |
| 7 | No operational visibility | Excellent | 10/10 |
| 8 | 77% failure from data quality | Moderate | 7/10 |
| 9 | Trust through transparency | Excellent | 10/10 |
| 10 | Progressive autonomy | Excellent | 10/10 |
| 11 | Meet users where they work | Partial | 5/10 |

**Overall alignment: 87% (96/110)**

---

## Per-Finding Evaluation

### 1. Alert Fatigue (75 mentions in reviews) --- Score: 9/10

**How the prototype addresses it:**
- Low-priority insight cards (e.g., stale documentation, Info severity) are **collapsed by default** with a "Show details" expander. This directly reduces visual noise on the home screen.
- Insights are ranked by priority (Critical > Warning > Info) with clear visual hierarchy through color-coded left borders and severity tags.
- The Focus screen provides filtering by priority level, category, and date range, plus sorting by priority, newest, blast radius, or confidence --- letting users control what they see.
- The sidebar badge shows only the critical count (2), not the total count, avoiding inflated urgency.
- Auto-resolved items appear as success cards rather than requiring action, communicating closure rather than demanding attention.

**Gap:** No explicit quiet hours, notification batching/digest grouping on the Focus screen itself, or daily notification budget. The Settings screen does offer frequency controls (real-time, hourly digest, daily digest) which partially addresses this. The Notifications screen could benefit from grouping related notifications rather than showing them as a flat list.

### 2. Stale Metadata --- Score: 9/10

**How the prototype addresses it:**
- A dedicated insight card ("12 table descriptions are outdated after recent schema changes") proactively surfaces stale documentation.
- The Automate agent proposes documentation updates as a pending automation ("Update documentation for 12 stale tables") with preview and approve actions.
- Completed automations in the log show successful documentation regeneration, proving the system acts on this problem.
- A scheduled "Documentation freshness check" automation runs daily at 07:00 UTC, making staleness detection continuous rather than one-time.
- Activity feed on the home sidebar shows "Resolved 3 stale metadata entries automatically."

**Gap:** No visual indicator of metadata freshness per-asset in the Find results (though trust signals do show "Fresh: 2 hours ago" and "Stale: 3 days" badges). The system detects and fixes stale metadata but does not yet show a global metadata health dashboard.

### 3. Manual Classification Bottleneck --- Score: 9/10

**How the prototype addresses it:**
- The Automate agent proposes batch classification ("Apply PII classification to 47 columns") with confidence breakdown (32 high-confidence, 15 medium-confidence).
- Auto-classification above the confidence threshold (95%) runs automatically, with an example showing "8 new columns in raw_signups" auto-classified at 99% confidence.
- Scheduled daily PII classification scans (06:00 UTC) make classification continuous.
- The approval workflow supports both batch approve ("Approve all high-confidence >95%") and individual review ("Review individually"), addressing different comfort levels.

**Gap:** No dedicated classification review queue for Priya (governance analyst). The research evaluation specifically called out the need for a "My Review Queue" sorted by confidence, filterable by regulation. The prototype routes classification through the Automate screen's pending tab, which is close but not persona-specific.

### 4. Schema Drift with Blast Radius --- Score: 10/10

**How the prototype addresses it:**
- Schema drift is the first and most prominent insight card, marked Critical with full blast radius visualization: "4 models > 2 dashboards > ~340 daily queries."
- The blast radius uses a visual chain (arrow-connected spans) showing the propagation path from source to consumers.
- Step-by-step agent reasoning traces the drift from detection through lineage to suggested fix: "Add column alias mapping `email AS customer_email` in the staging model."
- 94% confidence score is accompanied by a detailed tooltip explaining the scoring methodology (data type compatibility, naming similarity, value overlap analysis).
- The suggested action ("Apply column mapping") is directly executable with a preview option.

This is the strongest research-to-prototype alignment in the entire design. The journey map's Step 2 ("Agent surfaces role-relevant priority issue") maps exactly to this card.

### 5. Impact Analysis is Manual --- Score: 9/10

**How the prototype addresses it:**
- Every insight card includes a "Blast radius" section quantifying downstream impact (models, dashboards, queries, tables, data products).
- The Focus screen can be sorted by "Blast radius" to prioritize highest-impact issues.
- Agent reasoning chains show automated lineage tracing (e.g., "Lineage traced downstream: Found 4 dbt models... 2 Looker dashboards... approximately 340 daily queries").
- The data quality anomaly card (NULL rate spike) traces impact through to "Revenue reporting."

**Gap:** No interactive lineage graph visualization. The journey map's opportunities column mentions "Interactive impact graph showing upstream/downstream dependencies" and "One-click lineage view from insight card." The prototype shows blast radius as text chains, not as a visual graph. This is the most significant gap for Jordan's persona.

### 6. Fragmented Tooling --- Score: 8/10

**How the prototype addresses it:**
- Three agents (Focus, Find, Automate) operate within a single unified interface, reducing context-switching across separate tools.
- Find agent searches across 3,240 catalog assets, 127 policies, and 48 pipeline definitions in one query, eliminating the need to check Snowflake, dbt, Airflow, and Looker separately.
- Insight cards surface information from multiple systems (Snowflake schemas, dbt models, Airflow DAGs, Looker dashboards) in a single view.
- Agent log shows activity across all three agents in a chronological timeline.

**Gap:** The prototype does not show deep integration with specific external tools (IDE, Jupyter, dbt Cloud UI). The Find results show "View lineage" and "See access policy" buttons, but these appear to stay within the Strata interface rather than linking to the native tool. True fragmentation reduction requires either embedding within existing tools or providing deep links out.

### 7. No Operational Visibility --- Score: 10/10

**How the prototype addresses it:**
- The Focus agent is the direct answer to this finding. Instead of showing dashboards with risk scores, it tells Jordan exactly what to do: "Apply column mapping," "Investigate source," "Retry pipeline," "Revoke write access."
- Every insight card follows the pattern: what happened, why it matters, what to do about it. This matches the research report's principle "No alert without context."
- The home screen stat cards (Insights today, Actions pending, Auto-resolved, Agent accuracy) give immediate operational awareness.
- The activity feed provides a real-time log of what agents are doing.

This is a direct implementation of the research finding. The design converts monitoring data into actionable tasks.

### 8. 77% Failure from Data Quality --- Score: 7/10

**How the prototype addresses it:**
- Agent accuracy is displayed prominently (98.4% on home screen), creating a visible link between data quality and agent reliability.
- Confidence scores on every insight card are tied to data quality signals (pattern matching, ML classifier agreement, sample validation).
- The research report's design principle "Data Quality = Agent Quality" is articulated in the report but partially reflected in the prototype.
- Confidence tooltips explain the scoring methodology, linking reliability to data foundation.

**Gap:** The prototype does not show a direct "data quality score" alongside agent confidence. The research report calls for displaying data quality scores alongside agent confidence to make the relationship explicit. The Find results show quality scores (e.g., "Quality: 96/100") on data assets, but insight cards do not surface the quality score of the underlying data that informed the agent's analysis. A "Data quality: 96/100 for this source" line in the insight meta section would close this gap.

### 9. Trust Through Transparency --- Score: 10/10

**How the prototype addresses it:**
- Every insight card has a "Show reasoning" toggle that reveals a numbered step-by-step reasoning chain explaining exactly how the agent reached its conclusion.
- Confidence scores with detailed tooltips explain the scoring methodology (e.g., "94% confidence means the agent matched the renamed column by data type compatibility (100%), naming similarity (92%), and value overlap analysis (91%)").
- The Agent Log provides full transparency into every action taken, with reasoning chains, assets affected, and duration.
- Auto-resolved items show their confidence and offer an "Undo" action, maintaining user control over automated decisions.
- Resolved insights show who resolved them and when, creating an audit trail.

This is the strongest UX pattern in the prototype. It directly implements CrowdStrike Charlotte's confidence-scored verdicts model referenced in the research.

### 10. Progressive Autonomy --- Score: 10/10

**How the prototype addresses it:**
- The autonomy dial on the home sidebar offers three levels (Manual, Smart, Full auto) with clear descriptions.
- The Settings screen provides granular per-agent autonomy controls:
  - Focus agent: Manual / Smart / Auto-triage
  - Find agent: Basic / Smart / Proactive
  - Automate agent: Suggest only / Auto low-risk / Full auto
- Confidence thresholds are user-configurable: auto-apply above 95%, require review below 70%.
- The data scope section allows toggling which data sources agents can access and whether agents can modify data.
- The "Undo" capability on auto-resolved actions preserves user control even at higher autonomy levels.

This maps directly to the research report's principle: "Start with suggestions, earn trust, then automate."

### 11. Meet Users Where They Work --- Score: 5/10

**How the prototype addresses it:**
- Settings include a Slack notifications toggle ("Send critical and warning insights to #data-ops channel") and email digest.
- Notification frequency options (real-time, hourly, daily) allow users to control delivery.

**Gap:** This is the weakest area. The research report's design principle explicitly states "Embed agents in Slack, IDE, BI tools, and browser extensions -- not behind a dedicated 'AI' tab." The prototype is entirely an in-app experience. There is:
- No Slack bot UI or interaction model (only a notification push)
- No browser extension
- No IDE integration
- No BI tool embedding (e.g., Looker extension)
- No API or webhook configuration for custom integrations

The journey map references "Slack" and "Email" as touchpoints, but the prototype only implements the notification push, not the interactive agent experience in those channels. This is likely a Phase 2 concern given prototype scope, but it should be flagged as the largest gap between research vision and current design.

---

## Per-Persona Evaluation

### Anika (Data Analyst / BI Developer)

**Jobs to be done:**
- Find trustworthy data quickly: **Addressed.** The Find agent with trust signals (Quality score, Freshness, Classification, Owner) directly serves this need.
- Know when upstream changes affect dashboards: **Addressed.** Schema drift and data quality insight cards show blast radius including dashboard impact.
- Reduce context-switching: **Partially addressed.** The unified surface helps, but Anika's daily tools (Looker, SQL editor) are not directly integrated.

**Pain points addressed:**
- Excessive time searching across silos: Find agent searches all assets in one query.
- Cannot tell if data is trustworthy: Trust signals (quality score, freshness, classification) on every Find result.
- Dashboards break from upstream changes: Schema drift cards trace impact to dashboards.
- Multiple tools fragment the workflow: Unified agent surface, but no extension into Looker/BI tools.

**Gaps:**
- No personalized insight view scoped to "datasets Anika uses." The prototype is viewed through Jordan's perspective.
- No interactive lineage graph showing how upstream changes propagate to her specific dashboards.
- No way to subscribe to specific tables/dashboards for change notifications.

### Jordan (Data Engineer / Platform Engineer)

**Jobs to be done:**
- Understand impact before changes: **Addressed.** Blast radius on every insight card, schema drift detection with lineage.
- Debug pipeline failures: **Addressed.** Data quality anomaly cards correlate failures to source system changes (e.g., Stripe API).
- Onboard new team members: **Partially addressed.** Find agent can answer questions about data, but no explicit onboarding flow.
- Ensure GDPR/CCPA compliance: **Addressed.** Classification insights and SOC 2 compliance tag audit.

**Pain points addressed:**
- Impact analysis is manual: Automated blast radius computation.
- Pipeline failures span multiple systems: Agent correlates across Stripe, Snowflake, dbt, Airflow.
- Documentation always out of date: Auto-documentation updates.
- Fielding ad-hoc requests: Find agent can answer analyst questions directly.

**Gaps:**
- No infrastructure-as-code (Terraform/YAML) path for agent configuration. Settings are UI-only.
- No connection health monitoring or scan cost tracking.
- No pipeline-specific view showing all insights relevant to a particular DAG or pipeline.

### Priya (Data Steward / Governance Lead)

**Jobs to be done:**
- Define and enforce quality standards: **Addressed.** Classification policies and compliance tag audits.
- Maintain business glossary: **Not directly addressed.** Documentation updates are table-level, not glossary-level.
- Manage access policies and classification: **Addressed.** Batch classification, access anomaly detection.
- Audit usage for compliance: **Addressed.** Agent log provides an audit trail of all actions.

**Pain points addressed:**
- Manual classification does not scale: Batch classification automation with confidence thresholds.
- Governance feels like overhead: Agents surface compliance gaps proactively rather than requiring manual audits.
- Bridging business and technical language: Find agent uses natural language queries.

**Gaps:**
- No dedicated classification review queue sorted by confidence. The user-research-evaluation.md specifically identified this as a critical missing feature.
- No classification policy templates (pattern-based rules that auto-apply).
- No cross-table consistency checker (same column classified differently across tables).
- No compliance trend dashboard showing improvement over time per regulation.

### Marcus (CDO / Data Product Owner)

**Jobs to be done:**
- Executive visibility into data health: **Partially addressed.** Home screen stats (insights, actions pending, agent accuracy) provide a snapshot, but there is no executive dashboard with trends.
- Demonstrate ROI of data governance: **Partially addressed.** Agent accuracy (98.4%) and auto-resolved count (12 this week) are proxies for value, but no cost-savings or time-savings metrics.
- Track team adoption: **Not addressed.** No multi-user adoption metrics, no team activity views.

**Pain points addressed:**
- No operational visibility into what needs attention: The Focus agent provides this for individual contributors, but Marcus needs aggregated views across the team.

**Gaps:**
- No executive dashboard or reporting view. The research report defines Marcus's agent interaction as "Aggregate adoption metrics," but the prototype has no team-level or org-level views.
- No export or reporting functionality.
- No ROI calculator or value attribution for agent actions.
- The prototype is scoped to a single user (Jordan). Marcus needs a multi-user, multi-team perspective.

### Sam (Business User / Domain Expert)

**Jobs to be done:**
- Find data without technical knowledge: **Addressed.** Find agent accepts natural language questions ("Where is customer PII stored?").
- Trust that data is current and correct: **Addressed.** Trust signals on Find results show quality scores and freshness.
- Get from question to answer quickly: **Addressed.** Find results are immediate with related questions for follow-up.

**Pain points addressed:**
- Does not understand technical dashboards: Find agent provides plain-language answers.
- Cannot determine if data is trustworthy: Quality scores and freshness badges on every result.

**Gaps:**
- No Sam-specific view or simplified interface. The prototype shows the full agent interface (Focus, Find, Automate), which may be overwhelming for a business user.
- No "certified dataset" or "recommended dataset" concept highlighted in Find results.
- No data request workflow (Sam wants data but does not know who to ask).

---

## Gaps and Recommendations for Future Phases

### Phase 2 Priorities (High Impact)

1. **Slack/external integration UX** (Finding 11). Design the interactive agent experience in Slack, not just notification pushes. Users should be able to approve automations, dismiss insights, and ask the Find agent questions directly from Slack. This is the largest gap between research vision and current implementation.

2. **Interactive lineage visualization** (Finding 5). Replace the text-based blast radius chain with a clickable graph showing data flow from source to consumer. This was specifically called out in the journey map opportunities for both Anika and Jordan.

3. **Classification review queue for Priya** (Finding 3). Build a dedicated review interface sorted by confidence, filterable by regulation, with batch approve/reject and classification policy templates. The user-research-evaluation identified this as a critical missing feature.

4. **Executive dashboard for Marcus**. Aggregate insights across users and teams. Show trends in agent accuracy, auto-resolution rates, classification coverage, and compliance posture over time. Include ROI metrics.

### Phase 3 Priorities (Medium Impact)

5. **Data quality score on insight cards** (Finding 8). Display the underlying data quality score alongside agent confidence on each insight card to make the "data quality = agent quality" relationship explicit.

6. **Persona-adaptive views**. Adjust the interface density, vocabulary, and default screens based on user role. Sam should see a simplified Find-first interface. Priya should land on a classification review queue. Marcus should see an executive dashboard.

7. **Infrastructure-as-code for agent settings**. Allow Jordan to export/import agent configuration as YAML, manage via Terraform, and version-control autonomy settings.

8. **Notification grouping and digests**. Group related notifications (e.g., all schema drift events from a single source change) rather than showing them as separate entries.

### Phase 4 Priorities (Lower Impact)

9. **Pipeline-specific agent views**. Filter all insights and actions by a specific pipeline, DAG, or data product.

10. **Classification consistency checker**. Surface cases where the same column pattern is classified differently across tables.

11. **Browser extension / IDE plugin**. Embed agent insights in VS Code, Jupyter, and BI tools.

---

## Overall Assessment

The prototype demonstrates strong alignment with the research findings. Of the 11 key research findings evaluated, 4 received perfect scores (schema drift, operational visibility, trust through transparency, progressive autonomy) and 5 received strong scores (8-9/10). These represent the core value proposition of the Insight Agents concept.

The two areas with the largest gaps are external integration (Slack/IDE/BI tool embedding) and the data-quality-to-agent-confidence linkage. The external integration gap is understandable given prototype scope --- this is inherently a Phase 2 deliverable. The data quality linkage is a more addressable gap that could be closed with relatively small changes to the insight card metadata.

The strongest design decisions in the prototype are:

- **Collapsed low-priority cards** to prevent alert fatigue
- **Step-by-step reasoning chains** with numbered steps explaining agent logic
- **Confidence tooltips** breaking down the scoring methodology
- **Blast radius visualization** quantifying downstream impact
- **Per-agent autonomy controls** with configurable confidence thresholds
- **Undo capability** on auto-resolved actions preserving user control

The persona coverage is strongest for Jordan (the prototype is viewed through his perspective) and weakest for Marcus (no executive/team view) and Sam (no simplified interface). Priya is moderately served --- her classification workflow is addressed through the Automate agent, but lacks a dedicated review queue identified as critical in the user research evaluation.

**Bottom line:** The prototype successfully translates 87% of research findings into concrete UX patterns. The design decisions are well-grounded in the research, and the gaps are clearly scoped to future phases rather than representing fundamental misalignment. The Focus/Find/Automate agent model directly addresses the core research insight that users need actionable intelligence, not more dashboards.
