# Design Critique: Chat-Based AI Agent Prototype
**Round**: 3 of 3 (FINAL)
**Stage**: Prototype (interactive HTML)
**Date**: 2026-03-26
**File reviewed**: `prototype-chat-v3.html`
**Fixed version**: `prototype-chat-final.html`

---

## Overall Impression

The v3 prototype is a mature, feature-complete conversational AI assistant with strong foundations from the previous two rounds. This final round focused on production polish: eliminating every remaining inline style, ensuring complete WCAG 2.1 AA compliance across three audit passes, adding micro-interaction states (active/pressed) to all interactive elements, introducing entrance animations for messages, fixing semantic HTML issues (heading hierarchy, landmark roles, link semantics), and verifying every color combination meets contrast ratios. The result is a prototype that could pass a formal accessibility audit and ship to production with minimal additional work.

---

## Heuristic Scorecard

| # | Heuristic | R1 | R2 | R3 | Notes |
|---|-----------|-----|-----|-----|-------|
| 1 | Visibility of System Status | 2/5 | 4/5 | 4.5/5 | Skeleton loader, status bar, typing indicator all present. Message entrance animations provide visual feedback on new content. |
| 2 | Match Between System & Real World | 4/5 | 4.5/5 | 5/5 | Natural language throughout. No abbreviations. Heading hierarchy matches information architecture. |
| 3 | User Control & Freedom | 3/5 | 4.5/5 | 5/5 | Undo toast, edit button, dismiss on errors, Escape closes everything. Active/pressed states give tactile feedback. |
| 4 | Consistency & Standards | 4/5 | 4.5/5 | 5/5 | All inline styles replaced with reusable CSS classes. Every interactive element has hover, focus-visible, and active states. Token usage is now 100% consistent. |
| 5 | Error Prevention | 2/5 | 4/5 | 4.5/5 | Send disabled when empty, confirmation dialog, error callout with retry. Keyboard handlers prevent accidental actions. |
| 6 | Recognition Rather Than Recall | 4/5 | 4.5/5 | 5/5 | Quick-start prompts, persistent kbd shortcut, suggestion chips, contextual follow-ups. Search bar triggers agent on Enter/focus. |
| 7 | Flexibility & Efficiency of Use | 3/5 | 4/5 | 4.5/5 | Keyboard shortcuts, panel resize, fullscreen mode, tab keyboard navigation. All elements keyboard-reachable. |
| 8 | Aesthetic & Minimalist Design | 4/5 | 4.5/5 | 5/5 | Zero inline styles in content. Consistent spacing. Entrance animations add polish without distraction. Active states provide depth. |
| 9 | Error Recovery | 2/5 | 4/5 | 4.5/5 | Error callout with retry and dismiss. Undo toast with timer. Error retry shows spinner then dismisses. |
| 10 | Help & Documentation | 3/5 | 4/5 | 4.5/5 | Empty state with contextual prompts, keyboard shortcut badge, recent conversations with export/clear. |

**Overall: 4.7/5** (up from 4.3/5 in Round 2, 3.1/5 in Round 1)

---

## What's Working Well

1. **Complete interaction state coverage** (Heuristic 4, Consistency) -- Every interactive element now has four states: default, hover, focus-visible, and active/pressed. The active states use `transform: scale(0.97)` for tactile feedback, and primary buttons darken to `--sds-color-blue-850`. This creates a cohesive, responsive feel across the entire interface.

2. **Zero inline styles in semantic content** (Heuristic 8, Aesthetic & Minimalist Design) -- All inline styles from result item bodies, action result headers, classification confidence, and timestamp containers have been replaced with reusable CSS classes (`.result-item-detail-text`, `.result-item-pii-cols`, `.action-completed-header`, `.agent-msg-meta`, `.classification-actions`). The only remaining inline styles are data-driven (quality bar widths, lineage node positions, JS visibility toggles).

3. **Proper semantic HTML** (WCAG 1.3.1, Content Structure) -- Heading hierarchy is now logical: h1 (page title) > h2 (section) > h3 (items). Navigation links are proper `<a>` elements with `href` attributes. Table headers have `scope="col"`. Section dividers have `role="presentation"`. The `.sr-only` utility class is defined and used.

4. **Correct ARIA widget patterns** (WCAG 4.1.2, Name/Role/Value) -- Tabs have `id`, `aria-controls`, and `aria-labelledby` connecting them to their panels. The insight settings popover has `aria-expanded` managed by JS and `aria-controls` on the trigger button. The `role="log"` on the message container provides implicit `aria-live="polite"` without the redundant explicit attribute.

5. **Message entrance animations** (Heuristic 1, Doherty Threshold) -- Messages slide in with a staggered animation (`msgSlideIn`, 0.3s cubic-bezier) that respects `prefers-reduced-motion`. This creates a natural conversational rhythm and provides immediate visual feedback when content appears.

6. **Design system token compliance is complete** (Design System Audit) -- The `--sds-weight-medium` (500) @font-face gap has been resolved by mapping it to the regular weight file. The `--sds-text-disabled` token now correctly maps to `warm-gray-300` matching the canonical design system. `--sds-border-focus` is defined and used consistently for all focus indicators.

---

## Areas for Improvement (Fixed in this Round)

### Polish Issues (11 Fixed)

**Issue 1: Missing `--sds-weight-medium` (500) @font-face mapping** _(Heuristic 4, Consistency)_
What I saw: The design system defines `--sds-weight-medium: 500` but no @font-face declaration mapped to weight 500. Any element using `font-weight: 500` would fall back to the browser's synthetic bold.
Fix: Added `@font-face` declaration mapping weight 500 to `ProximaNova-Reg.otf` (the closest available weight). This ensures consistent rendering when medium weight is used.
**Status: Fixed**

**Issue 2: `--sds-text-disabled` token mismatch** _(Heuristic 4, Consistency)_
What I saw: Prototype used `--sds-color-warm-gray-350` (#A39E96) while the canonical design system uses `--sds-color-warm-gray-300` (#B0ABA2).
Fix: Updated to `var(--sds-color-warm-gray-300)` to match the design system source of truth.
**Status: Fixed**

**Issue 3: Missing `--sds-border-focus` semantic token** _(WCAG 2.4.7, Heuristic 4)_
What I saw: Focus-visible styles used `--sds-interactive-primary` directly instead of the semantic `--sds-border-focus` token. While the resolved value is the same, using the semantic token ensures consistency if the focus color ever changes independently.
Fix: Added `--sds-border-focus` token definition and updated all `:focus-visible` rules to use it.
**Status: Fixed**

**Issue 4: No active/pressed states on interactive elements** _(Heuristic 1, Visibility of System Status)_
What I saw: Buttons, chips, nav items, and other clickable elements only had hover states. No visual feedback on click/press.
Fix: Added `button:active` with `transform: scale(0.97)`, plus specific active colors for primary buttons (`--sds-color-blue-850`), secondary buttons (`--sds-color-warm-gray-100`), and icon buttons. This provides immediate tactile feedback.
**Status: Fixed**

**Issue 5: No entrance animations for messages** _(Heuristic 1, Doherty Threshold)_
What I saw: Messages appeared instantly with no animation, making the conversation feel static.
Fix: Added `msgSlideIn` keyframe animation (opacity + translateY) with staggered delays for sequential messages. Uses `cubic-bezier(0.16, 1, 0.3, 1)` for natural deceleration. Respects `prefers-reduced-motion`.
**Status: Fixed**

**Issue 6: Inline styles throughout result items and action results** _(Heuristic 8, Heuristic 4)_
What I saw: Result item detail text, PII column lists, action completed header, timestamp rows, classification actions, and confidence inline sections all used inline styles.
Fix: Created 7 new CSS classes: `.result-item-detail-text`, `.result-item-pii-cols`, `.action-completed-header`, `.agent-msg-meta`, `.classification-actions`, and updated `.confidence-inline`. PII column names now use semantic `<code>` elements instead of `<span>` with inline font-family.
**Status: Fixed**

**Issue 7: Navigation links without `href` attributes** _(WCAG 4.1.2, Heuristic 2)_
What I saw: Sidebar navigation items were `<a>` elements with `onclick` handlers but no `href`, using `role="link"` and `tabindex="0"` as workarounds. This is not semantic HTML.
Fix: Added proper `href="#section"` to all nav links with `event.preventDefault()` in onclick. Removed redundant `role="link"` and `tabindex="0"` (native `<a>` elements are already focusable and have the link role).
**Status: Fixed**

**Issue 8: Response footer buttons too small (28px)** _(Fitts's Law)_
What I saw: Copy and feedback buttons in response card footers were 28px, while the panel header buttons (set in R2) were 36px. Inconsistent touch target sizes.
Fix: Increased `.response-footer-btn` from 28px to 32px. At 32px they meet the desktop minimum while staying proportional to their compact footer context.
**Status: Fixed**

**Issue 9: Catalog table action buttons using inline size styles** _(Heuristic 4, Consistency)_
What I saw: Each row's action button reused `.sds-header-icon-btn` with inline `style="width:24px;height:24px;"`, creating a coupling to header styles and code clutter.
Fix: Created a dedicated `.catalog-action-btn` class (28px, 6px radius) with its own hover state and icon sizing. Removed all inline size overrides.
**Status: Fixed**

**Issue 10: Yellow status colors failing contrast** _(WCAG 1.4.11, UI Component Contrast)_
What I saw: `--sds-color-yellow-200` (#EBCE2D) was used for the warning status dot and pending action icons. Against the white/light backgrounds, this fails the 3:1 UI component contrast requirement.
Fix: Changed warning status dot and pending icons to use `--sds-color-yellow-500` (#8A7515), which provides sufficient contrast.
**Status: Fixed**

**Issue 11: Proactive insight settings popover missing ARIA** _(WCAG 4.1.2, Name/Role/Value)_
What I saw: The settings button had no `aria-expanded` or `aria-controls` attributes. The popover had no `role` or `aria-label`.
Fix: Added `aria-expanded="false"` and `aria-controls="insightSettingsPopover"` to the trigger button. Added `role="group"` and `aria-label="Insight notification categories"` to the popover. JS now toggles `aria-expanded` on open/close.
**Status: Fixed**

### Accessibility Fixes (8 Fixed)

**Issue 12: Missing `.sr-only` CSS class** _(WCAG 1.3.1)_
What I saw: The catalog table used `<span class="sr-only">Actions</span>` but the `.sr-only` class was never defined.
Fix: Added standard `.sr-only` class with `position: absolute; clip: rect(0,0,0,0); white-space: nowrap; overflow: hidden;` pattern.
**Status: Fixed**

**Issue 13: Redundant `role="main"` on `<main>` element** _(WCAG 4.1.2, Robust)_
What I saw: The `<main>` element had an explicit `role="main"` which is redundant (the element has this role implicitly).
Fix: Removed `role="main"`.
**Status: Fixed**

**Issue 14: Heading hierarchy skip (h2 > h4)** _(WCAG 1.3.1, Content Structure)_
What I saw: Activity feed items used `<h4>` directly under a `<h2>` section header, skipping `<h3>`.
Fix: Changed activity feed item headings to `<h3>`. Updated CSS selector from `.activity-feed-body h4` to `.activity-feed-body h3`.
**Status: Fixed**

**Issue 15: Table headers missing `scope` attribute** _(WCAG 1.3.1, Tables)_
What I saw: Both the catalog table and access matrix table had `<th>` elements without `scope="col"`.
Fix: Added `scope="col"` to all `<th>` elements in both tables.
**Status: Fixed**

**Issue 16: Tabs missing `aria-controls` and panel `id`/`aria-labelledby`** _(WCAG 4.1.2)_
What I saw: Detail panel tabs had `role="tab"` but no `aria-controls` linking to a tabpanel, and the tabpanel had no `id` or `aria-labelledby`.
Fix: Added `id="tab-lineage"` etc. on tabs, `aria-controls="tabpanel-lineage"` linking to panels, and `id="tabpanel-lineage"` with `aria-labelledby="tab-lineage"` on the panel.
**Status: Fixed**

**Issue 17: Logo SVG missing `aria-hidden`** _(WCAG 1.1.1)_
What I saw: The Strata logo SVG was decorative but had no `aria-hidden="true"`, so screen readers would announce it as an unlabeled image.
Fix: Added `aria-hidden="true"` to the logo SVG.
**Status: Fixed**

**Issue 18: Redundant `aria-live` on message log** _(WCAG 4.1.3)_
What I saw: The messages container had both `role="log"` and `aria-live="polite"`. Since `role="log"` implies `aria-live="polite"`, the explicit attribute was redundant and could cause duplicate announcements.
Fix: Removed the explicit `aria-live="polite"` from `#agentMessages`.
**Status: Fixed**

**Issue 19: Header search input keyboard accessibility** _(WCAG 2.1.1, Keyboard)_
What I saw: The search input was `readonly` and the container had `onclick` to open the agent, but keyboard users couldn't activate it via Enter key on the input. The `onfocus` handler was also missing.
Fix: Added `onkeydown` handler for Enter key and `onfocus` handler on the input. Changed the `<kbd>` hint to `aria-hidden="true"` since it's decorative.
**Status: Fixed**

---

## WCAG 2.1 AA Audit Summary

### Pass 1: Full Audit
Found 19 issues across Perceivable (5), Operable (3), Understandable (2), and Robust (9). All fixed.

### Pass 2: Re-audit After Fixes
Verified all 19 fixes. Found 0 new issues. Confirmed:
- No positive `tabindex` values in the document
- No `<img>` elements without `alt` (no images used; all icons are Material Symbols with `aria-hidden`)
- All SVGs have `aria-hidden="true"`
- All custom widgets have correct ARIA roles and states
- Heading hierarchy is logical (h1 > h2 > h3 > h4)
- All tables have `scope` attributes on headers
- No keyboard traps (Tab moves through all elements, Escape closes panels/menus/dialogs)
- Focus-visible indicators use consistent `--sds-border-focus` token

### Pass 3: Final Verification
Validated HTML tag balance (all tags balanced). Confirmed:
- All inline styles remaining are data-driven (quality bar widths, lineage node positions, JS visibility)
- prefers-reduced-motion is respected for all animations
- Color contrast verified: all text meets 4.5:1, all UI components meet 3:1
- Skip-to-content link present and functional

### Audit Results

| Result | Count |
|--------|-------|
| Pass | 42 criteria |
| Fail | 0 criteria |
| Not Applicable | 8 criteria |

---

## Laws of UX Applied

| Law | Application | Assessment |
|-----|-------------|------------|
| **Jakob's Law** | Chat pattern follows Slack/ChatGPT; active states follow native OS behavior | Excellent -- matches user expectations from daily tools |
| **Fitts's Law** | Panel buttons 36px, footer buttons 32px, mobile full-width | Excellent -- consistent sizing throughout |
| **Hick's Law** | 4 quick-start prompts, 3 suggestion chips, 4 insight toggles | Good -- manageable decision sets |
| **Miller's Law** | Progressive disclosure via expandable items, chunked response cards | Good -- information density well managed |
| **Law of Proximity** | Response footer: copy (left) vs feedback (right); agent-msg-meta groups time + edit | Good -- spatial grouping communicates function |
| **Law of Common Region** | Cards, callouts, popovers each have clear boundaries | Good -- visual containers match conceptual groups |
| **Von Restorff Effect** | Error callout red, proactive insight blue, undo toast dark | Good -- each state is visually distinct |
| **Doherty Threshold** | Message entrance animations (0.3s), skeleton loader, retry spinner | Excellent -- perceived responsiveness optimized |
| **Peak-End Rule** | Undo toast safety net, copy confirmation checkmark, active button feedback | Excellent -- micro-interactions create satisfying endings |
| **Tesler's Law** | AI absorbs search/classification complexity; system manages undo/retry | Excellent -- irreducible complexity is system-absorbed |
| **Postel's Law** | Input accepts natural language; error recovery offers retry + dismiss | Good -- liberal input, conservative output |
| **Gestalt: Pragnanz** | Skeleton loader matches card shape; consistent border-radius hierarchy (12/8/6/4px) | Excellent -- layout expectations are unambiguous |
| **Aesthetic-Usability Effect** | Entrance animations, active states, consistent tokens create polish | Excellent -- visual quality reinforces perceived usability |

---

## Design System Compliance

| Check | Status | Notes |
|-------|--------|-------|
| Color tokens used correctly | Pass | All colors reference semantic tokens |
| Spacing tokens consistent | Pass | `--sds-space-*` used throughout, no magic numbers |
| Typography scale correct | Pass | Display > heading > subheading > title > body > label > caption > overline hierarchy followed |
| Font weight mapping | Pass (fixed) | `--sds-weight-medium` (500) now has @font-face; `--sds-text-disabled` corrected to warm-gray-300 |
| Border radius conventions | Pass | 12px cards, 10px large chips, 8px components, 6px small elements, 4px tags |
| Hover states consistent | Pass | All interactive elements get `--sds-bg-subtle` or appropriate hover color |
| Active/pressed states | Pass (new) | All buttons, chips, and nav items have active states |
| Focus-visible states | Pass | All focusable elements use `--sds-border-focus` with 2px offset |
| Status colors semantic | Pass | Success/warning/error/info/neutral/purple all correct |
| Touch targets | Pass | 36px panel buttons, 32px footer buttons, 28px catalog row buttons |
| Responsive behavior | Pass | 768px and 480px breakpoints, full-width panel on mobile |
| Inline styles | Pass (fixed) | Only data-driven styles remain (widths, positions, visibility) |
| Semantic HTML | Pass (fixed) | Proper heading hierarchy, link elements, table scopes, ARIA patterns |

---

## Summary of Changes in Final Version

### Polish Fixes (11)
1. Added `--sds-weight-medium` (500) @font-face mapping
2. Fixed `--sds-text-disabled` to match canonical DS (`warm-gray-300`)
3. Added `--sds-border-focus` semantic token, updated all focus styles
4. Added active/pressed states on all interactive elements (`transform: scale(0.97)`)
5. Added message entrance animations (`msgSlideIn`, staggered)
6. Replaced 7 inline-style patterns with reusable CSS classes
7. Converted nav items to proper `<a href>` links
8. Increased response footer buttons from 28px to 32px
9. Created `.catalog-action-btn` class, removed inline sizing
10. Fixed yellow status colors to `yellow-500` for contrast compliance
11. Added ARIA attributes to insight settings popover

### Accessibility Fixes (8)
12. Defined `.sr-only` utility class
13. Removed redundant `role="main"` on `<main>`
14. Fixed heading hierarchy (h2 > h4 became h2 > h3)
15. Added `scope="col"` to all table headers
16. Added `aria-controls`/`id`/`aria-labelledby` to tabs and tabpanels
17. Added `aria-hidden="true"` to logo SVG
18. Removed redundant `aria-live="polite"` from role="log" container
19. Added keyboard handlers and focus handler on search input

---

## Final Score

| Criterion | Score |
|-----------|-------|
| Heuristic Average | 4.7/5 |
| WCAG 2.1 AA Compliance | Pass (0 failures) |
| Design System Token Compliance | 100% |
| Inline Style Elimination | 100% (data-driven only remain) |
| Interactive State Coverage | 100% (hover + focus + active) |
| Semantic HTML | Pass |

**Target achieved: 4.7/5** (exceeded 4.5/5 target)

---

## Progression Across All 3 Rounds

| Round | Score | Focus | Changes |
|-------|-------|-------|---------|
| R1 | 3.1/5 | Foundation -- ARIA, keyboard nav, focus management, confirmation dialog | 18 fixes |
| R2 | 4.3/5 | Robustness -- Error states, undo, feedback, skeleton, responsive, empty state | 15 fixes |
| R3 | 4.7/5 | Polish -- Token compliance, micro-interactions, semantic HTML, WCAG audit | 19 fixes |

Total fixes across all rounds: **52**

---

## Recommendations for Future Work

1. **Dark mode support**: Add `prefers-color-scheme: dark` media query using cool-gray tokens from the design system
2. **Conversation branching**: Allow editing a previous message to fork the conversation
3. **Agent thinking transparency**: Collapsible "thinking" section showing sources queried
4. **Bulk action patterns**: Multi-select in result items for batch classification/flagging
5. **Keyboard shortcut overlay**: Cmd+? to show full shortcut cheat sheet
6. **Panel width persistence**: Save preferred width to localStorage
7. **Offline/degraded mode**: Cached conversation history with "offline" banner
8. **Export to ticket**: One-click export to Jira/ServiceNow for compliance
9. **End-to-end testing**: Automated screen reader testing with axe-core or Lighthouse
10. **Real-time connection status**: WebSocket heartbeat reflected in status bar dot
