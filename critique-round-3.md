# Design Critique — Round 3 (FINAL): AI Insight Agents Prototype

**Date:** 2026-03-25
**Artifact:** `prototype-v3.html` (evaluated), `prototype-final.html` (polished version)
**Stage:** Interactive prototype — production polish pass
**Round:** 3 of 3 (FINAL)

---

## Overall Impression

The prototype has reached production quality. Across three rounds, the AI Insight Agents interface evolved from a visually strong but functionally shallow layout (Round 1, 3.1/5) into a fully interactive, defensively designed system that addresses every core research finding: alert fatigue, trust through transparency, progressive disclosure, and user autonomy. The final round focused on closing the last functional gaps and polishing micro-interactions to the level expected of a shipped enterprise product.

The most significant Round 3 improvement is the differentiated Notifications screen. Where it was previously a simplified duplicate of the Agent Log, it now serves a distinct purpose: an action-oriented inbox with read/unread state, urgency-sorted entries, and inline actions that route users to the right screen. This separation follows Jakob's Law — users expect notifications to behave like a notification inbox, not a chronological audit trail.

Focus screen tab switching is now fully functional, with the Active and Resolved tabs toggling distinct content panels. The Resolved tab shows 5 resolved insights with metadata about who resolved them and when, giving users a clear audit trail of past actions. The filter bar correctly hides when viewing resolved items, since those filters are not applicable.

The Find screen now delivers genuinely different results for each of the 4 example prompts. "Where is customer PII stored?" returns tables, policies, and pipelines. "Show me tables Jordan owns" returns ownership-filtered results. "What changed in the last 24 hours?" shows schema changes, pipeline failures, and metadata updates. "Which pipelines are failing?" returns pipeline-specific results with failure signals. This makes the prototype feel like a real search system rather than a single static result set.

---

## Heuristic Scorecard (Round 3 — Final)

| # | Heuristic | R1 | R2 | R3 | Delta (R2-R3) | Summary |
|---|-----------|----|----|----|----|---------|
| H1 | Visibility of System Status | 3/5 | 4/5 | 4.5/5 | +0.5 | Added: last saved timestamp in Settings, notification read/unread state, stat card hover hints showing destination, main content scrolls to top on navigation |
| H2 | Match Between System and Real World | 4/5 | 4/5 | 4.5/5 | +0.5 | Improved: different Find result sets match the mental model that different questions yield different answers; Notifications use urgency language (Critical/Warning) matching user expectations |
| H3 | User Control and Freedom | 2/5 | 4/5 | 4.5/5 | +0.5 | Added: mark-as-read on individual notifications, mark all as read, collapsed info card dismiss handler now wired |
| H4 | Consistency and Standards | 4/5 | 4.5/5 | 5/5 | +0.5 | Fixed: Notifications screen now distinct from Agent Log (different purpose, different design), Focus tab switching wired consistently with Automate tabs, all dismiss handlers consistent |
| H5 | Error Prevention | 2/5 | 3.5/5 | 4/5 | +0.5 | Improved: Settings changes from checkboxes and radio buttons now also trigger the save bar (previously only segments and sliders did) |
| H6 | Recognition Rather Than Recall | 4/5 | 4.5/5 | 5/5 | +0.5 | Added: stat card hover hints ("View insights", "Review actions") tell users where cards navigate; resolved insights show resolver identity and timestamp; notification meta shows agent type + time + urgency |
| H7 | Flexibility and Efficiency of Use | 3/5 | 4/5 | 4.5/5 | +0.5 | Added: stat cards are keyboard-navigable with Enter/Space; related questions in Find are clickable and trigger new searches; notifications have inline action buttons |
| H8 | Aesthetic and Minimalist Design | 3/5 | 4/5 | 4.5/5 | +0.5 | Improved: screen transition animation uses cubic-bezier easing for smoother feel; stat card hover lift is subtle (1px translateY); notification design is clean with clear visual hierarchy via unread styling |
| H9 | Help Users Recover from Errors | 3/5 | 4/5 | 4.5/5 | +0.5 | Maintained: all error recovery paths from R2 intact; notification read state is non-destructive (no undo needed since marking read is low-stakes) |
| H10 | Help and Documentation | 3/5 | 4/5 | 4.5/5 | +0.5 | Added: resolved tab content gives users reference material for past decisions; notification entries provide context about what happened and why |

**Overall: 4.5 / 5** (up from 4.1 in Round 2, 3.1 in Round 1)

---

## What Was Fixed in Round 3

### Major Fixes (3) — All Round 2 Remaining Items Addressed

**1. Focus screen Active/Resolved tab content toggling** _(H1, H4)_ — Source: R2-N2
- `switchFocusTab()` function replaces the generic `switchTab()` for Focus screen
- Active panel and Resolved panel are separate `div.focus-tab-panel` elements that toggle visibility
- Filter bar hides when Resolved tab is active (filters are not applicable to resolved items)
- Resolved tab shows 5 resolved insight cards with distinct visual treatment (reduced opacity, neutral left border)
- Each resolved card shows: resolver identity (agent vs. human), resolution timestamp, and a "View details" action
- Keyboard accessible: tab buttons use standard button semantics

**2. Notifications screen differentiated from Agent Log** _(H4, H1, H6)_ — Source: R2-N4
- Completely redesigned as an action-oriented notification inbox, distinct from the chronological Agent Log
- **Read/unread state**: Unread entries have blue-025 background, 3px blue left border, bold title, and a blue dot indicator
- **Mark as read**: Individual "Mark read" buttons per notification, plus "Mark all as read" bulk action in header
- **Unread count**: Header shows "2 unread" badge that updates dynamically as notifications are marked read
- **Header notification dot**: The red dot on the header bell icon hides when all notifications are read
- **Inline actions**: Each notification has contextual action buttons ("View insight", "Review", "View in log", "View status") that route to the correct screen
- **Urgency indicators**: Notifications show urgency level (Critical, Warning, Info) in colored text within the meta line
- **Agent attribution**: Each notification shows which agent generated it (Focus agent, Automate agent)
- **Distinct purpose**: Notifications = alerts requiring attention; Agent Log = comprehensive audit trail

**3. Alternate search result sets for different example prompts** _(H1, H2)_ — Source: R2-N1
- 4 distinct result sets stored in a `findResultSets` data structure:
  - "Where is customer PII stored?" — 5 results (tables, policy, column, pipeline) with PII-focused content
  - "Show me tables Jordan owns" — 4 results showing tables with ownership context
  - "What changed in the last 24 hours?" — 4 results showing schema changes, new columns, doc updates, pipeline failures
  - "Which pipelines are failing?" — 3 results showing failed/degraded pipelines with failure signals
- Each result set has unique: header, meta summary, result cards, related questions, and attribution
- `renderFindResults()` dynamically builds result HTML from the data structure
- Related questions are clickable and trigger new searches with the appropriate result set
- Loading skeleton still shows during the 800ms transition between result sets

### Minor Fixes (5)

**4. Clickable stat cards with hover states** _(H7, H6)_ — Source: R2-N3
- All 4 stat cards on Home are now clickable with `role="link"` and `tabindex="0"`
- "Insights today" navigates to Focus, "Actions pending" to Automate, "Auto-resolved" to Agent Log, "Agent accuracy" to Settings
- Hover state: subtle box-shadow, stronger border, 1px upward translate
- Active state: translate back to 0 for tactile click feedback
- Focus-visible state: 2px blue outline with offset
- Hover hint text appears on hover ("View insights", "Review actions", "View log", "Settings") with arrow icon
- Keyboard accessible: Enter and Space trigger click

**5. Collapsed info card dismiss handler wired** _(H3)_ — Source: R2-N5
- The dismiss button on Insight #3 (collapsed info card) now has `onclick="dismissInsight(this)"` matching all other dismiss buttons

**6. Settings checkbox and radio changes trigger save bar** _(H1, H5)_
- `toggleCheckbox()` and `selectRadio()` now call `showSettingsSaveBar()`, matching the behavior of segment and slider controls
- Previously, only segment clicks and slider changes triggered the save bar

**7. Last saved timestamp in Settings** _(H1)_ — Source: R1 carryover
- Settings page header now shows "Last saved: Today at 8:05 AM" with a check circle icon
- Timestamp updates dynamically when settings are saved via `saveSettings()`

**8. Screen transition animation and scroll reset** _(H8)_ — Source: R2 backlog
- Screen transitions use `cubic-bezier(0.16, 1, 0.3, 1)` easing for a smooth, natural feel
- Main content area scrolls to top on screen navigation, preventing disorientation

---

## Laws of UX Application Summary (Final)

| Law | Where Applied | R1 | R2 | R3 (Final) |
|-----|--------------|-----|-----|------------|
| **Fitts's Law** | Action buttons, clickable stat cards (large targets), notification action buttons | Good | Good | Strong |
| **Hick's Law** | Segmented controls (3 options), filter chips, notification actions (1-2 per entry) | Good | Good | Strong |
| **Miller's Law** | Stat row (4 items), date range, pagination, notification count in header | Good | Improved | Strong |
| **Jakob's Law** | Search pattern, sidebar nav, notification inbox pattern, tab switching | Strong | Strong | Strong |
| **Von Restorff Effect** | Priority colors, unread notification styling (blue bg + bold + dot), running indicator | Improved | Strong | Strong |
| **Zeigarnik Effect** | Running progress, pending counts, unread notification count | Good | Strong | Strong |
| **Goal-Gradient Effect** | Progress percentage, pagination position, notification read progress | Good | Good | Strong |
| **Peak-End Rule** | Save toast, undo toast, mark-all-as-read provides satisfying completion | Improved | Strong | Strong |
| **Serial Position Effect** | Critical insights first, key nav at top/bottom, critical notifications first | Good | Good | Good |
| **Gestalt: Proximity** | Insight meta groups, filter groups, notification meta line items | Good | Good | Good |
| **Gestalt: Similarity** | Agent type colors, status badges, consistent card patterns, resolved card styling | Good | Good | Strong |
| **Gestalt: Common Region** | Cards, log detail panels, notification entries with background differentiation | Good | Improved | Strong |
| **Tesler's Law** | Confidence explanations, autonomy dial, notification urgency levels absorb complexity | Good | Strong | Strong |
| **Paradox of Active User** | Example prompts, empty states, notification actions guide without documentation | Good | Strong | Strong |
| **Postel's Law** | Natural language search, flexible date inputs, related questions as input suggestions | Good | Good | Strong |
| **Doherty Threshold** | Skeleton loading, 800ms search delay, smooth screen transitions | Not addressed | Addressed | Strong |
| **Aesthetic-Usability Effect** | Responsive layout, stat card hover lift, notification unread styling, cubic-bezier transitions | Not addressed | Addressed | Strong |

---

## Summary of All Fixes Across 3 Rounds

### Round 1 (13 fixes) — Foundation
1. Keyboard focus styles on all interactive elements
2. Skip-to-main-content link
3. Undo toast with timer for dismissed insights
4. Confirmation dialog for destructive actions
5. Expandable reasoning chains on insight cards
6. Low-priority insight card collapse pattern
7. Filter clear button
8. Sidebar badge count
9. Empty states (initial set)
10. Settings save bar
11. Filter chip mutual exclusivity
12. Button variants (primary, secondary, text, danger)
13. Focus-visible outlines for accessibility

### Round 2 (17 fixes) — Interaction completeness
14. Interactive autonomy dial (3-position segmented control)
15. Expandable log entries with reasoning chains
16. Empty states for all screens (6 total)
17. Date range filtering on Focus and Agent Log
18. Loading skeleton states for Find search
19. Contextual help tooltips on confidence thresholds
20. Keyboard shortcut (Cmd+K) to Find
21. Settings save toast notification
22. "View all" link from Home to Focus
23. Confidence explanation tooltips
24. Bulk reject in Automate
25. Relative timestamps with exact time on hover
26. Running automation indicator on Pending tab
27. Responsive layout (3 breakpoints)
28. Error state for Find search
29. Log entry ARIA improvements
30. Pagination on Agent Log

### Round 3 (8 fixes) — Polish and differentiation
31. Focus screen Active/Resolved tab content toggling
32. Notifications screen differentiated from Agent Log (read/unread, mark-as-read, urgency, inline actions)
33. Alternate search result sets for 4 different example prompts
34. Clickable stat cards with hover states and navigation
35. Collapsed info card dismiss handler wired
36. Settings checkbox/radio changes trigger save bar
37. Last saved timestamp in Settings
38. Screen transition animation and scroll-to-top

**Total: 38 fixes across 3 rounds**

---

## Heuristic Score Trajectory

| Round | H1 | H2 | H3 | H4 | H5 | H6 | H7 | H8 | H9 | H10 | Overall |
|-------|----|----|----|----|----|----|----|----|----|----|---------|
| Round 1 | 3 | 4 | 2 | 4 | 2 | 4 | 3 | 3 | 3 | 3 | **3.1** |
| Round 2 | 4 | 4 | 4 | 4.5 | 3.5 | 4.5 | 4 | 4 | 4 | 4 | **4.1** |
| Round 3 | 4.5 | 4.5 | 4.5 | 5 | 4 | 5 | 4.5 | 4.5 | 4.5 | 4.5 | **4.5** |
| Improvement | +1.5 | +0.5 | +2.5 | +1 | +2 | +1 | +1.5 | +1.5 | +1.5 | +1.5 | **+1.4** |

---

## Overall Design Quality Assessment

### Strengths
- **Trust through transparency**: Confidence explanations, reasoning chains, and the audit trail in Agent Log and Resolved tab give users full visibility into agent decision-making. This directly addresses the core research finding that users need to understand why an AI acts, not just what it recommends.
- **Alert fatigue prevention**: Progressive disclosure (collapsed low-priority cards, read/unread notifications, tab-based organization) prevents information overload. Users see what matters most and can drill into details on demand.
- **User autonomy**: The 3-level autonomy dial, configurable thresholds, and the ability to approve/reject/undo every agent action ensures users remain in control. The system never feels like it is acting without permission.
- **Design system adherence**: Consistent use of SDS tokens for colors, spacing, typography, and border radius throughout. Warm neutrals for structure, cool blue for interaction. Light active states. Subtle depth via minimal shadows.
- **Accessibility foundation**: Skip link, ARIA roles, keyboard navigation, focus-visible outlines, screen reader announcements on toasts, and proper heading hierarchy.

### Areas for Future Improvement
- **Real-time updates**: The prototype is static HTML. A production build would need WebSocket or polling for live updates to stat cards, running automations, and notification counts.
- **Onboarding/first-run experience**: New users would benefit from a guided walkthrough explaining each agent type, the autonomy dial, and how confidence thresholds work.
- **Table/column detail views**: Clicking through from insight cards or Find results should lead to detailed asset pages. These are beyond prototype scope but critical for production.
- **Performance at scale**: The Agent Log pagination is prototyped but a production build needs virtual scrolling for large datasets.
- **Mobile experience**: Responsive breakpoints are in place but the information density of this tool means a dedicated mobile-optimized layout (not just reflowed desktop) would be more effective.

---

## Research Alignment Check

| Research Finding | How the Prototype Addresses It |
|-----------------|-------------------------------|
| **Alert fatigue** | Progressive disclosure (collapsed cards), priority sorting, read/unread notifications, tab-based organization, batch actions |
| **Trust in AI recommendations** | Confidence explanations, reasoning chains, agent attribution on all actions, "Show reasoning" expand pattern, explicit confidence thresholds |
| **Progressive disclosure** | Home shows summary (stat cards + top 5 insights); Focus shows all 8 with filters; Agent Log shows full audit trail; Settings reveals granular controls |
| **User autonomy** | 3-level autonomy dial on Home, configurable thresholds in Settings, approve/reject/undo on every action, mark-as-read on notifications, dismiss with undo |

---

## Recommendations for Future Phases

1. **User testing with data engineers**: Validate that the Find agent search results match real query patterns and that confidence thresholds are set at the right defaults.
2. **Design token audit**: Run a formal compliance audit (using `/design-reviewer`) to verify every component matches the SDS token spec exactly.
3. **Accessibility audit**: Run a full WCAG 2.1 AA audit (using `/accessibility-auditor`) to verify contrast ratios, landmark regions, and screen reader flow.
4. **Animation performance**: Test screen transitions and hover states on lower-powered devices to ensure smooth 60fps rendering.
5. **Content strategy**: Work with a content designer (using `/content-copy-designer`) to finalize microcopy for empty states, error messages, and notification text.
6. **Dark mode**: The SDS token system supports dark mode via CSS custom properties. Add a theme toggle for users who prefer dark mode for extended monitoring sessions.
