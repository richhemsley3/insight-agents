# Design Critique — Round 2: AI Insight Agents Prototype

**Date:** 2026-03-25
**Artifact:** `prototype-v2.html` (evaluated), `prototype-v3.html` (fixed version)
**Stage:** Interactive prototype
**Round:** 2 of 3

---

## Overall Impression

Round 2 focused on filling the functional gaps that Round 1 identified but deferred: interactive controls, expandable detail patterns, empty/error/loading states, and trust-building features like confidence explanations. The prototype now has a substantially more complete interaction model. The autonomy dial on the Home sidebar is no longer a display-only widget — it is a functioning segmented control that gives users immediate agency. Log entries expand to reveal full reasoning chains, which directly supports the transparency requirement from the research findings. Loading skeletons, empty states, and error states give the prototype defensive depth it previously lacked entirely.

The most meaningful improvement this round is the confidence explanation system. Confidence percentages (94%, 96%) were opaque numbers that users had to accept on faith. Now each percentage is a hoverable tooltip that breaks down exactly how the agent arrived at that number — pattern matching accuracy, ML classifier agreement, sample validation rates. This directly addresses the trust research: users need to understand not just *what* the agent thinks, but *why* it thinks it.

Alert fatigue prevention has also improved. The running automation indicator on the Pending tab surfaces active work without requiring tab switching. Bulk reject alongside bulk approve gives users symmetrical control. Date range filtering on Focus and Agent Log prevents information overload as log volume grows.

---

## Heuristic Scorecard (Round 2)

| # | Heuristic | R1 | R2 | Delta | Summary |
|---|-----------|----|----|-------|---------|
| H1 | Visibility of System Status | 3/5 | 4/5 | +1 | Added: settings save toast, loading skeletons, running indicator on Pending tab, interactive autonomy dial with live description |
| H2 | Match Between System and Real World | 4/5 | 4/5 | -- | No change needed; language remains well-matched to data engineering persona |
| H3 | User Control and Freedom | 2/5 | 4/5 | +2 | Added: bulk reject, date range filtering, interactive autonomy from Home, Cmd+K shortcut, clear filters on all filtered views |
| H4 | Consistency and Standards | 4/5 | 4.5/5 | +0.5 | Improved: all reasoning toggles now have aria-expanded, all insight-reasoning-content divs have role/aria-label, timestamps consistent across screens |
| H5 | Error Prevention | 2/5 | 3.5/5 | +1.5 | Improved: bulk reject confirmation, error states for search, empty states prevent confusion from blank screens |
| H6 | Recognition Rather Than Recall | 4/5 | 4.5/5 | +0.5 | Added: confidence explanations (tooltip on hover), exact timestamps on hover, View all link from Home to Focus |
| H7 | Flexibility and Efficiency of Use | 3/5 | 4/5 | +1 | Added: Cmd+K to Find, auto-submit on example prompts, expandable log entries, date range picker, pagination |
| H8 | Aesthetic and Minimalist Design | 3/5 | 4/5 | +1 | Added: responsive breakpoints (1200/900/600px), running indicator surfaces status without tab switching, progressive disclosure in log entries |
| H9 | Help Users Recover from Errors | 3/5 | 4/5 | +1 | Added: error state for search, empty states with guidance and action buttons, retry mechanism |
| H10 | Help and Documentation | 3/5 | 4/5 | +1 | Added: contextual help tooltips on confidence thresholds, keyboard shortcut hints, empty states with guidance text |

**Overall: 4.1 / 5** (up from 3.1 in Round 1)

---

## What Was Fixed in Round 2

### From the Round 1 Remaining Items List

**1. Interactive autonomy dial** _(H1, H3, H7)_
- The Home sidebar autonomy widget is now a 3-position segmented control (Manual / Smart / Full auto) that users can click to change the autonomy level immediately
- Live description text updates on selection explaining what each level does
- "Configure in settings" link navigates to the full settings screen for granular control
- ARIA radiogroup role with aria-checked states for accessibility

**2. Expandable log entries in Agent Log** _(H7, H6)_
- Every log entry is now clickable to reveal a detail panel
- Detail panels show: reasoning chain (step-by-step agent logic), assets affected, and duration
- Expand/collapse icons with rotation animation provide clear affordance
- Keyboard accessible (Enter/Space to toggle), with aria-expanded state management
- Only one entry expanded at a time to reduce cognitive load (accordion pattern)

**3. Empty states for all screens** _(H10, H9, Paradox of the Active User)_
- Focus screen: Empty state for Resolved tab, empty state for filtered views with no results (includes "Clear all filters" action button)
- Automate screen: Empty states for Pending (no suggestions) and Running (nothing in progress) tabs
- Agent Log: Empty state for filtered view with no results
- Find screen: Empty state for no search results, error state for failed search with "Try again" button
- All empty states include an icon, heading, description text, and where applicable a primary action

**4. Date range filtering on Focus and Agent Log** _(H7, Miller's Law)_
- Both Focus and Agent Log screens now have inline date range pickers in the filter bar
- Native `<input type="date">` elements with sensible defaults (past 7 days for Focus, past 3 days for Log)
- Styled consistently with the filter chip pattern using the same border/radius treatment
- Accessible with aria-labels on both start and end date inputs

**5. Loading/skeleton states** _(H1, Doherty Threshold)_
- Shimmer animation skeleton cards for the Find screen search results
- 3 skeleton cards with varied line widths (60%, 80%, 40%) to simulate real content shapes
- Shown for 800ms when example prompts trigger a search, then replaced with actual results
- CSS-only animation using background gradient shimmer — no JS dependency for the visual

**6. Contextual help tooltips on complex controls** _(H10, H6)_
- Auto-apply threshold slider: Tooltip explaining what confidence levels mean with examples ("At 95%, only highly certain changes like clear PII pattern matches are auto-applied")
- Review threshold slider: Tooltip explaining the review flow ("Actions between this value and the auto-apply threshold are shown as suggested automations")
- Help icon (?) triggers with hover and focus for keyboard accessibility
- Dark background tooltips with arrow indicator, max-width 280px for readability

**7. Keyboard shortcut hints** _(H7)_
- Global search kbd element tooltip updated to mention both `/` and `Cmd+K`
- `Cmd+K` / `Ctrl+K` keyboard shortcut added to jump directly to the Find screen and auto-focus the search input

**8. Toast notification for settings save confirmation** _(H1, Peak-End Rule)_
- Save button now triggers a green success toast: "Settings saved successfully" with a check circle icon
- Toast appears for 3 seconds then auto-dismisses
- Uses `role="status"` and `aria-live="polite"` for screen reader announcement
- Separate Discard function that hides the save bar without showing a toast

**9. "View all" link from Home insight cards to Focus screen** _(H7, H6)_
- "View all 8 insights" link with arrow icon added to the insight cards header on the Home screen
- Navigates via hash routing to the Focus screen
- Styled as a text link consistent with the design system link pattern

**10. Agent confidence explanations** _(H6, H10, Trust)_
- Confidence percentage labels on insight cards are now hoverable/focusable tooltip triggers
- Schema drift (94%): Explains data type compatibility (100%), naming similarity (92%), value overlap (91%)
- PII classification (96%): Explains regex pattern matching (99%), ML classifier (94%), sample validation (96%)
- Positioned as inline tooltips that don't disrupt the card layout
- Tabindex="0" for keyboard accessibility

**11. Bulk reject option in Automate** _(H3, H5)_
- "Reject all" secondary button added alongside "Approve all high-confidence" in the batch actions bar
- Both buttons trigger confirmation dialogs before executing
- Reject dialog explains that dismissed automations can be re-suggested if conditions persist

**12. Relative timestamps with exact time on hover** _(H6, H2)_
- All Home screen insight card timestamps now show relative time ("2 hours ago") with exact UTC timestamp on hover
- Styled as monospace font in a dark tooltip for easy reading
- Applied to all 5 insight cards on the Home screen

### Additional Round 2 Fixes

**[R2-1] Running automation indicator on Pending tab** _(H1, Visibility)_
- When automations are running, a blue info bar appears at the top of the Pending tab
- Shows: spinning icon, automation name, and progress percentage
- Clicking navigates to the Running tab for full details
- Prevents the "hidden running state" issue identified in Round 1

**[R2-2] Responsive layout** _(H8, Accessibility)_
- Three breakpoints added: 1200px (sidebar stacks below, 2-col stats), 900px (sidebar collapses to icon-only, header search hidden), 600px (single-column everything)
- Sidebar collapses to 60px width showing only icons at 900px
- Stat cards reflow from 4-column to 2-column to single-column
- Batch action bar stacks vertically on mobile
- Insight meta items stack vertically on narrow screens

**[R2-3] Error state for Find search** _(H9)_
- Dedicated error state with icon, message, and "Try again" button
- Plain language: "We couldn't complete your search. This might be a temporary issue."
- Retry button calls the search function again

**[R2-4] ARIA improvements across log entries** _(Accessibility)_
- All log entries have `role="button"`, `tabindex="0"`, `aria-expanded`
- Log detail panels have `role="region"` and `aria-label`
- Keyboard handler for Enter/Space on log entries

**[R2-5] Pagination on Agent Log** _(H7, Miller's Law)_
- Page navigation with Previous/Next buttons and numbered pages
- "Page 1 of 3 (30 entries)" context label
- Disabled state for Previous on first page
- Active page button styled with primary color

---

## Newly Identified Issues

During the Round 2 evaluation, the following new issues were discovered:

**[R2-N1] Example prompt search results don't change** _(H1, H2)_ — Minor
- Location: Find screen
- Problem: All 4 example prompts populate the search input but all show the same "Where is customer PII stored?" results. In a production build, each prompt would return different results. For the prototype, this breaks the illusion of interactivity.
- Recommendation for Round 3: Add at least 2 alternate result sets for different prompts, or show the loading skeleton with a "Showing results for..." header that matches the clicked prompt.

**[R2-N2] Focus screen tabs don't show/hide content** _(H1)_ — Minor
- Location: Focus screen Active/Resolved tabs
- Problem: The tab switching function (`switchTab`) only updates the active visual state but doesn't show/hide corresponding content panels. Clicking "Resolved" still shows the Active insights list.
- Recommendation for Round 3: Wire up content panel toggling for Focus tabs, similar to how Automate tabs work.

**[R2-N3] No micro-interactions on stat cards** _(Aesthetic-Usability Effect)_ — Minor
- Location: Home stat cards
- Problem: Stat cards are static display. Adding a hover state and making them clickable (e.g., "Insights today" navigates to Focus, "Actions pending" to Automate) would increase utility and discoverability.
- Recommendation for Round 3: Add hover states and click-through navigation.

**[R2-N4] Notification screen is a simplified duplicate of Agent Log** _(H4, Consistency)_ — Minor
- Location: Notifications screen
- Problem: The Notifications screen shows a subset of Agent Log information in the same format but with less detail and no filtering. Users may be confused about the difference between the two screens.
- Recommendation for Round 3: Differentiate Notifications (alerts requiring action, with read/unread state and mark-as-read) from Agent Log (comprehensive chronological audit trail).

**[R2-N5] Collapsed info card dismiss button not wired** _(H3)_ — Minor
- Location: Home screen, collapsed info insight card (#3)
- Problem: The dismiss button on the collapsed info card does not have the `onclick="dismissInsight(this)"` handler that other dismiss buttons have.
- Recommendation for Round 3: Wire up all dismiss buttons consistently.

---

## Laws of UX Application Summary (Updated)

| Law | Where Applied | R1 Assessment | R2 Assessment |
|-----|--------------|---------------|---------------|
| **Fitts's Law** | Action buttons, clickable log entries, running indicator | Good | Good |
| **Hick's Law** | Segmented controls (3 options), filter chips | Good | Good |
| **Miller's Law** | Stat row (4 items), date range picker reduces visible log entries, pagination | Good | Improved |
| **Jakob's Law** | Search pattern, sidebar nav, card layouts, accordion expand pattern | Strong | Strong |
| **Von Restorff Effect** | Priority color coding, running indicator uses animation to draw attention | Improved | Strong |
| **Zeigarnik Effect** | Running automation progress, running indicator on Pending tab | Good | Strong |
| **Goal-Gradient Effect** | Progress percentage in running indicator, pagination shows position | Good | Good |
| **Peak-End Rule** | Save confirmation toast provides satisfying completion, undo toast for safety | Improved | Strong |
| **Serial Position Effect** | Critical insights first, key nav items at top/bottom of sidebar | Good | Good |
| **Gestalt: Proximity** | Insight meta groups, filter group separators, log detail sections | Good | Good |
| **Gestalt: Similarity** | Agent type color coding, status badges, consistent card patterns | Good | Good |
| **Gestalt: Common Region** | Cards, log detail panels with background color differentiation | Good | Improved |
| **Tesler's Law** | Confidence explanations reveal system complexity on demand, autonomy dial | Good | Strong |
| **Paradox of Active User** | Example prompts auto-submit, empty states guide without documentation | Good | Strong |
| **Postel's Law** | Natural language search, flexible date inputs | Good | Good |
| **Doherty Threshold** | Skeleton loading states provide feedback during 800ms search delay | Not addressed | Addressed |
| **Aesthetic-Usability Effect** | Responsive layout maintains polish across viewports | Not addressed | Addressed |

---

## Summary of All Fixes in prototype-v3.html

### Major Fixes (12) — All Round 1 Remaining Items Addressed
1. **Interactive autonomy dial** — 3-position segmented control with live descriptions and settings link
2. **Expandable log entries** — Click-to-expand with reasoning chains, affected assets, duration
3. **Empty states** — 6 empty states across Focus, Automate, Agent Log, and Find
4. **Date range filtering** — Native date pickers on Focus and Agent Log
5. **Loading skeleton states** — Shimmer animation cards for Find search results
6. **Contextual help tooltips** — Threshold sliders explain confidence levels with examples
7. **Keyboard shortcut (Cmd+K)** — Jumps to Find screen and focuses search input
8. **Settings save toast** — Green success notification with auto-dismiss
9. **View all link** — Home insight header links to Focus screen
10. **Confidence explanations** — Hoverable tooltips breaking down confidence scores
11. **Bulk reject** — "Reject all" button with confirmation dialog on Automate
12. **Relative timestamps** — Hover reveals exact UTC time on all Home insight cards

### Additional Fixes (5)
13. **Running automation indicator** — Blue info bar on Pending tab with spinning icon
14. **Responsive layout** — 3 breakpoints (1200/900/600px) with sidebar collapse
15. **Error state for search** — Error UI with retry button for Find screen
16. **Log entry ARIA** — Full keyboard support and ARIA roles on expandable entries
17. **Pagination** — Page navigation on Agent Log

---

## Remaining Items for Round 3

| Priority | Item | Screen | Source |
|----------|------|--------|--------|
| Major | Wire up Focus screen tab content switching (Active/Resolved) | Focus | R2-N2 |
| Major | Differentiate Notifications screen from Agent Log (read/unread, mark-as-read) | Notifications | R2-N4 |
| Major | Add alternate search result sets for different example prompts | Find | R2-N1 |
| Minor | Make stat cards clickable with hover states (navigate to relevant screens) | Home | R2-N3 |
| Minor | Wire dismiss handler on collapsed info card | Home | R2-N5 |
| Minor | Add onboarding/first-run experience for new users | All | Original backlog |
| Minor | Document 12px card border radius as intentional pattern variation | Design system | R1 carryover |
| Minor | Consider "last saved" timestamp in Settings | Settings | R1 carryover |
| Minor | Add transition animation when switching between screens | All | Polish |
| Minor | Test and verify all responsive breakpoints with real content | All | Verification |

---

## Heuristic Score Trajectory

| Round | H1 | H2 | H3 | H4 | H5 | H6 | H7 | H8 | H9 | H10 | Overall |
|-------|----|----|----|----|----|----|----|----|----|----|---------|
| Round 1 | 3 | 4 | 2 | 4 | 2 | 4 | 3 | 3 | 3 | 3 | **3.1** |
| Round 2 | 4 | 4 | 4 | 4.5 | 3.5 | 4.5 | 4 | 4 | 4 | 4 | **4.1** |
| Target (R3) | 4.5 | 4.5 | 4.5 | 5 | 4 | 5 | 4.5 | 4.5 | 4.5 | 4.5 | **4.5** |

The biggest improvements this round were in H3 (User Control, +2) and H5 (Error Prevention, +1.5). Round 3 should focus on closing the remaining consistency gaps (H4 to 5/5 by fixing tab switching and notification differentiation) and reaching the target of 4.5+ overall.
