# Design Critique — Round 1: AI Insight Agents Prototype

**Date:** 2026-03-25
**Artifact:** `prototype-v1.html` (6 screens)
**Stage:** Interactive prototype
**Output:** `prototype-v2.html` (fixed version)

---

## Overall Impression

The prototype demonstrates strong foundational design work. The insight card pattern with inline actions, confidence levels, and progressive reasoning disclosure directly addresses key research findings around reducing response time and building trust through transparency. The design system compliance is excellent — SDS tokens are used consistently throughout, and the warm neutral + cool accent palette creates the right approachable-yet-professional tone for an enterprise data management tool.

However, several heuristic violations undermine the experience in ways that would erode user trust and create friction in daily use. The most critical gaps are around user control (no undo for dismissals, no confirmation for destructive actions), accessibility (missing keyboard support, ARIA roles), and alert fatigue management (all insights presented at equal visual weight regardless of priority).

---

## Heuristic Scorecard (Aggregate)

| # | Heuristic | Score | Summary |
|---|-----------|-------|---------|
| H1 | Visibility of System Status | 3/5 | Good confidence bars and status tags; missing loading states, no feedback on settings changes, no save confirmation |
| H2 | Match Between System and Real World | 4/5 | Language is well-matched to data engineering persona; "Blast radius" is excellent domain language; minor jargon in some areas |
| H3 | User Control and Freedom | 2/5 | No undo on dismiss, no confirmation on destructive actions, no filter reset, autonomy dial not interactive |
| H4 | Consistency and Standards | 4/5 | Excellent SDS compliance; minor inconsistencies in icon weights and button patterns between screens |
| H5 | Error Prevention | 2/5 | "Revoke write access" and "Approve all" have no confirmation; dismiss is permanent with no safety net |
| H6 | Recognition Rather Than Recall | 4/5 | Good use of inline metadata, confidence bars, and blast radius visualization; sidebar badge missing |
| H7 | Flexibility and Efficiency of Use | 3/5 | Global search has keyboard shortcut; missing focus-visible styles, no bulk actions on Focus screen |
| H8 | Aesthetic and Minimalist Design | 3/5 | Clean layout but all insights presented at equal visual weight; info-level cards compete with critical ones for attention |
| H9 | Help Users Recover from Errors | 3/5 | No undo toasts, no error recovery paths shown in prototype |
| H10 | Help and Documentation | 3/5 | No empty states for filtered views; no onboarding guidance; no contextual help on settings controls |

**Overall: 3.1 / 5**

---

## Per-Screen Findings

### Screen 1: Home (Dashboard)

**What's working well:**
- Greeting with role context and "5 new insights" count reduces cognitive load (H6, Serial Position Effect)
- Stat cards provide at-a-glance system health — maps to CDO/Marcus's needs
- Insight cards with inline actions reduce context switching (research: 42% faster response time)
- Progressive reasoning disclosure ("Show reasoning" toggle) perfectly implements the summary-to-detail pattern from research
- Confidence bars and blast radius create trust through transparency
- Right sidebar with activity log and autonomy dial gives system status at a glance (H1)

**Issues found and fixed:**

**[C1] No undo on insight dismissal** _(H3: User Control, H5: Error Prevention)_
- Location: "Dismiss" button on all insight cards
- Problem: Clicking Dismiss permanently removes an insight with no way to recover it. Users who accidentally dismiss a critical insight have no recourse. Research emphasizes that trust requires reversibility.
- Fix: Added `dismissInsight()` function that animates the card away and shows an 8-second undo toast with timer. Card can be restored via undo.

**[C2] Destructive "Revoke write access" has no confirmation** _(H5: Error Prevention)_
- Location: Insight card #4 (Access Anomaly)
- Problem: Revoking production write access is a high-impact, potentially irreversible action presented as a single-click button with no confirmation step.
- Fix: Added confirmation dialog with clear description of impact and a Cancel option. Dialog closes on backdrop click and Escape key.

**[M1] Info-level insights compete visually with critical ones** _(H8: Aesthetic and Minimalist Design, Von Restorff Effect)_
- Location: Home insight card list
- Problem: All 5 insight cards have the same visual weight and expand state. The info-level documentation insight competes for attention with the critical schema drift insight. This contributes to the alert fatigue identified in research as the #1 complaint.
- Fix: Info-priority cards now start collapsed (title + priority tag visible, details hidden behind "Show details" link). Critical and warning cards remain fully expanded.

**[M2] Autonomy dial in sidebar is display-only** _(H1: Visibility of System Status)_
- Location: Right sidebar autonomy widget
- Problem: The autonomy dial shows current level but has no interactive affordance. Users can see "Smart auto-triage" but can't adjust it from the home screen. This creates a mismatch between the visual suggestion of control and actual capability.
- Recommendation for Round 2: Make dial clickable to navigate to Settings, or add direct level adjustment.

**[m1] Sidebar lacks badge for pending insights** _(H1: Visibility of System Status)_
- Location: Left sidebar "Focus" nav item
- Problem: No indication of how many critical insights need attention without navigating to the Focus screen.
- Fix: Added red badge showing "2" (critical count) next to Focus nav item.

---

### Screen 2: Focus (All Active Insights)

**What's working well:**
- Tab bar with counts (Active: 8, Resolved: 24) provides clear system status (H1)
- Filter chips for Priority and Category support efficient triage (H7)
- Sort dropdown with meaningful options (Priority, Newest, Blast radius, Confidence) matches mental models (H2)
- Insight cards maintain consistency with Home screen (H4)

**Issues found and fixed:**

**[M3] Filter chips lack mutual exclusivity with "All"** _(H4: Consistency, H5: Error Prevention)_
- Location: Priority filter group
- Problem: "All" can remain active while individual priority filters are also selected, creating an ambiguous state. Users can end up with "All" + "Critical" both highlighted — does that mean all, or just critical?
- Fix: Added JavaScript to enforce mutual exclusivity: selecting "All" clears individual filters, selecting any individual filter deselects "All", and if all individual filters are deselected "All" re-activates.

**[M4] No "Clear filters" option** _(H3: User Control and Freedom)_
- Location: Filter bar
- Problem: After applying multiple filters, users must manually deselect each one to return to the unfiltered view. No single action to reset.
- Fix: Added "Clear filters" text button that appears when any non-default filter is active.

**[m2] Sort dropdown lacks aria-label** _(Accessibility)_
- Location: Sort select element
- Problem: Screen readers announce the selected option but not the purpose of the control.
- Fix: Added `aria-label="Sort insights by"`.

**[m3] No empty state for "Resolved" tab** _(H10: Help and Documentation)_
- Recommendation for Round 2: When the Resolved tab shows no items (e.g., fresh account), show an empty state with guidance.

---

### Screen 3: Find (Natural Language Search)

**What's working well:**
- Hero search pattern with large input field follows established search UX conventions (Jakob's Law)
- Example prompt chips reduce the blank-page problem and demonstrate capabilities (H10, Paradox of the Active User)
- Search results include trust signals (quality score, freshness, classification, owner) directly addressing the transparency research finding
- Highlighted search terms in results reduce recognition load (H6)
- Related questions section encourages exploration (H7)
- Multi-asset type results (Tables, Policies, Columns, Pipelines) with type badges aid scanning (Gestalt: Law of Similarity)
- Attribution line with timing ("0.8s") builds confidence in system performance (H1, Doherty Threshold)

**Issues found and fixed:**

**[M5] Search bar lacks semantic role and accessible label** _(Accessibility)_
- Location: Find search bar
- Problem: The search input has no `role="search"` on the container and no `aria-label` on the input. Screen readers can't identify the purpose.
- Fix: Added `role="search"` to container, `aria-label="Search your data catalog"` to input, `aria-hidden="true"` on decorative icon.

**[m4] Example prompts don't submit search** _(H7: Flexibility)_
- Location: Example prompt chips
- Problem: Clicking an example prompt only fills the input text but doesn't trigger a search. Users must additionally click or press Enter.
- Recommendation for Round 2: Auto-submit when example prompt is clicked.

**[m5] No keyboard shortcut to focus Find search** _(H7: Flexibility)_
- Recommendation for Round 2: Add Cmd+K or dedicated shortcut to jump to Find search from any screen.

---

### Screen 4: Automate (Suggested Actions)

**What's working well:**
- Tab organization (Pending, Running, Completed, Scheduled) maps perfectly to automation lifecycle (H2)
- "Approve all high-confidence" batch action addresses power user efficiency (H7, Pareto Principle)
- Running tab shows progress bar with percentage and time estimate (H1, Goal-Gradient Effect, Zeigarnik Effect)
- Completed tab includes "Undo (23h remaining)" providing time-bounded reversibility (H3)
- Scheduled tab shows cron-like descriptions in user-friendly format (H2)
- Automation cards include scope and estimated time, enabling informed decisions (H6)

**Issues found and fixed:**

**[C3] "Approve all" has no confirmation step** _(H5: Error Prevention)_
- Location: Batch actions bar
- Problem: A single click approves and runs multiple automations simultaneously. For an action that could modify production data across 47 columns and 12 tables, this violates error prevention principles. The research emphasizes the autonomy dial — users must feel in control.
- Fix: Added confirmation dialog that specifies exactly how many automations qualify and what they will do.

**[m6] Running tab content not visible by default** _(H1: Visibility)_
- Location: Running tab (hidden)
- Problem: If an automation is actively running, users don't see it unless they switch tabs. The Running count "1" is visible in the tab, but the active operation deserves more prominence.
- Recommendation for Round 2: Show a compact running status indicator on the Pending tab or in the page header when automations are active.

---

### Screen 5: Agent Log

**What's working well:**
- Chronological log with monospace timestamps aids scanning (H6, Gestalt: Law of Proximity)
- Color-coded agent type labels (Focus blue, Find purple, Automate green) create visual grouping (Gestalt: Law of Similarity)
- Result badges (Success, Pending, Info) provide status at a glance (H1)
- Filter chips for Agent and Outcome enable focused review (H7)

**Issues found and fixed:**

**[M6] No filter clear option** _(H3: User Control and Freedom)_
- Location: Agent Log filter bar
- Problem: Same issue as Focus screen — no single action to reset all filters.
- Fix: Added "Clear filters" button.

**[m7] Log entries not expandable** _(H7: Flexibility)_
- Location: Log entry rows
- Problem: Entries show a brief description but clicking reveals nothing more. For debugging and audit purposes, users need to see full details (which assets were affected, exact timestamps, agent reasoning).
- Recommendation for Round 2: Add expandable detail panel on click.

**[m8] No pagination or date range filter** _(H7: Flexibility, Miller's Law)_
- Location: Log list
- Problem: All entries shown in a single flat list. As log volume grows, this becomes unmanageable.
- Recommendation for Round 2: Add date range picker and pagination or virtual scrolling.

---

### Screen 6: Agent Settings

**What's working well:**
- Segmented controls for autonomy levels are clear and well-labeled (H2, Hick's Law)
- Contextual description below each segmented control updates dynamically (H6, H10)
- Slider controls with live value display for confidence thresholds (H1)
- Toggle switches with descriptive labels and helper text (H6)
- Data scope section with checkbox list of connected sources (H6)
- The autonomy control per-agent type directly implements the "autonomy dial" research finding

**Issues found and fixed:**

**[C4] No save/discard pattern for settings changes** _(H1: Visibility, H3: User Control)_
- Location: Entire settings screen
- Problem: Changes take effect immediately with no visible confirmation. Users can't tell if their changes were saved. There's no way to discard changes. If a user accidentally toggles "Allow agents to modify data" off, there's no feedback or undo path.
- Fix: Added sticky save bar at the bottom that appears when any setting is changed, with "Discard" and "Save changes" buttons.

**[C5] Toggle switches not keyboard accessible** _(Accessibility, H7)_
- Location: All toggle switches in Settings
- Problem: Toggle switches are `<div>` elements with only `onclick` handlers. They have no `tabindex`, no `role`, no `aria-checked`, and cannot be operated via keyboard. This is a WCAG failure.
- Fix: Added `role="switch"`, `tabindex="0"`, `aria-checked`, `aria-labelledby` referencing the label text, and keyboard event handlers for Space and Enter keys.

**[m9] Confidence threshold sliders lack contextual help** _(H10: Help)_
- Location: Auto-apply threshold and Review threshold sliders
- Problem: Users can set any value from 70-100% and 30-90% respectively, but there's no guidance on what these numbers mean in practice. What does 85% confidence mean for a PII classification?
- Recommendation for Round 2: Add tooltip or helper text explaining what confidence levels mean with examples.

---

## Cross-Screen Issues

### Accessibility Fixes Applied

| Issue | Location | Fix |
|-------|----------|-----|
| No skip-to-content link | Page-level | Added skip link, visible on keyboard focus |
| Missing ARIA landmarks | Header, nav, main | Added `role="banner"`, `aria-label="Main navigation"`, `role="main"` |
| Decorative icons announced by screen readers | Throughout | Added `aria-hidden="true"` to decorative icon spans |
| No focus-visible styles | All interactive elements | Added `:focus-visible` styles with blue outline and shadow ring |
| Toggle switches not keyboard operable | Settings | Added tabindex, role, aria-checked, keyboard handlers |
| Search inputs missing labels | Header, Find | Added `aria-label` attributes |
| Reasoning toggles missing aria-expanded | Home insight cards | Added `aria-expanded` attribute, updated in JS |

### Design System Compliance Notes

The prototype has strong SDS compliance. All colors, spacing, typography, and component patterns use `--sds-*` tokens. A few minor notes:

1. **Sidebar icon weight**: Icons in the sidenav use `wght: 300` which is correct per SDS, but the insight tag icons use `wght: 400`. Should standardize — recommend 300 for navigation, 400 for inline status icons (current pattern is fine).
2. **Card border radius**: Some cards use `12px` border radius while SDS standard is `8px`. The 12px is used deliberately for full-width cards, which works visually, but should be documented as a pattern.
3. **Stat card value color**: Uses `--sds-text-primary` (dark) which is correct. The research report prototype uses `--sds-interactive-primary` (blue) for stat values which has better visual hierarchy.

---

## Summary of All Fixes in prototype-v2.html

### Critical Fixes (5)
1. **Undo toast on dismiss** — All "Dismiss" buttons now animate the card away and show an 8-second undo toast with timer
2. **Confirmation dialog for destructive actions** — "Revoke write access" now requires confirmation via modal dialog
3. **"Approve all" confirmation** — Batch approval on Automate screen now requires confirmation
4. **Settings save/discard bar** — Sticky bar appears when settings are changed, with Discard and Save options
5. **Toggle switch accessibility** — All toggles now keyboard-operable with full ARIA support

### Major Fixes (4)
6. **Info insight card collapse** — Info-priority cards on Home start collapsed to reduce alert fatigue
7. **Filter mutual exclusivity** — "All" and individual filters now behave correctly with clear logic
8. **Clear filters button** — Added to Focus and Agent Log filter bars
9. **Search accessibility** — Added `role="search"`, `aria-label`, and `aria-hidden` on decorative elements

### Minor Fixes (4)
10. **Skip-to-content link** — Added for keyboard users
11. **ARIA landmarks** — Added roles to header, nav, main
12. **Sidebar badge** — Focus nav item shows critical insight count
13. **Focus-visible styles** — All interactive elements get visible focus ring on keyboard navigation

---

## Remaining Items for Round 2

| Priority | Item | Screen |
|----------|------|--------|
| Major | Make autonomy dial on Home sidebar interactive (link to settings or inline control) | Home |
| Major | Add expandable detail panel to log entries | Agent Log |
| Major | Show running automation indicator on Pending tab | Automate |
| Major | Add date range filter and pagination to Agent Log | Agent Log |
| Major | Empty states for all filtered/tabbed views showing no results | Focus, Automate, Log |
| Major | Auto-submit when clicking example prompts in Find | Find |
| Minor | Add keyboard shortcut (Cmd+K) to jump to Find search | Find |
| Minor | Add contextual help tooltips on confidence threshold sliders | Settings |
| Minor | Consider showing "last saved" timestamp in Settings | Settings |
| Minor | Add Notifications screen content parity with Agent Log (merge or differentiate) | Notifications |
| Minor | Document 12px card border radius as an intentional pattern variation | Design system |
| Minor | Add loading/skeleton states for async operations (search results, automation runs) | Find, Automate |

---

## Laws of UX Application Summary

| Law | Where Applied | Assessment |
|-----|--------------|------------|
| **Fitts's Law** | Action buttons are well-sized (min 32px height); focus-visible outlines make targets more identifiable | Good |
| **Hick's Law** | Segmented controls limit choices to 3 options each; filter chips chunk categories well | Good |
| **Miller's Law** | Stat row shows 4 items (within 7+/-2); insight meta groups 2-3 items | Good |
| **Jakob's Law** | Search pattern, sidebar nav, card-based layouts all follow established conventions | Strong |
| **Von Restorff Effect** | Critical/warning/info color coding makes priority visually distinct; collapsed info cards reduce competition | Improved in v2 |
| **Zeigarnik Effect** | Running automation progress bar with percentage motivates monitoring | Good |
| **Goal-Gradient Effect** | Running automation shows "67% complete, ~4 min remaining" | Good |
| **Peak-End Rule** | Auto-resolved success card with green border provides satisfying completion; undo toast provides safe ending | Improved in v2 |
| **Serial Position Effect** | Critical insights positioned first in feed; Home nav item at top of sidebar | Good |
| **Gestalt: Proximity** | Insight meta items grouped closely; filter groups separated by dividers | Good |
| **Gestalt: Similarity** | Agent type color coding in log (blue/purple/green); status badge colors | Good |
| **Gestalt: Common Region** | Cards provide clear boundaries for each insight/automation | Good |
| **Tesler's Law** | Autonomy controls let users choose complexity trade-off; "Smart" mode as default bears complexity for user | Good |
| **Paradox of Active User** | Example prompts in Find screen demonstrate capability without requiring documentation | Good |
| **Postel's Law** | Natural language search accepts flexible input; results normalize across asset types | Good |
