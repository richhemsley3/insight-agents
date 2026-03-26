# Design Critique: Chat-Based AI Agent Prototype
**Round**: 1 of 3
**Stage**: Prototype (interactive HTML)
**Date**: 2026-03-26
**File reviewed**: `prototype-chat.html`
**Fixed version**: `prototype-chat-v2.html`

---

## Overall Impression

The prototype demonstrates a strong foundational vision for an integrated AI assistant within a data management platform. The conversation flow is well-structured with a natural Find > Understand > Act progression, and the response cards provide rich, scannable information. However, the prototype has critical accessibility gaps (no ARIA roles, no keyboard navigation in key components, no focus management), missing system status feedback (no typing indicator, no connection status), and insufficient action confirmation for significant operations. The visual design is well-executed with proper use of SDS tokens, though a few interaction patterns need tightening.

---

## Heuristic Scorecard

| # | Heuristic | Score | Notes |
|---|-----------|-------|-------|
| 1 | Visibility of System Status | 2/5 | No typing/loading indicator when AI processes. No connection status. No progress feedback on actions (classify, flag). Timestamps missing on messages. |
| 2 | Match Between System & Real World | 4/5 | Good domain language (PII, lineage, classification). Natural conversational tone. Confidence percentages are clear. Minor: "Service accts" abbreviation is jarring. |
| 3 | User Control & Freedom | 3/5 | Escape key closes panel (good). No undo for flagging/classification actions. No way to resize panel between sidebar and full-screen. No cancel mid-action. |
| 4 | Consistency & Standards | 4/5 | Consistent SDS token usage throughout. Button patterns are uniform. Minor: `--sds-weight-medium` (500) referenced but not in font-face declarations (only 300/400/600/700/800). |
| 5 | Error Prevention | 2/5 | Send button active even when input is empty. Significant actions (flag access, run classification) execute without confirmation dialog. No validation on destructive operations. |
| 6 | Recognition Rather Than Recall | 4/5 | Suggested follow-up chips reduce recall. Response cards show inline metadata. Recent conversations visible in empty state. Minor: follow-up prompt in italic without clear "suggested" label. |
| 7 | Flexibility & Efficiency of Use | 3/5 | Cmd+K shortcut is good. Context menu on right-click is efficient. Missing: panel resize for power users, no keyboard shortcut hints in context menu, no bulk actions in catalog. |
| 8 | Aesthetic & Minimalist Design | 4/5 | Clean visual hierarchy. Cards are well-structured. Warm neutrals with cool accents follow SDS principles. Minor: classification result list uses heavy inline styles rather than reusable classes. |
| 9 | Error Recovery | 2/5 | No error states shown for failed actions. No retry mechanism visible. If classification fails for a column, no recovery path is suggested. No undo toast pattern. |
| 10 | Help & Documentation | 3/5 | Keyboard hint appears on load (good). Empty state explains capabilities. Missing: tooltip on AI icon explaining what it does, no onboarding for first-time users, no "What can I ask?" help within the panel. |

**Overall: 3.1/5**

---

## What's Working Well

1. **Natural conversation progression** (Heuristic 2, Jakob's Law) -- The Find > Understand > Act flow mirrors how data professionals actually work. Starting with "Where is customer PII stored?" and naturally progressing to access review and then classification is exactly the mental model users would have.

2. **Rich response cards with inline actions** (Heuristic 6, Law of Common Region) -- Response cards group related information within clear visual boundaries. The expandable result items with inline "View in catalog" and "See lineage" actions reduce context-switching and keep users in flow.

3. **Proactive insight differentiation** (Von Restorff Effect) -- The proactive insight card uses a distinct left border accent (blue-450), different background (blue-025), and an "AI Insight" label header, making it clearly distinguishable from user-initiated responses. This is well-executed.

4. **Source attribution on every response** (Trust & Transparency) -- The attribution footer on each response card ("Searched 3,240 assets across 5 connected sources") builds trust by showing provenance. This is critical for enterprise data tools.

5. **Context menu integration** (Heuristic 7, Fitts's Law) -- Right-click context menu on catalog table rows with AI-specific actions is a power-user efficiency feature that feels native to the platform rather than bolted on.

6. **Confidence indicators on classifications** (Trust & Transparency) -- Showing per-column confidence percentages with a highlighted low-confidence item (product_category at 72%) helps users understand AI certainty and know where human review is needed.

7. **Warning callout for stale access** (Heuristic 5, Selective Attention) -- The yellow warning callout with left border accent draws attention to the security risk without being alarming. Positioned correctly within the access response context.

---

## Areas for Improvement

### Critical Issues (Must Fix)

**Issue 1: No ARIA roles or accessibility landmarks** _(Heuristic 10, Accessibility)_
What I see: The prototype has no `role` attributes, no `aria-label` on interactive regions, no `aria-expanded` on the agent toggle, no `aria-live` region for dynamic chat messages, and no `aria-hidden` on decorative icons. Screen readers cannot navigate or understand the interface.
Why it matters: Enterprise tools must meet WCAG 2.1 AA. Without ARIA, keyboard-only and screen reader users are completely blocked. This is a legal and ethical requirement.
Suggestion: Added `role="complementary"` on agent panel, `role="log" aria-live="polite"` on messages area, `aria-expanded` on toggle button, `aria-hidden="true"` on all decorative Material icons, `role="menu"` and `role="menuitem"` on context menu.
**Status: Fixed in v2**

**Issue 2: No keyboard navigation in context menu** _(Heuristic 7, Heuristic 3)_
What I see: The context menu opens on right-click/button click but has no keyboard support. Arrow keys don't navigate menu items. No focus trap. Tab doesn't close the menu. Users relying on keyboard cannot use AI context actions.
Why it matters: Context menus are a core interaction pattern. Without keyboard support, a significant portion of the feature is inaccessible. Violates Fitts's Law for keyboard users.
Suggestion: Added Arrow Up/Down navigation, focus management on open (first item focused), Escape to close, Tab to dismiss.
**Status: Fixed in v2**

**Issue 3: Significant actions execute without confirmation** _(Heuristic 5, Error Prevention)_
What I see: When the user says "flag it and classify the untagged columns," the AI immediately executes both actions. Flagging creates a review ticket (SEC-1247) and classification modifies column metadata -- both are consequential operations with no confirmation step.
Why it matters: In enterprise data governance, modifying access flags and classification metadata can trigger downstream policy enforcement. Users need a confirmation gate before irreversible actions. This violates Error Prevention and User Control & Freedom.
Suggestion: Added a confirmation dialog component with proper focus trap, Escape key support, and Cancel as the default-focused button. Added a "Confirmed and completed" label to action results to make it clear the confirmation step occurred.
**Status: Fixed in v2**

**Issue 4: No focus management on panel open/close** _(Heuristic 3, User Control & Freedom)_
What I see: When the agent panel opens, focus stays on the toggle button rather than moving to the input field. When the panel closes, focus is lost entirely (no return to trigger element). This breaks keyboard flow.
Why it matters: Focus management is essential for panel/drawer patterns. Without it, keyboard users lose their place and must Tab through the entire page to find where they were.
Suggestion: Focus moves to input field on panel open (with 350ms delay for animation). Focus returns to toggle button on close.
**Status: Fixed in v2**

### Major Issues (Must Fix)

**Issue 5: No typing/loading indicator** _(Heuristic 1, Doherty Threshold)_
What I see: When a query is sent, there is no visual indication that the AI is processing. The user sees nothing between sending their message and receiving the response card. No typing dots, no skeleton loader, no progress bar.
Why it matters: Response latency for AI queries in data platforms can range from 2-10 seconds. Without a typing indicator, users may think the system is broken and re-submit, or lose confidence. The Doherty Threshold (400ms) means we need immediate visual feedback.
Suggestion: Added CSS for a typing indicator component (animated bouncing dots) ready for integration in the message flow. Status bar added showing connection state.
**Status: CSS added in v2, integration point documented**

**Issue 6: Send button active when input is empty** _(Heuristic 5, Error Prevention)_
What I see: The send button is visually active and clickable even when the textarea is empty. Clicking it would send an empty message.
Why it matters: This is a basic error prevention failure. Users should not be able to trigger an action with invalid state. Wastes API calls and creates confusion.
Suggestion: Added `.disabled` state on send button when input is empty. Button uses `--sds-color-warm-gray-200` background and `cursor: not-allowed`. State updates on every `input` event. Enter key also checks for non-empty content.
**Status: Fixed in v2**

**Issue 7: No panel resize capability** _(Heuristic 3, Heuristic 7)_
What I see: The agent panel is fixed at 440px wide (sidebar) or full-width (fullscreen). There is no intermediate state. Users cannot adjust the panel width to balance conversation viewing with the main content.
Why it matters: Power users need flexibility. A 440px panel may be too narrow for complex response cards (access matrices, lineage) but full-screen is overkill for simple queries. This violates Flexibility and Efficiency of Use.
Suggestion: Added a drag-to-resize handle on the left edge of the panel. Min width 360px, max 800px. Visual feedback on hover/drag. Disabled in fullscreen mode.
**Status: Fixed in v2**

**Issue 8: No timestamps on messages** _(Heuristic 1, Visibility of System Status)_
What I see: Chat messages have no time indicators. Users cannot tell when a conversation happened, how old the responses are, or the temporal relationship between messages.
Why it matters: In enterprise contexts, knowing when an access review was run or when a classification was applied is critical for audit trails. Timestamps also help users understand data freshness.
Suggestion: Added timestamps (e.g., "9:14 AM") to each message turn using a new `.agent-msg-time` class.
**Status: Fixed in v2**

**Issue 9: Suggested follow-up lacks visual distinction** _(Heuristic 6, Von Restorff Effect)_
What I see: The "Would you like me to flag the stale service account access for review?" prompt is styled as italicized text in a subtle gray box. There is no label indicating this is a suggested action, no visual emphasis distinguishing it from response body text.
Why it matters: Follow-up suggestions are a key interaction pattern for chat AI -- they reduce recall and guide users toward productive next steps. If users miss them, the conversational flow breaks.
Suggestion: Added a "Suggested follow-up" overline label above the suggestion text, using the standard SDS overline pattern (11px, uppercase, tracking-wide).
**Status: Fixed in v2**

### Minor Issues (Fixed Where Straightforward)

**Issue 10: Decorative icons not hidden from screen readers** _(Accessibility)_
What I see: Material Symbols icons throughout the UI (search, notifications, chevrons, etc.) are read by screen readers as their text content ("search", "expand_more", etc.), creating noise.
Suggestion: Added `aria-hidden="true"` to all decorative icons. Interactive icons that serve as the only label get `aria-label` on the parent button instead.
**Status: Fixed in v2**

**Issue 11: No reduced-motion support** _(Accessibility, WCAG 2.3.3)_
What I see: Multiple animations (panel slide, fade-in, pulse, spin) with no `prefers-reduced-motion` media query.
Suggestion: Added `@media (prefers-reduced-motion: reduce)` that sets all animation and transition durations to near-zero.
**Status: Fixed in v2**

**Issue 12: No skip-to-content link** _(Accessibility)_
What I see: Keyboard users must Tab through the entire header and sidebar navigation before reaching main content.
Suggestion: Added a visually hidden skip link that appears on focus, jumping to `#mainContent`.
**Status: Fixed in v2**

**Issue 13: Agent panel status bar missing** _(Heuristic 1)_
What I see: No indication of whether the AI system is connected, what version is running, or system health.
Suggestion: Added a compact status bar below the panel header showing "Connected -- Strata AI v3.2" with a green dot indicator.
**Status: Fixed in v2**

**Issue 14: `--sds-weight-medium` (500) not in font-face declarations** _(Heuristic 4, Consistency)_
What I see: The CSS defines `--sds-weight-medium: 500` and uses it in multiple places, but the `@font-face` declarations only include weights 300, 400, 600, 700, 800. The browser will synthesize weight 500, which may render differently than intended.
Suggestion: Noted for design system team. The token should map to 400 or 600, or a Medium weight font file should be added.
**Status: Documented, requires design system update**

---

## Laws of UX Applied

| Law | Application | Assessment |
|-----|-------------|------------|
| **Jakob's Law** | Chat panel pattern follows Slack/Teams/ChatGPT conventions | Good -- sidebar panel, message bubbles, and input area match established patterns |
| **Fitts's Law** | Touch targets for panel buttons (28px) are below the 44px recommended minimum | Needs attention -- panel header buttons should be at least 32px with 8px spacing |
| **Hick's Law** | Context menu has 6 items, suggestion chips have 3 | Good -- manageable number of choices |
| **Miller's Law** | Response cards chunk information well (summary > details > actions) | Good -- progressive disclosure reduces cognitive load |
| **Law of Proximity** | Result item metadata (tag, detail count, quality score) grouped tightly | Good -- clear visual grouping |
| **Law of Common Region** | Response cards and result items use borders/backgrounds to group | Good -- well-defined boundaries |
| **Von Restorff Effect** | Proactive insight card visually distinct | Good -- left border + background differentiates from standard responses |
| **Doherty Threshold** | No typing indicator for AI processing time | Critical gap -- must show immediate feedback |
| **Peak-End Rule** | No completion feedback after actions execute | Gap -- the "Flagged" confirmation is good, but no celebration/satisfaction moment |
| **Tesler's Law** | AI absorbs complexity of cross-source PII search | Excellent -- reduces irreducible complexity for users |
| **Postel's Law** | Input is flexible (natural language) | Good -- liberal in what it accepts |
| **Gestalt: Similarity** | User messages (blue bubble) vs AI messages (flat cards) clearly different | Good -- clear sender differentiation |

---

## Design System Compliance

| Check | Status | Notes |
|-------|--------|-------|
| Color tokens used correctly | Pass | All semantic tokens properly applied |
| Spacing tokens consistent | Pass | `--sds-space-*` used throughout |
| Typography scale correct | Pass | Type hierarchy follows SDS scale |
| Border radius conventions | Pass | 12px cards, 8px components, 6px small elements |
| Hover states consistent | Pass | `--sds-bg-subtle` on all interactive elements |
| Active/selected states | Pass | Blue-100 bg + blue-750 text pattern followed |
| Status colors semantic | Pass | Error/warning/success/info tokens correctly mapped |
| Font weight mapping | Warning | `--sds-weight-medium` (500) not in @font-face stack |
| Transition easing | Note | Uses `cubic-bezier(0.16, 1, 0.3, 1)` for panels (spring) -- differs from SDS standard `cubic-bezier(0.4, 0, 0.2, 1)` but appropriate for slide animations |

---

## Summary of Changes in v2

### Critical Fixes (5)
1. Added comprehensive ARIA roles and landmarks throughout
2. Added keyboard navigation to context menu (Arrow keys, Escape, Tab)
3. Added confirmation dialog component for destructive/significant actions
4. Added focus management on panel open (input focused) and close (toggle button focused)
5. Added `aria-live="polite"` region for dynamic chat content

### Major Fixes (5)
6. Added typing indicator CSS component
7. Send button disabled when input is empty
8. Added panel resize via drag handle (360px-800px range)
9. Added timestamps to all messages
10. Added "Suggested follow-up" label to follow-up prompts

### Minor Fixes (4)
11. Added `aria-hidden="true"` on all decorative icons
12. Added `prefers-reduced-motion` media query
13. Added skip-to-content link
14. Added agent panel status bar (connection indicator + version)

---

## Recommendations for Round 2

1. **Error states**: Add error handling UI for failed AI queries, network issues, and partial action failures
2. **Undo pattern**: Implement toast-based undo for flagging and classification actions
3. **Loading skeletons**: Replace typing indicator with response card skeleton loaders
4. **Mobile responsiveness**: Panel should become full-screen on viewports under 768px
5. **Panel header touch targets**: Increase button sizes from 28px to at least 36px
6. **Empty state enhancement**: Add contextual quick-start prompts based on current page
7. **Conversation history management**: Add ability to name, search, and delete past conversations
8. **Detail panel interaction**: Full-screen detail panel tabs need keyboard support and ARIA roles
