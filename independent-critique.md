# Design Critique: Strata AI-First Homepage Prototype

**Stage:** Interactive prototype
**Date:** 2026-03-26
**Reviewer:** Independent fresh-eyes critique
**Scope:** Full homepage (dashboard, conversation mode, catalog page, onboarding)

---

## Overall Impression

This is a well-executed AI-first homepage that makes a genuine bet on the AI prompt bar as the primary interaction model rather than treating AI as a sidebar feature. The visual design is polished, the information hierarchy is clear, and the progressive disclosure strategy (collapsible cards, skeleton loading, onboarding walkthrough) shows thoughtful consideration of cognitive load. However, the prototype reveals several structural issues around the conversation mode transition, role-based personalization that is cosmetic rather than functional, and a disconnect between the breadth of the dashboard and the depth available through each widget. The AI is central in positioning but partially decorative in execution -- many "Ask AI" buttons lead to the same static response regardless of context.

---

## Heuristic Scorecard

| Heuristic | Score | Notes |
|-----------|-------|-------|
| Visibility of System Status | 4/5 | Strong: skeleton loading, thinking animation with contextual labels, aria-live region for screen reader status. Weak: no indication of data freshness staleness on dashboard itself, no loading state for role switch. |
| Match Between System & Real World | 4/5 | Language is mostly user-appropriate ("Schema drift," "Blast radius" are domain-correct for data engineers). "AI-Readiness Score" may confuse some users -- readiness for what? |
| User Control & Freedom | 4/5 | Undo on dismiss, back button from conversation, clear conversation, Escape closes overlays. But: no way to undo a submitted AI query, no way to edit a prior query in conversation, clearing conversation has no confirmation. |
| Consistency & Standards | 3/5 | Inconsistent collapse behavior: Source Health starts expanded, Recent Activity and Popular Queries start collapsed with no visible rationale. "Ask AI" buttons vary in phrasing ("Ask AI," "Ask AI about impact," "Ask about this"). Two different prompt inputs on conversation view (top bar + bottom follow-up). |
| Error Prevention | 3/5 | Prompt bar accepts empty-looking queries (whitespace check exists but no guidance on what constitutes a good query). No character limit indicator. Suggestion chips help prevent malformed queries, but free-text input has no autocomplete or type-ahead. |
| Recognition Rather Than Recall | 4/5 | Good: suggestion chips, popular team queries, breadcrumb-like "Back to dashboard" in conversation. Missing: no query history, no way to see previous conversations, no saved queries. |
| Flexibility & Efficiency of Use | 3/5 | Cmd+K shortcut is excellent. Keyboard "d" to dismiss insights is discoverable only via code. No other keyboard shortcuts documented. No bulk actions anywhere. Role selector is a power feature but lacks keyboard navigation within the dropdown. |
| Aesthetic & Minimalist Design | 4/5 | Clean, warm visual language. Good signal-to-noise ratio. The AI sparkle badges on card headers are appropriately subdued after removing text labels. Minor: the homepage is still information-dense with 6+ widget types visible above and below fold. |
| Error Recovery | 4/5 | AI error state is well-designed with clear messaging, retry action, and back-to-dashboard escape. Error state preserves the query. But: no error handling shown for network failures on the dashboard itself, no error state for the catalog page. |
| Help & Documentation | 4/5 | Onboarding walkthrough is solid (4 steps, skip option, progress dots, Escape to dismiss, localStorage persistence). But: no way to re-access the tour after completion, no contextual help on individual widgets, no tooltip on the AI-Readiness Score explaining the methodology. |

**Overall: 3.7 / 5**

---

## What's Working Well

1. **AI prompt bar as hero element is convincing** _(H1: Visibility of System Status + Von Restorff Effect)_
   The prompt bar is visually prominent with a subtle glow on focus, scale transform, and clear send affordance. The Cmd+K shortcut, placeholder text, and suggestion chips below it create a complete interaction pattern that signals "this is where you start." The Von Restorff Effect is well-applied: the prompt bar is the single most visually distinct element on the page.

2. **Thinking state with contextual labels builds trust** _(H1: Visibility of System Status + Doherty Threshold)_
   The animated thinking dots paired with context-specific messages ("Analyzing schema changes across 14 sources...") communicate that the system is doing meaningful work, not just spinning. The 2.2-second simulated delay with the 15-second error timeout is a reasonable simulation of real AI latency. The aria-live region broadcasts status to screen readers.

3. **Insight cards with priority hierarchy are immediately scannable** _(H8: Aesthetic & Minimalist Design + Gestalt: Similarity)_
   The left-border color coding (red/yellow/green) combined with icon + text priority labels creates redundant visual encoding that works for colorblind users. The card layout uses flex with equal heights, making the grid scannable. The dismiss-with-undo pattern (H3: User Control) is a textbook implementation.

4. **Blast radius visualization makes abstract data tangible** _(H2: Match Between System & Real World)_
   The flow diagram (source -> models -> dashboards -> queries/day) maps to how data engineers actually think about impact. The connected nodes with arrows leverage Uniform Connectedness to show causality. The "~340 queries/day, 12 users affected" quantifies risk in terms the user cares about.

5. **Progressive disclosure through collapsible cards** _(Hick's Law + Miller's Law)_
   Defaulting Recent Activity and Popular Queries to collapsed while keeping the primary widgets (AI-Readiness, Security Posture, Governance) expanded is a reasonable information hierarchy. The collapse state is persisted to localStorage, respecting returning users. This reduces initial cognitive load from 8+ content sections to a more manageable 5.

6. **Governance gamification with point values** _(Goal-Gradient Effect + Zeigarnik Effect)_
   The 67% progress bar combined with "+8 pts," "+5 pts," "+3 pts" on each action creates strong Goal-Gradient motivation. Users can see exactly how each action contributes to their score. The "Start next task" primary CTA at the bottom channels this motivation into action.

7. **Trust tags with tooltips provide contextual metadata** _(H6: Recognition Rather Than Recall)_
   The inline trust tags (PII, Certified, Quality score, Freshness, Owner, AI-ready) surface critical metadata without requiring navigation to a detail page. The tooltip implementation with edge-aware positioning prevents viewport overflow. The visual encoding uses both color and icon for redundancy.

8. **Conversation mode preserves context well** _(H6: Recognition Rather Than Recall + Working Memory)_
   The conversation view carries the user's original query visually, shows the AI thinking state, then reveals the response with attribution and follow-up input. The "Back to dashboard" and "Clear conversation" controls provide clear exits.

---

## Areas for Improvement

### Critical Issues

**[C1] All AI queries lead to the same static response** _(H1: Visibility of System Status + Mental Model)_
What I see: Every "Ask AI" button, suggestion chip, and prompt submission triggers `askAI()` which navigates to the conversation view and shows the same pre-built schema-drift response regardless of what was asked. Clicking "Show PII inventory by column type" on the Security widget shows schema drift results. Clicking "Help me classify the 12 unclassified columns" shows schema drift results.
Why it matters: This breaks the fundamental promise of the AI-first design. In a stakeholder review or usability test, this will immediately undermine trust in the prototype. Users who try multiple queries will conclude the AI is fake, which contaminates all subsequent feedback. More critically, it means the prototype cannot be used to validate whether the AI response patterns work for different query types (PII queries, access queries, compliance queries each need different response structures).
Suggestion: Create 3-4 distinct response templates keyed to query category (schema/lineage, PII/classification, access/security, governance/compliance). Map the `askAI()` function to select the appropriate template based on keyword matching, which is already partially implemented in the thinking-label logic. This is the single highest-impact improvement for the prototype.
**Severity: Critical**

**[C2] No feedback when role changes -- personalization is cosmetic** _(H1: Visibility of System Status + Tesler's Law)_
What I see: The role selector dropdown works mechanically (icon and label update), but switching from "Engineer" to "Executive" changes nothing on the page. The insights remain the same, the widgets remain the same, the suggestion chips remain the same. There is no loading state, no content swap, no even subtle indication that the view has been personalized.
Why it matters: Role-based personalization is one of the headline features described in the design. If it does not visibly change anything, it actively misleads stakeholders about the product's capability. For users, it violates the match between system and real world: selecting "Executive" and still seeing schema drift details and dbt model references is a jarring mismatch.
Suggestion: At minimum, swap the suggestion chips and insight cards per role. Show a brief transition (0.3s fade) when roles switch. For the Executive role, replace technical insights with risk posture summaries. For the Analyst role, emphasize data quality and freshness. The greeting subtitle ("Data Engineering Lead") should also update to match the selected role.
**Severity: Critical**

### Major Issues

**[M1] Conversation view has two competing prompt inputs** _(H4: Consistency & Standards + Hick's Law)_
What I see: The conversation view (`screen-home-conversation`) has the main AI prompt bar at the top of the page AND a follow-up input at the bottom of the conversation thread. Both accept text input. Both have send buttons. They look slightly different (the top one has Cmd+K hint, the bottom one does not). It is unclear which one the user should use for follow-up questions.
Why it matters: Two inputs for the same function creates decision paralysis (Hick's Law) and inconsistency (H4). Users will waste time figuring out which input is "correct." The top bar placeholder says "Ask a follow-up..." while the bottom input says "Ask a follow-up question..." -- even the placeholder text is inconsistent.
Suggestion: Remove the top prompt bar from the conversation view or collapse it into a minimal sticky header with just the query text displayed (not editable). Use only the bottom follow-up input for continued conversation. This matches the pattern established by ChatGPT, Perplexity, and other AI-first products (Jakob's Law).
**Severity: Major**

**[M2] No query history or conversation persistence** _(H6: Recognition Rather Than Recall + Zeigarnik Effect)_
What I see: When a user clicks "Clear conversation" or navigates back to the dashboard, the conversation is gone forever. There is no history of past queries, no way to resume a conversation, and no indication on the dashboard that a conversation recently took place.
Why it matters: Data engineers and analysts often run the same query patterns daily. Requiring them to re-type or re-discover queries from scratch every session ignores the Paradox of the Active User (they will not remember exact query phrasing). The lack of persistence also means users cannot share AI findings with teammates or reference them in meetings.
Suggestion: Add a "Recent conversations" section (collapsed by default) to the dashboard, showing the last 3-5 queries with timestamps. Allow clicking to restore a conversation. Consider a "Share response" action alongside the existing copy/bookmark buttons.
**Severity: Major**

**[M3] Security posture widget metric cards have no context** _(H2: Match Between System & Real World + Anchoring Bias)_
What I see: The six security metric cards show raw numbers (47, 128, 7, 3, 12, 8) with short labels but no context for whether these numbers are good or bad beyond color coding on two of them. "47 Unclassified assets" -- is that 47 out of 50 (very bad) or 47 out of 3,240 (probably fine)? "128 PII cols tracked" -- is that a success metric or a risk count?
Why it matters: Without context, users will anchor on the raw number and may misinterpret the severity. A CDO seeing "47 unclassified" may panic or shrug depending on their assumed denominator. The metric lacks the "compared to what?" framing that makes numbers actionable.
Suggestion: Add denominators or trend indicators. "47 / 3,240 unclassified" or "47 unclassified (down from 62)." Even a small sparkline or micro-trend arrow would help. The "+5 this week" on the overall score is good -- extend that pattern to the sub-metrics.
**Severity: Major**

**[M4] Collapsed sections hide critical information by default** _(H1: Visibility of System Status + Serial Position Effect)_
What I see: "Recent Activity" is collapsed by default on the homepage. This section contains the most recent events: a classification scan 12 minutes ago, a schema change 1 hour ago, an access review 3 hours ago, and a new source connection. These are operationally relevant for a daily user, but they are hidden behind a click.
Why it matters: The Serial Position Effect suggests users pay most attention to the first and last items on a page. By collapsing Recent Activity (which is chronologically the most relevant), the design buries time-sensitive information in favor of slower-changing scores (AI-Readiness, Governance Progress). For a Data Engineer checking the dashboard first thing in the morning, recent events are arguably more important than the readiness score which changes slowly.
Suggestion: Default Recent Activity to expanded (showing the most recent 2-3 items) and collapse the Popular Queries section instead. Alternatively, show a one-line summary when collapsed: "4 events today -- 1 critical" to give users a reason to expand.
**Severity: Major**

**[M5] Onboarding tour has no re-entry point** _(H10: Help & Documentation + Paradox of the Active User)_
What I see: The onboarding walkthrough runs once on first visit and is persisted as complete via `localStorage.setItem('strata_onboarding_complete', 'true')`. There is no way to re-access the tour from the UI. The Help button ("?") in the header has no attached behavior.
Why it matters: The Paradox of the Active User means users will rush through the onboarding to start using the product. When they later want to understand a feature (e.g., "what does AI-Readiness Score mean?"), they cannot re-access the guidance. The non-functional Help button is a dead interaction that erodes trust (H1).
Suggestion: Wire the "?" help button to offer "Restart tour" as one option. Also add contextual help tooltips (info icons) on widget titles like "AI-Readiness Score" and "Governance Progress" that explain the methodology on hover.
**Severity: Major**

**[M6] Prompt bar has no type-ahead, autocomplete, or query suggestions** _(H5: Error Prevention + Postel's Law)_
What I see: The AI prompt input is a plain text field with no autocomplete, no type-ahead suggestions, and no indication of what types of queries the system can handle. The suggestion chips below provide some guidance, but once the user starts typing, they are on their own.
Why it matters: For an AI-first interface, the prompt bar is the most critical interaction point. Without type-ahead, users must rely on recall rather than recognition (H6 violation). They may submit poorly formed queries that produce unhelpful results, leading to the perception that the AI is not useful. Postel's Law says to be liberal in what you accept, but even liberal systems benefit from guiding users toward well-formed input.
Suggestion: Add a type-ahead dropdown that appears after 2+ characters, showing matching queries from the suggestion chips, popular team queries, and recent personal queries. This transforms recall into recognition and reduces error rates.
**Severity: Major**

**[M7] "Clear conversation" has no confirmation dialog** _(H3: User Control & Freedom + H5: Error Prevention)_
What I see: The "Clear conversation" button in the conversation view immediately navigates back to the dashboard and clears the conversation state. There is no confirmation step, no undo mechanism, and no way to recover the conversation.
Why it matters: The conversation may contain a complex multi-turn AI analysis that took time to build. Accidentally clicking "Clear conversation" (which is positioned next to "Back to dashboard") destroys this work irreversibly. This is a destructive action that should require confirmation, especially since there is no conversation history to recover from.
Suggestion: Add a lightweight confirmation: either a toast with "Conversation cleared -- Undo" (matching the insight dismiss pattern) or a brief confirmation dialog. Alternatively, "clearing" could just navigate away while preserving the conversation in history for later retrieval.
**Severity: Major**

### Minor Issues

**[m1] Keyboard shortcut "d" for dismiss is undiscoverable** _(H7: Flexibility & Efficiency of Use)_
What I see: Pressing "d" when an insight card has focus triggers the dismiss action. This shortcut is implemented in code but never communicated to the user -- no tooltip, no documentation, no keyboard shortcut reference.
Why it matters: Undiscoverable shortcuts help nobody. Power users who would benefit from this shortcut will never find it. Meanwhile, a user accidentally pressing "d" while an insight card has focus will dismiss it unexpectedly.
Suggestion: Add a keyboard shortcut reference accessible from the "?" help menu. Consider adding a subtle "Press D to dismiss" hint on focused insight cards.
**Severity: Minor**

**[m2] Inconsistent button text across similar actions** _(H4: Consistency & Standards)_
What I see: Actions related to AI are labeled inconsistently: "Ask AI," "Ask AI about impact," "Ask about this," "View blast radius" (which also triggers AI). In the activity timeline, the same concept appears as "Ask about this." In insight cards, it is "Ask AI." In source health, it is "Ask AI about impact."
Why it matters: Internal inconsistency increases cognitive load. Users must re-parse each label to determine if these are the same action or different ones. Consistent labeling aids recognition over recall.
Suggestion: Standardize to two patterns: "Ask AI" for general queries and "View [thing]" for specific drill-downs. Drop the "about impact" / "about this" variations.
**Severity: Minor**

**[m3] Trust tags are very small and dense on mobile** _(Fitts's Law)_
What I see: Trust tags (PII, Certified, Q:94, etc.) use 11px uppercase text with 10px icons. On the conversation view, result items can have 4-5 trust tags in a row. On mobile viewports, these become extremely small and difficult to tap for tooltip access.
Why it matters: Fitts's Law requires minimum 44x44px touch targets for reliable interaction. The trust tags with their tooltips are an important information layer, but at their current size they are difficult to interact with on touch devices.
Suggestion: On mobile, consider a truncated trust tag row that shows the first 2-3 tags with a "+2 more" overflow that expands on tap. Alternatively, increase touch target size by adding transparent padding around each tag.
**Severity: Minor**

**[m4] Hero subtitle statistics are static and not obviously interactive** _(H1: Visibility of System Status)_
What I see: The hero subtitle shows "Data Engineering Lead / 14 sources connected / 3,240 assets cataloged." These statistics look like static text but represent live system data. There is no indication of when these numbers were last updated or whether they are clickable.
Why it matters: Stale data presented as current erodes trust. Users may wonder if "14 sources" is up to date, especially if they just connected a new source.
Suggestion: Add a subtle "Last updated: 5 min ago" timestamp or make the stats clickable (linking to Sources and Catalog respectively). Even a hover state suggesting interactivity would help.
**Severity: Minor**

**[m5] Conversation send button is always visible in the follow-up input** _(H4: Consistency & Standards)_
What I see: The follow-up input at the bottom of the conversation view always shows the send button (blue, prominent). But in the main homepage prompt bar, the send button only appears when there is text input. This is inconsistent behavior for the same interaction pattern.
Why it matters: The homepage pattern is better -- hiding the send button when there is no input reduces visual noise (H8) and prevents accidental empty submissions (H5).
Suggestion: Apply the same show-on-input logic to the follow-up input send button.
**Severity: Minor**

**[m6] Navigation items Sources, Lineage, and Governance are non-functional** _(H1: Visibility of System Status)_
What I see: The top navigation includes Sources, Lineage, and Governance as buttons, but clicking them does nothing (no onclick handler, no route). The sidebar also has dead links to Classifications, Access Policies, Compliance, and Settings.
Why it matters: For a prototype review, non-functional navigation creates confusion about scope. Reviewers will click these and conclude the prototype is broken. Non-functional elements should be clearly marked or should navigate to a placeholder page (like the Security page does).
Suggestion: Either add placeholder screens (matching the Security page pattern) or add a `disabled` visual treatment with a tooltip: "Coming soon" on items that are out of scope.
**Severity: Minor**

**[m7] The AI-Readiness Score ring does not explain its methodology** _(H2: Match Between System & Real World)_
What I see: The ring shows "72 / 100" with six sub-dimensions (Classification 87%, Documentation 62%, etc.) but there is no indication of how the overall 72 is calculated from the sub-scores. Is it a weighted average? Are all dimensions equal?
Why it matters: Data professionals will immediately question the methodology. If they cannot understand or trust the score, they will not act on it. The AI note saying "Documentation is your biggest gap" is helpful, but users may wonder why 62% Documentation matters more than 71% Lineage.
Suggestion: Add an info icon next to "AI-Readiness Score" with a tooltip or expandable section explaining the scoring methodology briefly.
**Severity: Minor**

---

## Laws of UX Analysis

### 1. Hick's Law
The homepage initially presents a manageable number of choices: one prompt bar, five suggestion chips, three insight cards. The progressive disclosure of collapsible sections further reduces decision complexity. However, the Security Posture widget presents six undifferentiated metric cards of equal visual weight, creating a moment of choice paralysis. Users scanning the security grid must evaluate all six to decide which matters most. **Recommendation:** Visually prioritize the 1-2 metrics that need attention (the red and yellow ones) and de-emphasize the healthy ones.

### 2. Miller's Law
The six AI-Readiness sub-dimensions (Classification, Documentation, Freshness, Lineage, Quality, Schema Stability) fall within the 7 plus or minus 2 range and are visible simultaneously, which is appropriate since users are scanning (recognition), not recalling. The trust tags on result items sometimes reach 5 per item, which is at the upper limit but still within working memory for a visible set. No violations detected for pure recall scenarios.

### 3. Fitts's Law
The main prompt bar (52px height, 600px width) is an appropriately large target. The send button (32x32px) is adequate for desktop but borderline for touch (44px minimum recommended). The insight card dismiss buttons (24x24px) are small and only appear on hover, which is a reasonable desktop pattern but would fail on touch devices. The security metric cards are well-sized at approximately 100x60px each. The trust tags (approximately 40x20px) are the smallest interactive elements and represent the primary Fitts's Law risk.

### 4. Doherty Threshold
The 1.2-second skeleton loading delay followed by a 0.6-second onboarding delay, then a 2.2-second AI thinking animation, creates a total first-load experience of approximately 4 seconds. This exceeds the 400ms Doherty Threshold but is mitigated by appropriate feedback at each stage (skeleton, thinking animation). The role selector response is instant (under 100ms), which is appropriate. The concern is that in production, actual AI response times may be 5-15 seconds, and the current 2.2-second simulation may set unrealistic expectations.

### 5. Gestalt: Proximity
The two-column grid layout uses Proximity effectively: left column groups data-readiness widgets (AI-Readiness, Source Health, Recent Activity) while the right column groups risk/governance widgets (Security Posture, Governance Progress, Popular Queries). Within cards, the spacing between header and body (300-500 units) creates appropriate visual separation. One weakness: the "insights footer" ("+5 more insights today / 89% resolved automatically") is spaced far from the insight cards it describes (spacing of 300 + large margin), which could weaken its perceived association with the insight section.

### 6. Gestalt: Common Region
Cards are the primary grouping mechanism and are used consistently. Each widget has a clear boundary (border-subtle, 12px radius). The insight cards use an additional left-border color to encode priority within the common region. The conversation response cards use a footer region (background: surface) to separate actions from content. The blast radius visualization uses a distinct background (bg-surface) to set it apart from the surrounding response text. All appropriate uses of Common Region.

### 7. Von Restorff Effect
The critical insight card (red left border, error icon) effectively stands out from the warning and success cards. The AI sparkle badges on card headers were appropriately reduced to icon-only (no text) to avoid over-emphasis, per a prior round of revision. The primary CTA buttons ("Generate docs for top 50," "Start next task") use the blue interactive-primary color and stand out against the neutral palette. However, the security widget's "47 Unclassified assets" in error-red competes for attention with the critical insight card -- two "red alerts" on one page may dilute the isolation effect.

### 8. Serial Position Effect
In the top navigation, "Home" is first and "Governance" is last -- appropriate placement for primary and aspirational destinations. In the insight cards row, the critical card is first (leftmost), which aligns with the primacy effect. In the governance actions list, the highest-value action (+8 pts) is first and the lowest (+3 pts) is last, which is correct. The suggestion chips place "Where is customer PII?" first, which is a strong, concrete query that teaches the interaction pattern -- good use of primacy.

### 9. Peak-End Rule
The onboarding tour ends by focusing the prompt input and announcing "Welcome tour complete" -- a clean ending that channels users into the primary interaction. The conversation flow ends with a follow-up input, which maintains engagement. However, the governance widget could create a stronger peak moment: when a user completes a task and gains points, there is no celebration or acknowledgment in the current prototype. Adding a micro-animation or success toast when governance points are earned would create a positive peak.

### 10. Zeigarnik Effect
The Governance Progress widget (67% with specific next actions) is the strongest application of the Zeigarnik Effect in the prototype. The incomplete progress bar and itemized actions create a natural pull toward completion. The AI-Readiness Score (72/100) also triggers this effect, especially with the AI note identifying Documentation as the gap. However, the "+5 more insights today" footer link is a weaker application -- it hints at incomplete information but does not create strong enough motivation to expand.

### 11. Jakob's Law
The overall layout follows established enterprise SaaS conventions: top header with logo/nav/profile, sidebar on detail pages, card-based dashboard. The AI prompt bar resembles ChatGPT/Perplexity patterns, which is appropriate for 2026 user expectations. The conversation view with user bubble (right) and assistant response (left/full-width card) follows established AI chat patterns. The catalog table follows standard data table conventions. One violation: the role selector in the header is an uncommon pattern -- most enterprise tools handle role-based views through settings or URL parameters, not a header dropdown. This may confuse users expecting it to be a profile menu.

### 12. Tesler's Law (Conservation of Complexity)
The prototype does well absorbing complexity for the user: the AI-Readiness Score compresses six dimensions into a single number, the blast radius visualization compresses lineage graph analysis into a simple flow diagram, and the governance actions list reduces a complex compliance framework into three prioritized tasks. However, the AI prompt bar pushes complexity to the user by accepting unrestricted free text with no guidance on query boundaries (what can and cannot be asked). Providing structured query templates or a query builder for complex requests would absorb some of this complexity.

### 13. Occam's Razor
The design generally follows Occam's Razor -- each widget earns its place with actionable information. However, the AI sparkle badges on card headers (Security Posture, Governance Progress) are purely decorative since the AI contribution is already communicated through the content itself (AI notes, AI action buttons). Removing these badges entirely would simplify without losing information. Additionally, the "Popular on Your Team" widget has questionable value in its current form -- it duplicates functionality already available through the suggestion chips and could be removed or merged.

### 14. Aesthetic-Usability Effect
The warm neutral palette, polished typography (Proxima Nova), subtle shadows, and micro-interactions (card hover lift, governance action sweep) create a high-quality aesthetic that will generate positive first impressions. This is a strength but also a risk: the visual polish may cause stakeholders to overrate the prototype's readiness and undercount the issues identified in this critique (particularly C1 and C2). Be aware that usability test participants may report the prototype as "easy to use" based on aesthetics while their behavior reveals confusion.

### 15. Paradox of the Active User
The onboarding walkthrough (4 steps) is appropriately brief and can be skipped. The suggestion chips serve as implicit guidance for the prompt bar. However, several concepts are not self-explanatory: "AI-Readiness Score" (readiness for what?), trust tags (what do the abbreviations mean without hovering?), and the governance point system (how do points translate to real outcomes?). The Paradox predicts users will not hover over every trust tag to learn the system -- these need to be self-explanatory at a glance or progressively taught through use.

---

## Quick Wins

1. **Create 2-3 additional response templates** for different query categories (PII, access, compliance) and map them to keyword detection in `askAI()`. This takes the prototype from a single-response demo to a believable multi-scenario prototype. High impact, moderate effort.

2. **Swap suggestion chips and greeting subtitle when role changes.** Even without changing widget content, updating the 5 suggestion chips and the role title in the subtitle ("Data Engineering Lead" to "Executive, Data Office") would make role switching feel meaningful. Low effort, high impact.

3. **Default Recent Activity to expanded** and show the 2 most recent items. Move "Popular on Your Team" to collapsed. This surfaces time-sensitive information without adding scroll depth. Configuration change only.

4. **Wire the Help button** to show a simple dropdown with "Restart tour" and "Keyboard shortcuts" options. This resolves the dead-interaction issue and makes the onboarding re-accessible. Low effort.

5. **Add placeholder screens for Sources, Lineage, and Governance nav items** following the existing Security page pattern (icon + title + "Coming in the next sprint" + Back to Home button). This prevents dead-click confusion during stakeholder reviews. Copy-paste effort.

6. **Standardize "Ask AI" button labels** across all widgets to either "Ask AI" (for generic) or "View details" (for drill-downs). A find-and-replace pass through the HTML.

7. **Add confirmation to "Clear conversation"** using the existing undo-toast pattern. Reuse the toast component that already exists for insight dismissal. Minimal new code.

8. **Hide the send button on the follow-up input** until text is entered, matching the homepage prompt bar behavior. A three-line JavaScript addition.

---

## Severity Summary

| Category | Count | IDs |
|----------|-------|-----|
| Critical | 2 | C1 (static AI response), C2 (cosmetic role switching) |
| Major | 7 | M1 (dual prompt inputs), M2 (no query history), M3 (metrics lack context), M4 (collapsed Recent Activity), M5 (no onboarding re-entry), M6 (no type-ahead), M7 (no clear confirmation) |
| Minor | 7 | m1 (undiscoverable shortcut), m2 (inconsistent labels), m3 (small trust tags), m4 (static hero stats), m5 (inconsistent send button), m6 (dead nav items), m7 (unexplained score methodology) |

---

## Prioritized Recommendations

1. **Fix C1 first:** Add 3-4 response templates mapped to query categories. This is the foundation of the AI-first story and must work for the prototype to be testable.
2. **Fix C2 second:** Make role switching visually change at least the suggestion chips and greeting. The role selector is prominently positioned and will be one of the first things reviewers try.
3. **Fix M1:** Remove or collapse the top prompt bar in conversation view. This is a structural issue that confuses the interaction model.
4. **Fix M4 + Quick Win 3:** Swap collapse defaults so Recent Activity is visible and Popular Queries is hidden.
5. **Fix M5 + Quick Win 4:** Wire the Help button to enable onboarding re-entry.
6. **Address M3:** Add denominators or trends to security metric cards.
7. **Address M6:** Add type-ahead to the prompt bar, even if it only surfaces the existing suggestion chip and popular query text.

---

## Next Steps

- Use `/design-reviewer` to audit token compliance across the prototype (spot-check showed good adherence but there are inline styles on the Security placeholder page)
- Use `/accessibility-auditor` to verify contrast ratios on trust tags (small text on colored backgrounds) and keyboard navigation through the entire flow
- Use `/content-copy-designer` to standardize the AI action button labels and widget descriptions
- Consider a usability test with 3-5 data engineers using the prototype with multiple response templates (post-C1 fix) to validate whether the AI-first interaction model is intuitive or requires more onboarding
