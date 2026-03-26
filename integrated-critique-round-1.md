# Design Critique: AI-Integrated Homepage Prototype
**Round:** 1 of 3
**Stage:** Prototype (interactive HTML)
**Date:** 2026-03-26
**File reviewed:** `prototype-integrated.html`
**Fixed version:** `prototype-integrated-v2.html`

---

## Overall Impression

The AI-first homepage concept is strong. The prompt bar as hero focal point is the right call for an AI-native data platform, and the priority-coded insight cards create clear urgency. The inline conversation mode is well-executed with blast radius visualization. However, the page suffers from information density overload (7 distinct widget sections below the fold), inconsistent AI badging on every card, and several accessibility gaps. Trust tags are a strong concept but rendered at illegibly small sizes. The prototype has good bones but needs discipline around progressive disclosure and visual hierarchy to avoid overwhelming data engineers who come here to act, not read dashboards.

---

## Heuristic Scorecard

| Heuristic | Score | Notes |
|-----------|-------|-------|
| 1. Visibility of System Status | 2/5 | No loading/thinking state when AI queries fire. No feedback on dismiss. No skeleton states. Send button appears but no indication of processing. |
| 2. Match Between System & Real World | 4/5 | Good domain language ("blast radius", "schema drift"). One abbreviation issue ("Yester.", "Changed this wk"). |
| 3. User Control & Freedom | 2/5 | Insight dismiss has no undo. No way to restore dismissed cards. Conversation has back button (good), but no "clear conversation" option. |
| 4. Consistency & Standards | 3/5 | AI badge labels vary needlessly ("AI-Generated", "AI-Monitored", "AI-Powered", "AI-Guided", "AI-Enhanced"). Inline styles used instead of CSS classes for numerous elements. Security cards are divs with onclick, not buttons. |
| 5. Error Prevention | 3/5 | Prompt bar disables send when empty (good). But no error state exists for failed AI queries. No validation on query input. |
| 6. Recognition Rather Than Recall | 4/5 | Suggestion chips and popular queries reduce recall burden (good). Role selector shows current role. Conversation preserves context. |
| 7. Flexibility & Efficiency of Use | 4/5 | Cmd+K shortcut for prompt bar (good). Suggestion chips for common queries. But no keyboard shortcuts for dismissing/acting on insights. |
| 8. Aesthetic & Minimalist Design | 2/5 | 7+ widget sections compete for attention. Every card has an "AI-*" badge creating visual noise. Trust tags at 10px are nearly invisible. Inline styles proliferate. |
| 9. Error Recovery | 2/5 | No error states defined anywhere. If AI fails, user sees nothing. No retry mechanism. No empty states for zero results. |
| 10. Help & Documentation | 3/5 | Help button in header exists. AI annotations provide context. But no onboarding for first-time users. No tooltips on trust tags explaining what "Q:94" means. |

**Overall: 2.9 / 5**

---

## Laws of UX Findings

| Law | Observation | Severity |
|-----|-------------|----------|
| **Hick's Law** | 7+ widget sections with no progressive disclosure means too many choices on a single page. Users must decide where to look and what to act on. | High |
| **Miller's Law** | Dashboard presents 6 readiness dimensions + 6 security cards + 3 insight cards + 3 governance actions + 5 popular queries simultaneously. Well beyond working memory limits. | High |
| **Fitts's Law** | Trust tags at 10px with 1px padding are extremely small touch/click targets. Source breach action buttons also too small (2px 8px padding). | High |
| **Law of Proximity** | Insight cards section and two-column grid lack clear visual separation. The transition from full-width to two-column feels abrupt. | Medium |
| **Von Restorff Effect** | When every card has an "AI-*" badge, nothing stands out. The distinctiveness of AI features is diluted by over-labeling. | Medium |
| **Doherty Threshold** | No thinking/loading animation when AI processes a query. Users click and get transported to a conversation with pre-rendered results -- no sense of computation happening. | High |
| **Jakob's Law** | Security cards use div+onclick instead of buttons. Users expect interactive elements to be focusable and keyboard-accessible. | High |
| **Zeigarnik Effect** | Governance progress bar (67%) with point values is well-done. Creates healthy completion motivation. | Positive |
| **Goal-Gradient Effect** | Governance "Next actions" with point rewards is excellent -- accelerates effort near goals. | Positive |
| **Selective Attention** | AI badges on every card become invisible through repetition (banner blindness). Users will stop noticing them. | Medium |

---

## What's Working Well

1. **AI prompt bar as hero focal point** _(Recognition Rather Than Recall)_ -- centering the AI experience as the primary interaction surface is the right architecture. The gradient background, elevated card treatment, and Cmd+K shortcut make it clearly the #1 affordance.

2. **Priority-coded insight cards** _(Visibility of System Status)_ -- critical/warning/success with left border color coding and icon differentiation gives immediate triage capability. The inline "Ask AI" buttons on each card create direct action paths.

3. **Blast radius visualization** _(Match Between System & Real World)_ -- the linear flow diagram (source -> models -> dashboards -> queries) maps perfectly to how data engineers think about impact. Trust tags on conversation results add credibility.

4. **Gamified governance progress** _(Zeigarnik Effect + Goal-Gradient Effect)_ -- the 67% progress bar with point-valued actions creates clear motivation. "Start next task" CTA reduces decision friction.

5. **Inline conversation mode** _(Flexibility & Efficiency of Use)_ -- transitioning from dashboard to conversation without losing context is well-handled. The back button and persistent prompt bar maintain orientation.

6. **Role selector** _(Flexibility & Efficiency of Use)_ -- personalization by role (Engineer, Analyst, Steward, Security, Executive) with descriptive subtitles for each role.

---

## Areas for Improvement

### Issue 1: No Loading/Thinking State for AI Interactions
_(Heuristic: Visibility of System Status | Law: Doherty Threshold)_

**What I see:** When users click "Ask AI", suggestion chips, or any AI-triggering action, they are immediately transported to the conversation screen with fully rendered results. There is no indication that AI is processing -- no thinking animation, no skeleton state, no progressive reveal.

**Why it matters:** The Doherty Threshold tells us responses under 400ms feel instant, but AI responses in production will take 2-10 seconds. Without a thinking state, users will perceive the system as broken. The lack of feedback violates Visibility of System Status -- users cannot tell if their query was received.

**Fix applied in v2:** Added `.ai-thinking` component with animated dot pulse, `aria-live="polite"` region for screen reader announcements, and `askAI()` function now announces query status.

---

### Issue 2: Insight Card Dismiss Has No Undo
_(Heuristic: User Control & Freedom)_

**What I see:** Dismissing an insight card triggers a fade-out animation and `display: none` with no way to recover. The action is immediate and irreversible.

**Why it matters:** Users make mistakes. Accidental dismissal of a critical insight (schema drift affecting production) could mean missing an important alert. User Control & Freedom requires clearly marked "emergency exits" -- undo is the standard pattern for reversible actions.

**Fix applied in v2:** Added undo toast notification that appears for 6 seconds after dismissal. "Undo" button restores the dismissed card. Includes `aria-live` announcement for screen reader users.

---

### Issue 3: Security Cards Are Not Keyboard Accessible
_(Heuristic: Consistency & Standards | Law: Jakob's Law)_

**What I see:** The 6 security metric cards use `<div>` elements with `onclick` handlers. They cannot be reached via keyboard Tab navigation, have no focus indicators, and announce nothing to screen readers.

**Why it matters:** Jakob's Law says users expect interactive elements to behave like standard interactive elements. Clickable elements must be `<button>` or `<a>` with proper ARIA semantics. This is both a usability and WCAG AA compliance issue.

**Fix applied in v2:** Converted all 6 security cards from `<div>` to `<button>` with descriptive `aria-label` attributes. Added `:focus-visible` styling. Added `role="group"` wrapper with `aria-label`.

---

### Issue 4: Trust Tags Are Too Small to Read
_(Heuristic: Aesthetic & Minimalist Design | Law: Fitts's Law)_

**What I see:** Trust tags render at 10px font size with 1px vertical padding. On a standard display, these are barely legible. At this size, "Q:94" and "AI:68" require squinting and provide minimal information scent.

**Why it matters:** Fitts's Law states that small targets are harder to acquire. At 10px, trust tags fail to serve their purpose -- they exist to build trust in data quality, but users cannot read them without effort. The tags become visual noise rather than useful signals.

**Fix applied in v2:** Increased trust tag font size from 10px to `var(--sds-type-overline)` (11px) and vertical padding from 1px to 2px. Still compact but now legible.

---

### Issue 5: AI Badge Noise on Every Card
_(Heuristic: Aesthetic & Minimalist Design | Law: Von Restorff Effect, Selective Attention)_

**What I see:** Every card widget has a different "AI-*" badge: "AI-Generated", "AI-Monitored", "AI-Powered", "AI-Guided", "AI-Enhanced". There are 5 distinct labels for essentially the same concept.

**Why it matters:** The Von Restorff Effect states that visually distinct items are remembered -- but when everything is "AI-something," nothing is distinct. Through banner blindness (Selective Attention), users will learn to ignore these badges entirely. The inconsistent labeling also violates Consistency & Standards.

**Recommendation for Round 2:** Consolidate to a single, subtle AI indicator (sparkle icon only, no text) or remove entirely from the homepage where AI is already the defining feature. The homepage *is* AI -- labeling every widget as AI-powered is redundant.

---

### Issue 6: Information Density Overload
_(Heuristic: Aesthetic & Minimalist Design | Law: Hick's Law, Miller's Law)_

**What I see:** Below the hero, the homepage presents: (1) 3 insight cards, (2) AI-Readiness Score with 6 dimensions, (3) Source Health with SLA breaches, (4) Recent Activity with 4 timeline items, (5) Security Posture with 6 metric cards, (6) Governance Progress with 3 action items, (7) Popular Team Queries with 5 items. That is 7 distinct widget sections.

**Why it matters:** Hick's Law: decision time increases logarithmically with the number of choices. Miller's Law: working memory holds 7 plus or minus 2 items. When an engineer lands on this page, they face 30+ data points and 15+ action buttons competing for attention. This creates cognitive paralysis, not empowerment.

**Recommendation for Round 2:** Apply progressive disclosure. Show the top 3-4 sections by default and allow users to configure which widgets appear. Consider collapsible sections or a "Show more" pattern for the two-column grid.

---

### Issue 7: Role Dropdown Missing ARIA and Escape Key
_(Heuristic: Consistency & Standards | Law: Jakob's Law)_

**What I see:** The role selector dropdown has no `aria-haspopup`, no `aria-expanded`, no `aria-controls`, no `role="listbox"` on the dropdown, no `role="option"` on items, and no Escape key to close.

**Why it matters:** Custom dropdowns must replicate native select behavior for keyboard and screen reader users. Without ARIA attributes, the dropdown is invisible to assistive technology. Without Escape key handling, keyboard users are trapped.

**Fix applied in v2:** Added `aria-haspopup="listbox"`, `aria-expanded` (toggled dynamically), `aria-controls`, `role="listbox"`, `role="option"` with `aria-selected`, and Escape key handler that returns focus to the trigger button.

---

### Issue 8: Abbreviations and Truncations
_(Heuristic: Match Between System & Real World)_

**What I see:** "Yester." for "Yesterday" in the activity timeline. "Changed this wk" in security cards. These save minimal space but introduce ambiguity.

**Why it matters:** Match Between System & Real World requires speaking the user's language. Abbreviations create unnecessary cognitive load -- users must decode them. In a security context especially, ambiguity can lead to misunderstanding.

**Fix applied in v2:** "Yester." changed to "Yesterday". "Changed this wk" changed to "Changed this week".

---

### Issue 9: No Error States Defined
_(Heuristic: Help Users Recognize, Diagnose, and Recover from Errors)_

**What I see:** No error state exists for failed AI queries. No empty state for zero results. No network error handling. If the AI service is down, users would see a blank conversation screen.

**Why it matters:** In production, AI services fail. Network connections drop. Queries return no results. The absence of error handling makes these inevitable failures catastrophic for user trust. Error messages should be in plain language, identify the problem, and suggest recovery.

**Fix applied in v2:** Added `.ai-error-state` component CSS with icon, heading, description, and retry action pattern. Ready for implementation in the conversation flow.

---

### Issue 10: Extensive Inline Styles
_(Heuristic: Consistency & Standards)_

**What I see:** Over 20 inline `style` attributes across the prototype for colors, font sizes, padding, and layout. Examples: source health status text colors, quality score percentages, breach status text, security score unit text, governance next label, conversation animation delays, catalog owner cells.

**Why it matters:** Inline styles defeat the design system. They cannot be overridden by media queries, are invisible to design token audits, and create maintenance debt. Every inline style is a consistency risk.

**Fix applied in v2:** Replaced all non-data-driven inline styles with proper CSS classes: `.source-status-text-success`, `.source-status-text-warning`, `.source-status-text-error`, `.breach-status`, `.security-score-unit`, `.quality-pct`, `.result-item-usage`, `.result-tag-error`, `.result-tag-warning`, `.catalog-cell-owner`, `.catalog-th-actions`, `.btn-xs`, `.source-badge-databricks`, `.source-badge-s3`, `.governance-next-label`, `.home-hero--compact`, `.conv-msg-delay-1`, `.conv-msg-delay-2`. Only data-driven `width` percentages for progress bars remain inline.

---

### Issue 11: No Focus Management on Screen Transitions
_(Heuristic: Consistency & Standards | Accessibility)_

**What I see:** When navigating between screens (home -> conversation -> catalog), keyboard focus is not managed. Screen reader users receive no announcement of the new context. After an AI query triggers navigation to the conversation screen, focus remains where it was.

**Why it matters:** WCAG 2.1 requires that focus is managed during dynamic content changes. Without focus management, keyboard users lose their place, and screen reader users do not know the page has changed.

**Fix applied in v2:** Added `aria-live="polite"` region that announces navigation changes. `askAI()` moves focus to the conversation back button after transition. `activateScreen()` announces the new screen heading.

---

## Quick Wins

1. **Increase trust tag font size** -- 10px to 11px, instant readability improvement (done in v2)
2. **Add undo toast for insight dismiss** -- standard pattern, high user confidence impact (done in v2)
3. **Convert security cards to buttons** -- 6 elements, immediate keyboard access (done in v2)
4. **Fix abbreviations** -- "Yester." to "Yesterday", "this wk" to "this week" (done in v2)
5. **Add Escape key to role dropdown** -- standard dropdown behavior (done in v2)

---

## Summary of Changes in v2

| Category | Count | Description |
|----------|-------|-------------|
| Accessibility | 6 | ARIA live region, security card buttons, role dropdown ARIA, focus management, screen announcements, aria-label on security grid |
| User Control | 2 | Undo toast for insight dismiss, Escape key for role dropdown |
| System Status | 2 | AI thinking state CSS, AI status announcements |
| Error Handling | 1 | Error state component CSS |
| Design System | 2 | Hero heading uses --sds-type-display token, trust tags use --sds-type-overline |
| Consistency | 22 | Replaced all non-data inline styles with CSS classes |
| Content | 2 | Fixed abbreviations ("Yester." and "this wk") |

**Total fixes applied:** 37

---

## Remaining for Round 2

- [ ] Reduce information density with progressive disclosure (collapsible sections)
- [ ] Consolidate or remove AI badges (Von Restorff violation)
- [ ] Add actual thinking/skeleton animation to conversation transition
- [ ] Add error state rendering in conversation flow
- [ ] Add tooltips to trust tags explaining abbreviations (Q:94, AI:68)
- [ ] Consider empty states for each widget
- [ ] Add first-run onboarding experience
- [ ] Test color contrast ratios on all trust tag variants
- [ ] Responsive layout audit (mobile/tablet breakpoints)
- [ ] Add keyboard shortcuts for insight card actions
