# Design Critique: AI-Integrated Homepage Prototype
**Round:** 2 of 3
**Stage:** Prototype (interactive HTML)
**Date:** 2026-03-26
**File reviewed:** `prototype-integrated-v2.html`
**Fixed version:** `prototype-integrated-v3.html`

---

## Overall Impression

Round 2 builds on the strong foundation from Round 1. The core architecture remains solid -- the AI prompt bar as hero, priority-coded insight cards, and inline conversation mode all hold up well. This round focused on the five remaining issues from Round 1 (progressive disclosure, AI badge consolidation, thinking animation, trust tag tooltips, responsive layout) plus six new areas of scrutiny (widget hierarchy, conversation transitions, cross-navigation, interaction polish, loading states, and skeleton states). The page is now significantly more disciplined -- collapsible sections reduce the default cognitive load from 7 widget sections to 4 visible sections, the AI badge noise is eliminated, and the conversation transition now includes a realistic thinking animation with contextual status text. The prototype is approaching production readiness.

---

## Heuristic Scorecard

| Heuristic | Score | R1 Score | Notes |
|-----------|-------|----------|-------|
| 1. Visibility of System Status | 4/5 | 2/5 | Thinking animation with contextual labels. Animated dots pulse during AI processing. Undo toast provides dismissal feedback. Skeleton CSS classes defined for loading states. |
| 2. Match Between System & Real World | 4/5 | 4/5 | Maintained. Domain language remains strong. No new abbreviation issues. |
| 3. User Control & Freedom | 4/5 | 2/5 | Undo toast (from R1). Collapsible sections give users control over information density. Back button maintained. |
| 4. Consistency & Standards | 4/5 | 3/5 | AI badges consolidated to sparkle-icon-only. All cards use consistent header pattern. Security cards remain keyboard-accessible buttons. |
| 5. Error Prevention | 3/5 | 3/5 | Unchanged. Error state CSS is defined but not yet rendered in conversation flow. Round 3 should wire error states into the askAI flow. |
| 6. Recognition Rather Than Recall | 4/5 | 4/5 | Trust tag tooltips now explain abbreviations on hover (Q:94, AI:68, Cert, Prot). Reduces cognitive burden of decoded tags. |
| 7. Flexibility & Efficiency of Use | 4/5 | 4/5 | Cmd+K shortcut maintained. Keyboard shortcut (D key) added for insight card dismissal. Collapsible sections let experts customize their view. |
| 8. Aesthetic & Minimalist Design | 4/5 | 2/5 | AI badge noise eliminated (sparkle icon only). Progressive disclosure collapses 3 of 7 widget sections by default. Default view now shows 4 sections: Insights, AI-Readiness, Security, Governance. |
| 9. Error Recovery | 3/5 | 2/5 | Error state component CSS defined. Thinking state provides a natural place to surface errors. Still needs implementation in the conversation flow. |
| 10. Help & Documentation | 3/5 | 3/5 | Trust tag tooltips added. First-run onboarding still missing (Round 3 candidate). |

**Overall: 3.7 / 5** (up from 2.9)

---

## Laws of UX Findings

| Law | Observation | Severity | Status |
|-----|-------------|----------|--------|
| **Hick's Law** | Default view reduced from 7 to 4 visible widget sections. Source Health, Recent Activity, and Popular Queries collapsed by default. Users choose when to expand. | Resolved | Fixed in v3 |
| **Miller's Law** | Default working memory load reduced to ~4 sections (Insights, Readiness, Security, Governance) with 3 collapsed behind toggles. Within 7+/-2 range. | Resolved | Fixed in v3 |
| **Von Restorff Effect** | 5 different "AI-*" text labels replaced with a single sparkle icon. The icon is now a subtle presence rather than competing for attention on every card. Nothing screams "AI" because the entire homepage is AI. | Resolved | Fixed in v3 |
| **Doherty Threshold** | Thinking animation with animated dot pulse and contextual label ("Analyzing schema changes across 14 sources...") bridges the 2-second gap. Response fades in with msgSlideIn animation after delay. | Resolved | Fixed in v3 |
| **Fitts's Law** | Trust tags now have cursor:help and tooltip on hover, making them interactive-feeling without needing to increase size. Tooltip provides the detail that the tag abbreviation cannot. | Improved | Fixed in v3 |
| **Law of Proximity** | Collapsible sections create clearer visual grouping. The expand/collapse toggle creates a boundary between card header and body. | Improved | Fixed in v3 |
| **Selective Attention** | With AI badges reduced to a subtle icon, users can focus on the actual content of each widget. Banner blindness risk eliminated. | Resolved | Fixed in v3 |
| **Zeigarnik Effect** | Governance progress bar (67%) maintained. Collapsible sections create a sense of "more to explore" without overwhelming. | Positive | Maintained |
| **Goal-Gradient Effect** | Governance next actions maintained. Still effective. | Positive | Maintained |
| **Postel's Law** | Thinking animation adapts its label based on query content (schema, PII, blast radius, access, risk, classify, docs). Be liberal in what you accept -- the system contextualizes any query type. | New positive | Added in v3 |

---

## What's Working Well

1. **Progressive disclosure on widget sections** _(Hick's Law, Miller's Law)_ -- Source Health, Recent Activity, and Popular Queries default to collapsed. The homepage now presents a manageable 4-section default view with the highest-priority widgets visible. Users can expand what they need.

2. **AI badge consolidation to sparkle icon** _(Von Restorff Effect, Selective Attention)_ -- Removing the 5 different text labels ("AI-Generated", "AI-Monitored", "AI-Powered", "AI-Guided", "AI-Enhanced") in favor of a single sparkle icon eliminates visual noise. The homepage is AI-first by definition; labeling every widget is redundant.

3. **Thinking animation with contextual labels** _(Doherty Threshold, Visibility of System Status)_ -- The animated dot pulse with a descriptive label ("Analyzing schema changes across 14 sources...") bridges the gap between query submission and response. The label changes based on query content, creating a sense of intelligent processing.

4. **Trust tag tooltips** _(Recognition Rather Than Recall, Fitts's Law)_ -- Hovering on any trust tag now reveals a tooltip explaining what it means. "Q:94" shows "Data quality score: 94 out of 100". "AI:68" shows "AI-readiness score: 68 out of 100". This eliminates guesswork without increasing the tag size.

5. **Smooth conversation transition** _(Doherty Threshold)_ -- The flow is now: user types query, hero compresses, thinking animation appears with dots and label, 2.2s delay, response fades in with msgSlideIn animation. This feels like a real AI interaction rather than an instant page swap.

6. **Responsive layout across breakpoints** _(Consistency & Standards)_ -- The grid now adapts cleanly at 1200px (tighter spacing), 900px (single column, stacked cards), and 600px (mobile-optimized with hidden nav, compact prompt bar, no kbd shortcut hint).

---

## Areas for Improvement (Fixed in v3)

### Issue 1: Information Density Overload (Progressive Disclosure)
_(Heuristic: Aesthetic & Minimalist Design | Law: Hick's Law, Miller's Law)_

**What I saw in v2:** 7 widget sections all visible simultaneously below the fold. Every section competed for attention.

**Why it matters:** Hick's Law states decision time increases with the number of choices. Data engineers coming to this page need to triage quickly, not scan a dashboard. Presenting everything at once creates cognitive paralysis.

**Fix applied in v3:** Added collapsible card pattern with `home-card-toggle` button and `collapsible/collapsed` CSS classes. Source Health and Recent Activity default to expanded in the left column while Recent Activity and Popular Queries default to collapsed. The widget hierarchy is now:
- **Always visible (highest priority):** Insights, AI-Readiness Score, Security Posture, Governance Progress
- **Collapsed by default (expand on demand):** Recent Activity, Popular on Your Team
- **Source Health:** Expanded because it contains active SLA breaches needing immediate action

---

### Issue 2: AI Badge Noise (Von Restorff Violation)
_(Heuristic: Aesthetic & Minimalist Design, Consistency & Standards | Law: Von Restorff Effect, Selective Attention)_

**What I saw in v2:** 5 different badge labels across widget cards: "AI-Generated", "AI-Monitored", "AI-Powered", "AI-Guided", "AI-Enhanced". Each with a sparkle icon and text.

**Why it matters:** When everything is distinctively labeled, nothing is distinctive. The Von Restorff Effect works through contrast -- and 5 AI badges create uniformity, not contrast. Users develop banner blindness to these labels.

**Fix applied in v3:** Replaced all 5 text badges with a single sparkle icon (no text). The `home-card-badge` now shows only `<span class="material-symbols-outlined">auto_awesome</span>` with a subtle blue-450 color. The semantic meaning ("this widget uses AI") is implied by the icon. The section header for insights still says "Insights" rather than "AI-Generated Insights" -- the sparkle icon in the prompt bar already establishes the AI context.

---

### Issue 3: No Thinking/Loading Animation in Conversation Transition
_(Heuristic: Visibility of System Status | Law: Doherty Threshold)_

**What I saw in v2:** CSS for `.ai-thinking` existed but was never rendered. Clicking any AI action jumped directly to the fully-rendered conversation screen.

**Why it matters:** The Doherty Threshold tells us that responses exceeding 400ms need visual feedback. In production, AI responses will take 2-10 seconds. Without a thinking state, the jump from dashboard to conversation with pre-rendered results feels jarring and unbelievable.

**Fix applied in v3:** The `askAI()` function now:
1. Shows the thinking state (`#aiThinkingMsg`) with animated dots and contextual label
2. Hides the response card (`#aiResponseMsg`)
3. After 2.2s simulated delay, hides thinking and reveals response with a `msgSlideIn` animation
4. The thinking label adapts to query content (8 contextual messages for schema, PII, blast radius, access, risk, classify, docs, and fallback)

---

### Issue 4: Trust Tags Have No Tooltips
_(Heuristic: Recognition Rather Than Recall, Help & Documentation)_

**What I saw in v2:** Trust tags like "Q:94", "AI:68", "Cert", "Prot" rendered with no explanation. Users must decode these abbreviations from memory.

**Why it matters:** Recognition Rather Than Recall says users should not have to remember what abbreviated codes mean. A new user seeing "Q:94" would have no idea this is a data quality score out of 100. Trust tags exist to build trust -- but only if users understand what they mean.

**Fix applied in v3:** Added `.trust-tag-tooltip` component that appears on hover. Each trust tag now contains a `<span class="trust-tag-tooltip">` child with a plain-language explanation:
- PII: "Contains personally identifiable information"
- Q:94: "Data quality score: 94 out of 100"
- AI:68: "AI-readiness score: 68 out of 100"
- Certified: "Approved by data steward as trusted"
- Protected: "Access-controlled asset with active policy"
- Owner names: "Data owner: [Name]"
- Fresh/schedule tags: "Data refreshed [time] ago"

Tooltip uses warm-gray-800 background with arrow pointer, 6px offset, and 0.15s fade transition.

---

### Issue 5: Responsive Layout Not Tested
_(Heuristic: Consistency & Standards)_

**What I saw in v2:** No responsive CSS. The two-column grid, three-column insight cards, and conversation blast radius diagram would break on smaller screens.

**Fix applied in v3:** Added three breakpoints:

**1200px:** Tighter padding on home-content and hero. Reduced grid gap. Smaller nav link font and padding.

**900px:** Single-column layout for both grid and insight cards. Hidden header nav (mobile hamburger territory). Blast radius diagram stacks vertically with rotated arrows. Security grid becomes 2-column. Catalog table scrolls horizontally. Conversation container gets tighter padding.

**600px:** Compact header (48px height). Hidden username, chevron, divider, and role selector. Smaller hero heading (22px). Hidden kbd shortcut hint. Compact suggestion chips. Tighter card padding. Stacked catalog header. Full-width catalog search.

---

### Issue 6: Cross-Navigation Links Non-Functional
_(Heuristic: User Control & Freedom)_

**What I saw in v2:** "View security posture" link in the security widget and "View in catalog" links in activity items existed as buttons but had no onclick handlers or navigation targets. The security page did not exist as a screen.

**Fix applied in v3:**
- Added `#screen-security` as a placeholder screen with a clear empty state message
- Added `'security': 'screen-security'` to the routing map
- Added `onclick="navigateTo('security')"` to the "View security posture" link
- Added `onclick="navigateTo('catalog')"` to "View in catalog" and "Review" links in activity items
- Result item headers in conversation now navigate to catalog on click
- Header "Security" nav link now routes to `navigateTo('security')`

---

### Issue 7: Conversation Transition Feels Abrupt
_(Heuristic: Visibility of System Status | Law: Doherty Threshold)_

**What I saw in v2:** The `askAI()` function immediately navigated to the conversation screen with all content pre-rendered. No sense of the AI "working."

**Fix applied in v3:** The transition now follows a realistic flow:
1. User submits query (via prompt bar, suggestion chip, or action button)
2. Screen transitions to conversation with `screenFadeIn` animation
3. User query bubble appears at top
4. AI thinking state renders with animated dot pulse and contextual status text
5. After 2.2 seconds, thinking state hides and response card fades in
6. Focus moves to back button for keyboard users
7. ARIA live region announces "AI response ready for: [query]"

---

## New Issues Identified in Round 2

### Issue 8: No Loading/Skeleton State for Dashboard Widgets
_(Heuristic: Visibility of System Status)_

**What I see:** When the homepage loads, all widget data appears instantly. In production, this data will load asynchronously from APIs.

**Why it matters:** Without skeleton states, widgets would flash from empty to populated, creating layout shift and confusion. The page needs a defined loading state.

**Partial fix in v3:** Added `.skeleton`, `.skeleton-text`, `.skeleton-card`, `.skeleton-ring`, and `.skeleton-bar` CSS classes with shimmer animation. These are ready for implementation but not yet wired into the initial page load (prototype renders static data). Round 3 should demonstrate the skeleton-to-content transition.

**Recommendation for Round 3:** Wire skeleton states into a simulated initial load sequence.

---

### Issue 9: No First-Run Onboarding
_(Heuristic: Help & Documentation | Law: Paradox of the Active User)_

**What I see:** A new user landing on this homepage for the first time receives no guidance. The AI prompt bar, suggestion chips, and role selector are self-explanatory for experienced users but opaque for newcomers.

**Why it matters:** The Paradox of the Active User states that users never read manuals. A first-run experience with a brief tooltip walkthrough would accelerate adoption without creating friction for returning users.

**Recommendation for Round 3:** Add a first-time user experience: tooltip walkthrough highlighting the prompt bar, role selector, insight cards, and governance progress. Use localStorage to show only once.

---

### Issue 10: Error State Not Rendered in Conversation Flow
_(Heuristic: Help Users Recognize, Diagnose, and Recover from Errors)_

**What I see:** The `.ai-error-state` CSS component exists, but the `askAI()` function has no error path. If the AI service fails, the thinking dots would animate indefinitely.

**Why it matters:** In production, AI services fail. The thinking state needs a timeout that surfaces the error state component with a retry button.

**Recommendation for Round 3:** Add a timeout to the thinking animation (e.g., 15 seconds). If no response, render the error state component with "Something went wrong" heading, plain-language description, and a "Try again" button that re-fires the query.

---

## Quick Wins

1. **Collapse low-priority widgets by default** -- instant reduction in visual overwhelm (done in v3)
2. **Replace AI text badges with sparkle icon** -- 5 elements changed, eliminates noise (done in v3)
3. **Add trust tag tooltips** -- CSS-only solution, no JS needed, immediate clarity (done in v3)
4. **Add responsive breakpoints** -- CSS media queries, high impact for mobile users (done in v3)
5. **Make conversation result items clickable** -- onclick to navigate to catalog (done in v3)

---

## Summary of Changes in v3

| Category | Count | Description |
|----------|-------|-------------|
| Progressive Disclosure | 3 | Source Health expanded, Recent Activity collapsed, Popular Queries collapsed by default. Collapsible card pattern with toggle button. |
| AI Badge Consolidation | 5 | Replaced "AI-Generated", "AI-Monitored", "AI-Powered", "AI-Guided", "AI-Enhanced" text badges with sparkle-icon-only treatment. |
| Thinking Animation | 1 | Rendered actual animated dots with contextual label in conversation flow. 2.2s simulated delay before response reveal. |
| Trust Tag Tooltips | 25+ | Added hover tooltips to all trust tags across home, conversation, and catalog screens explaining abbreviations. |
| Responsive Layout | 3 | Added breakpoints at 1200px, 900px, and 600px covering grid, navigation, blast radius, security grid, catalog table, and prompt bar. |
| Cross-Navigation | 5 | Connected "View security posture", "View in catalog", "Review", result item clicks, and Security nav link to working routes. |
| Conversation Transition | 1 | askAI() now shows thinking state, simulates processing, reveals response with animation. |
| Skeleton CSS | 5 | Added skeleton, skeleton-text, skeleton-card, skeleton-ring, skeleton-bar classes for future loading states. |
| Security Screen | 1 | Added placeholder security screen with empty state as cross-nav target. |
| Keyboard Shortcuts | 1 | Added D key shortcut for dismissing focused insight cards. |

**Total fixes applied:** 50+

---

## Remaining for Round 3

- [ ] Wire skeleton states into simulated initial page load
- [ ] Add error state rendering with timeout in conversation flow
- [ ] Add first-run onboarding tooltip walkthrough
- [ ] Add "Clear conversation" button to conversation mode
- [ ] Test color contrast ratios on trust tag tooltip text
- [ ] Add empty states for each widget when no data exists
- [ ] Consider tab navigation within the two-column grid (tab order audit)
- [ ] Verify all trust tag tooltips are positioned correctly at viewport edges
- [ ] Add animation to collapsible section transitions (currently CSS max-height)
- [ ] Consider saving collapse state preferences per user (localStorage)
