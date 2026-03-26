# Design Critique: Chat-Based AI Agent Prototype
**Round**: 2 of 3
**Stage**: Prototype (interactive HTML)
**Date**: 2026-03-26
**File reviewed**: `prototype-chat-v2.html`
**Fixed version**: `prototype-chat-v3.html`

---

## Overall Impression

The v2 prototype addressed critical accessibility and interaction gaps from Round 1 effectively. ARIA roles, keyboard navigation, confirmation dialogs, and focus management are now solid. However, this round reveals a second tier of issues centered on the conversation experience itself: there are no error states for failed operations, no way to undo agent-executed actions, no feedback mechanisms (copy, thumbs up/down) on responses, and the empty state lacks contextual guidance. The proactive insight card, while visually distinct, provides no user control over when and what unsolicited messages appear. The panel resize handle works mechanically but has no visual affordance during drag. Mobile behavior is completely unaddressed. This round focuses on making the chat feel like a production-quality conversational tool rather than a demo flow.

---

## Heuristic Scorecard

| # | Heuristic | R1 | R2 | Notes |
|---|-----------|-----|-----|-------|
| 1 | Visibility of System Status | 2/5 | 4/5 | Typing indicator CSS exists but no skeleton loader for initial content. Status bar added but no error/degraded states. Fixed: skeleton loader, error status dot variants. |
| 2 | Match Between System & Real World | 4/5 | 4.5/5 | "Service accts" abbreviation fixed to "Service accounts". Natural multi-turn flow. Classification list still uses heavy inline styles. Fixed: reusable CSS classes for classification rows. |
| 3 | User Control & Freedom | 3/5 | 4.5/5 | Escape closes panel, confirmation dialog exists. Missing: undo for executed actions, message edit capability, proactive insight dismissal/control. Fixed: undo toast pattern, edit button on user messages, insight settings popover. |
| 4 | Consistency & Standards | 4/5 | 4.5/5 | Panel header buttons still at 28px (below Fitts's Law 44px min, 36px acceptable for desktop). Classification list used inline styles rather than component classes. Fixed: buttons to 36px, reusable classification classes. |
| 5 | Error Prevention | 2/5 | 4/5 | Send button disabled when empty (good). Confirmation dialog (good). Missing: error state UI for failed queries, partial action failures. Fixed: error callout component with retry and dismiss. |
| 6 | Recognition Rather Than Recall | 4/5 | 4.5/5 | Good suggestion chips. Empty state shows recent conversations but lacks contextual quick-start prompts. Keyboard shortcut not visible in panel. Fixed: contextual quick prompts in empty state, keyboard shortcut badge in panel header. |
| 7 | Flexibility & Efficiency of Use | 3/5 | 4/5 | Panel resize works. No keyboard shortcut hint visible in the panel UI itself. Detail panel tabs lack keyboard support. No conversation history management. Fixed: kbd badge in header, tab arrow key nav, export/clear buttons on history. |
| 8 | Aesthetic & Minimalist Design | 4/5 | 4.5/5 | Clean visual hierarchy. Classification list inline styles create code clutter. Response cards lack a footer action zone. Fixed: dedicated CSS classes, response card footer with copy/feedback. |
| 9 | Error Recovery | 2/5 | 4/5 | No error states in v2 at all. No retry mechanism. No undo. Fixed: error callout with retry button, undo toast with timer, dismiss option on errors. |
| 10 | Help & Documentation | 3/5 | 4/5 | Empty state explains capabilities. No "What can I ask?" prompts. No keyboard shortcut visible. Fixed: contextual quick-start prompts in empty state, kbd shortcut in panel title. |

**Overall: 4.3/5** (up from 3.1/5 in Round 1)

---

## What's Working Well

1. **Confirmation dialog pattern is solid** (Heuristic 5, Error Prevention) -- The confirmation dialog from R1 with focus trap, Escape key support, and Cancel as default-focused button is well-implemented. The visual hierarchy clearly separates the destructive action from the safe exit.

2. **Natural multi-turn conversation flow** (Heuristic 2, Jakob's Law) -- The Find > Understand > Act progression across turns 1-3 mirrors how data professionals work. Each response builds on the previous context without requiring the user to re-specify what table they're discussing.

3. **Rich response card structure** (Miller's Law, Chunking) -- Response cards chunk information into summary > details > expandable items > attribution. The progressive disclosure via expandable result items prevents cognitive overload while keeping details accessible.

4. **Status bar provides system awareness** (Heuristic 1) -- The connection status indicator with green dot and version number gives users confidence the system is operational. This is especially important for an AI tool where latency is expected.

5. **Accessibility improvements from R1 are maintained** (WCAG 2.1 AA) -- ARIA roles, landmarks, keyboard navigation on context menu, focus management, skip-to-content link, and reduced-motion support all carry forward correctly.

6. **Panel resize handle works well mechanically** (Heuristic 7) -- The 360px-800px range is appropriate. The drag handle activates on hover with a subtle color indicator. Fullscreen mode correctly disables resize.

---

## Areas for Improvement

### Critical Issues (Must Fix)

**Issue 1: No error states for failed AI queries or actions** _(Heuristic 9, Error Recovery)_
What I see: The prototype shows only successful outcomes. There is no UI for what happens when a query fails (network error, timeout), when an action partially fails (flagging succeeds but classification fails), or when the agent connection drops.
Why it matters: In enterprise data platforms, network failures and timeouts are common. Without error states, users have no way to understand what went wrong or how to recover. This violates Error Recovery and Visibility of System Status.
Suggestion: Added an error callout component with red border accent, error icon, plain-language message ("Lineage fetch failed. Unable to retrieve lineage data..."), retry button, and dismiss button. Added error/warning variants to the status bar dot. Retry button shows a spinner animation during retry attempt.
**Status: Fixed in v3**

**Issue 2: No undo pattern for agent-executed actions** _(Heuristic 3, User Control & Freedom)_
What I see: When the agent flags access and classifies columns, the result is presented as final. There is no way to undo either action. The "Review classifications" button exists but there is no undo mechanism.
Why it matters: In data governance, accidentally classifying a column as PII can trigger downstream policy enforcement (masking, access restrictions). Users need an immediate escape hatch. The Peak-End Rule suggests the experience after an action is as important as the action itself.
Suggestion: Added an undo toast component that appears at the bottom of the viewport with a timer bar (8 seconds), undo button, and auto-dismiss. Added an "Undo classifications" button inline in the action result card. The toast uses dark background (warm-gray-800) with high contrast for urgency.
**Status: Fixed in v3**

**Issue 3: No feedback mechanisms on responses** _(Heuristic 7, Flexibility & Efficiency of Use)_
What I see: Response cards have no copy-to-clipboard button, no thumbs up/down feedback, and no way for users to indicate whether a response was helpful. Users cannot easily extract information from responses.
Why it matters: Copy-to-clipboard is essential for enterprise tools where users share findings with colleagues via Slack, email, or tickets. Feedback buttons help the AI improve and give users a sense of agency. Without these, the interaction feels one-directional.
Suggestion: Added a `.response-card-footer` bar below the attribution line with copy button (left) and thumbs up/down buttons (right). Copy shows a checkmark confirmation for 2 seconds. Feedback buttons toggle with filled icon state.
**Status: Fixed in v3**

### Major Issues (Must Fix)

**Issue 4: Loading skeleton missing for initial panel content** _(Heuristic 1, Doherty Threshold)_
What I see: The typing indicator CSS was added in v2 but there is no skeleton loader for the response card area. When a user sends a query, the next visible state should be a skeleton that matches the response card shape, not just bouncing dots.
Why it matters: Skeleton loaders set user expectations for the shape and density of incoming content (Gestalt: Pragnanz). They reduce perceived wait time more effectively than generic spinners because users can anticipate the layout.
Suggestion: Added a `.skeleton-card` component with animated shimmer lines and a block placeholder. The skeleton is shown when a message is sent and hidden when the response arrives. Uses the same 12px border-radius as response cards for visual consistency.
**Status: Fixed in v3**

**Issue 5: Mobile/responsive behavior completely missing** _(Heuristic 7, Jakob's Law)_
What I see: On viewports under 768px, the agent panel overlaps the sidebar and content awkwardly. The sidebar navigation, stat grid, and home layout do not adapt. There are no mobile breakpoints.
Why it matters: Enterprise tools are increasingly used on tablets and phones for on-the-go monitoring. The agent panel should become full-screen on mobile. The sidebar should collapse. Suggestion chips should scroll horizontally.
Suggestion: Added `@media (max-width: 768px)` breakpoint that hides sidebar, makes agent panel full-width, adjusts stat grid to 2-column, and collapses home layout to single column. Added `@media (max-width: 480px)` for single-column stats and horizontal-scrolling suggestion chips.
**Status: Fixed in v3**

**Issue 6: Conversation history management missing** _(Heuristic 3, Heuristic 7)_
What I see: The empty state shows 3 recent conversations but there is no way to search, export, or clear history. The conversations are listed without management controls.
Why it matters: Over time, users accumulate many conversations. Without search, export (for audit/compliance), and clear functionality, the conversation list becomes unwieldy. Enterprise users need export for compliance documentation.
Suggestion: Added a header row above recent conversations with "Export" and "Clear" action buttons. Export uses a download icon, Clear uses a delete_outline icon. Both are compact text buttons that match SDS patterns.
**Status: Fixed in v3**

**Issue 7: Empty state lacks contextual quick-start prompts** _(Heuristic 6, Recognition Rather Than Recall)_
What I see: The empty state shows a generic "How can I help?" message and recent conversations, but does not offer contextual starter prompts based on the current page or user role.
Why it matters: Quick-start prompts reduce the "blank canvas" problem (Paradox of the Active User). Users who don't know what to ask are more likely to engage when given relevant starting points. Context-awareness (e.g., showing classification prompts when on the Classification page) increases the AI's perceived intelligence.
Suggestion: Added an `.empty-quick-prompts` section with 4 contextual prompt buttons, each with an icon and descriptive text. These sit between the description and recent conversations, creating a clear visual hierarchy: greeting > prompts > history.
**Status: Fixed in v3**

**Issue 8: No message retry on failure** _(Heuristic 9, Error Recovery)_
What I see: If a query or action fails, the user's only option would be to retype the message. There is no retry button associated with failed messages.
Why it matters: Re-typing a query is wasteful and frustrating. A retry button on the error state preserves the original intent and reduces friction. This is a standard pattern in chat applications (iMessage, Slack, WhatsApp all show retry on failed messages).
Suggestion: The error callout component includes a retry button that shows a spinning animation during retry, then dismisses the error on success. This is integrated into the error state from Issue 1.
**Status: Fixed in v3 (via error callout retry button)**

### Minor Issues (Fixed Where Straightforward)

**Issue 9: Panel header buttons too small** _(Fitts's Law)_
What I see: Panel header action buttons (new conversation, fullscreen, close) are 28px. The minimum recommended touch target is 44px (WCAG), with 36px acceptable for desktop-only contexts.
Suggestion: Increased `.agent-panel-btn` from 28px to 36px width and height.
**Status: Fixed in v3**

**Issue 10: Keyboard shortcut not visible in agent panel UI** _(Heuristic 6, Recognition Rather Than Recall)_
What I see: The Cmd+K shortcut appears as a transient toast on page load but is not visible within the agent panel itself. After the toast disappears, there is no reminder.
Suggestion: Added a persistent `kbd` badge in the panel title bar showing the shortcut, using subdued styling (overline size, tertiary color) so it doesn't compete with the title.
**Status: Fixed in v3**

**Issue 11: "Service accts" abbreviation in access summary** _(Heuristic 2, Match Between System & Real World)_
What I see: The access summary shows "Service accts" as a label. This abbreviation is jarring compared to the otherwise formal tone.
Suggestion: Changed to "Service accounts" — full label fits within the access stat card.
**Status: Fixed in v3**

**Issue 12: Proactive insight has no user control** _(Heuristic 3, User Control & Freedom)_
What I see: The proactive insight card appears unsolicited with "Show impact" and "Dismiss" buttons, but there is no way to control what types of proactive insights appear or to mute them entirely.
Why it matters: Unsolicited notifications can feel intrusive if users cannot control their frequency or categories. Users need a sense of agency over what the AI surfaces proactively.
Suggestion: Added a "Settings" button in the proactive insight header that opens a small popover with checkbox controls for insight categories (schema change alerts, stale access warnings, quality degradation, new data source activity). This gives users granular control without leaving the conversation.
**Status: Fixed in v3**

**Issue 13: Classification list uses inline styles** _(Heuristic 8, Heuristic 4)_
What I see: The classification results in Turn 3 use extensive inline styles for layout. This makes the code harder to maintain and breaks consistency with the rest of the design system approach.
Suggestion: Created reusable CSS classes: `.classification-list`, `.classification-row`, `.classification-col-name`, `.classification-tag-sm`, `.classification-confidence`, `.classification-confidence.low`. Replaced inline styles in the HTML.
**Status: Fixed in v3**

**Issue 14: Detail panel tabs lack keyboard support and ARIA** _(Heuristic 7, Accessibility)_
What I see: The detail panel tabs (Lineage, Schema, Access, Quality) have no keyboard navigation (Arrow Left/Right) and no ARIA `role="tablist"` / `role="tab"` / `aria-selected` attributes.
Suggestion: Added `role="tablist"` on container, `role="tab"` and `aria-selected` on each tab, `tabindex` management. Added Arrow Left/Right keyboard handler that moves focus and activates tabs.
**Status: Fixed in v3**

**Issue 15: User messages cannot be edited** _(Heuristic 3, Conversation Flow Quality)_
What I see: Once a user message is sent, there is no way to edit it. In multi-turn conversations, users often want to refine their question without starting over.
Why it matters: Editing a previous message is a standard pattern in chat tools (ChatGPT, Claude). Without it, users must scroll down and retype, which breaks the conversational flow and wastes effort.
Suggestion: Added a hover-revealed "Edit" button on user messages. The button appears on the right side of the timestamp row, using a subtle tertiary color that doesn't distract from the message content.
**Status: Fixed in v3 (UI pattern added, full edit flow requires JS implementation)**

---

## Laws of UX Applied

| Law | Application | Assessment |
|-----|-------------|------------|
| **Jakob's Law** | Chat panel follows Slack/Teams/ChatGPT conventions; undo toast follows Google Docs/Gmail pattern | Improved -- undo toast is a familiar recovery pattern |
| **Fitts's Law** | Panel header buttons increased to 36px; mobile responsive makes full-width targets | Improved -- meets desktop minimum, mobile addresses touch targets |
| **Hick's Law** | Quick-start prompts offer 4 focused choices; insight settings offer 4 toggles | Good -- manageable number of choices in each context |
| **Miller's Law** | Response cards chunk info well; classification list groups 5 items clearly | Good -- progressive disclosure reduces cognitive load |
| **Law of Proximity** | Response card footer groups copy + feedback logically separated | Good -- left (utility) vs right (evaluation) grouping |
| **Law of Common Region** | Error callout, skeleton card, and undo toast each have clear visual boundaries | Good -- distinct regions communicate distinct purposes |
| **Von Restorff Effect** | Error callout uses red accent; undo toast uses dark background | Good -- error state is visually distinct from success states |
| **Doherty Threshold** | Skeleton loader provides immediate shape feedback; retry spinner shows progress | Improved -- users see response structure within 100ms |
| **Peak-End Rule** | Undo toast provides a satisfying "safety net" ending after actions; copy confirmation gives positive micro-feedback | Improved -- action completion now has a positive finish |
| **Tesler's Law** | AI absorbs complexity of cross-source search; undo absorbs complexity of reversal | Excellent -- irreducible complexity managed by the system |
| **Postel's Law** | Input remains flexible (natural language); error recovery is liberal (retry, dismiss) | Good -- multiple recovery paths for each failure |
| **Gestalt: Pragnanz** | Skeleton loader mirrors response card shape, setting correct expectations | New application -- skeleton shape reduces ambiguity |
| **Paradox of Active User** | Quick-start prompts in empty state eliminate the blank canvas problem | New application -- users engage without reading documentation |

---

## Design System Compliance

| Check | Status | Notes |
|-------|--------|-------|
| Color tokens used correctly | Pass | All new components use semantic tokens |
| Spacing tokens consistent | Pass | `--sds-space-*` used throughout new components |
| Typography scale correct | Pass | Error callout, toast, footer all follow type scale |
| Border radius conventions | Pass | 12px cards, 8px components, 6px small elements, 4px tags |
| Hover states consistent | Pass | All new interactive elements get `--sds-bg-subtle` on hover |
| Active/selected states | Pass | Feedback buttons use blue-750 color when active; filled icon variant |
| Status colors semantic | Pass | Error callout uses `--sds-status-error-*`; undo toast uses custom dark bg |
| Touch targets | Improved | Panel buttons increased to 36px (from 28px) |
| Responsive behavior | New | Breakpoints at 768px and 480px match SDS grid patterns |
| Font weight mapping | Warning (carried from R1) | `--sds-weight-medium` (500) still not in @font-face stack |

---

## Summary of Changes in v3

### Critical Fixes (3)
1. Added error callout component for failed queries/actions with retry and dismiss
2. Added undo toast pattern with 8-second timer bar for reversible actions
3. Added copy-to-clipboard and thumbs up/down feedback on every response card

### Major Fixes (5)
4. Added skeleton loader component matching response card shape
5. Added mobile responsive breakpoints (768px and 480px)
6. Added conversation history management (export, clear) in empty state
7. Added contextual quick-start prompts in empty state
8. Added message retry via error callout retry button

### Minor Fixes (7)
9. Increased panel header buttons from 28px to 36px (Fitts's Law)
10. Added persistent keyboard shortcut badge in panel header
11. Changed "Service accts" to "Service accounts"
12. Added proactive insight settings popover with category toggles
13. Replaced inline styles with reusable CSS classes for classification list
14. Added ARIA roles and keyboard navigation to detail panel tabs
15. Added edit button (hover-revealed) on user messages

---

## Recommendations for Round 3

1. **Conversation branching**: Allow editing a previous message to fork the conversation, showing the original thread grayed out and the new branch continuing
2. **Agent thinking/reasoning transparency**: Show a collapsible "thinking" section that reveals what sources the agent queried and in what order
3. **Bulk action patterns**: When multiple tables match a query, allow selecting a subset for batch classification or flagging
4. **Keyboard shortcut overlay**: Cmd+? to show a full shortcut cheat sheet overlay
5. **Panel width persistence**: Save user's preferred panel width to localStorage so it survives page reloads
6. **Proactive insight frequency/timing controls**: Allow users to set quiet hours or frequency limits for proactive insights
7. **Offline/degraded mode**: Show cached conversation history and a clear "offline" banner when connection drops
8. **Export conversation to ticket**: One-click export of a conversation thread to Jira/ServiceNow as a compliance artifact
9. **Multi-select in result items**: Allow selecting multiple tables from a response to take batch actions
10. **Animation polish**: Add entrance animations for new messages, response card footer, and undo toast that feel smooth and intentional
