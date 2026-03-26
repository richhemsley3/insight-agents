# Design Critique & Heuristic Evaluation: Homepage Prototype — Round 1

**Stage:** Implementation (HTML/CSS prototype)
**Date:** 2026-03-26
**Evaluator:** Claude (design-critique + ux-heuristics skills)
**File:** `prototype-integrated-final.html`

---

## Overall Impression

The redesigned homepage is well-structured with a clear AI-first hierarchy: prompt bar at top, role-specific suggestion chips, compact greeting, insight cards, and a unified Data Health card. The overall architecture is sound. However, the Data Health card and Popular Queries row were built entirely with inline styles, bypassing the design system's token infrastructure. Several interactive elements lacked proper focus-visible states, and a handful of hardcoded pixel/color values undermined DS compliance. All issues identified in this round have been fixed.

---

## Heuristic Scorecard

| Heuristic | Score | Notes |
|-----------|-------|-------|
| Visibility of System Status | 5/5 | Excellent: skeleton loading, AI thinking dots with contextual labels, undo toast, live region for screen readers |
| Match Between System & Real World | 5/5 | Language matches data engineer mental model perfectly; role-specific content adapts |
| User Control & Freedom | 5/5 | Dismiss + undo on insights, clear conversation with confirm, back button, Cmd+K shortcut, Escape handlers |
| Consistency & Standards | 3/5 | **Data Health card and Popular Queries used inline styles instead of DS tokens/classes; now fixed** |
| Error Prevention | 4/5 | Send button hidden until input has text; autocomplete prevents typos; 15s error timeout |
| Recognition Rather Than Recall | 5/5 | Recent queries on focus, suggestion chips, autocomplete type-ahead, role selector with descriptions |
| Flexibility & Efficiency of Use | 5/5 | Cmd+K shortcut, D to dismiss, role switching, suggestion chips for quick access |
| Aesthetic & Minimalist Design | 4/5 | Clean hierarchy; AI badge is icon-only (Von Restorff fix done); some density opportunity in Data Health |
| Error Recovery | 5/5 | Clear error state with retry button, plain language, back-to-dashboard escape |
| Help & Documentation | 5/5 | Onboarding walkthrough (3 steps), help menu with restart tour + keyboard shortcuts |

**Overall: 4.6/5**

---

## What's Working Well

- **AI prompt bar focus management** is excellent. Focus-within styles, Cmd+K global shortcut, scale animation on focus, and the keyboard hint badge all reinforce the bar as the primary interaction point. _(H7: Flexibility & Efficiency)_
- **Insight cards match the DS `.sds-insight` pattern precisely.** Icon slot (36px/8px radius), content column with 4px gap, severity badges, meta dividers, and action links all match the reference component in `insight-cards.html`. _(H4: Consistency)_
- **Progressive disclosure is well-executed.** The homepage shows 3 prioritized insights and a unified Data Health card; "+5 more insights" and "View full report" expand on demand. This avoids alert fatigue. _(Law: Chunking, Occam's Razor)_
- **Role-based content switching** with smooth cross-fade transition provides personalization without page reload. Each role gets tailored chips, insights, and title. _(Law: Mental Model)_
- **Undo pattern for dismissed insights** with 6-second toast + restore is a textbook implementation of User Control & Freedom. _(H3)_
- **Skeleton loading state** provides clear system status during initial 1.2s load, with separate skeletons matching the actual content layout. _(H1: Visibility of System Status)_
- **Skip-to-content link** and `aria-live` region demonstrate accessibility consideration. _(H10: Help & Documentation)_
- **Onboarding walkthrough** with spotlight, progressive dots, skip option, and Escape support is well-implemented. _(Law: Zeigarnik Effect, H10)_

---

## Issues Found & Fixed (Round 1)

### Fixed: F1 — Data Health Card Used All Inline Styles
**Severity:** Major | **Heuristic:** H4 Consistency & Standards
- **Location:** Data Health card (lines ~1760-1834)
- **Problem:** Score display, dimension bars, AI note, next actions, and footer all used hardcoded `px` values in inline `style` attributes (e.g., `font-size: 48px`, `gap: 40px`, `padding: 12px 16px`, `font-weight: 700`). This bypasses the DS token system entirely, making the card fragile to theme changes and inconsistent with the rest of the UI.
- **Fix:** Created semantic CSS classes (`.health-score`, `.health-dimensions`, `.health-bar`, `.health-ai-note`, `.health-action-item`, `.health-card-footer`) using `--sds-*` tokens throughout. Removed all inline styles from the Data Health card HTML.

### Fixed: F2 — Popular Queries Row Used All Inline Styles
**Severity:** Major | **Heuristic:** H4 Consistency & Standards
- **Location:** Popular Queries row (lines ~1839-1849)
- **Problem:** All 3 chips and the row container used inline styles with hardcoded values (`font-size: 12px`, `padding: 4px 12px`, `gap: 8px`). Chips had no focus-visible state.
- **Fix:** Created `.popular-queries-row`, `.popular-queries-label`, `.popular-queries-chips`, and `.popular-chip` classes with proper tokens. Added `:hover`, `:focus-visible`, and `:active` states.

### Fixed: F3 — Data Health Next Actions Missing Keyboard/Focus States
**Severity:** Major | **Heuristic:** H7 Flexibility & Efficiency, Fitts's Law
- **Location:** 3 next-action buttons in Data Health card
- **Problem:** Buttons used `onmouseover`/`onmouseout` inline event handlers for hover state instead of CSS. No focus-visible state for keyboard users. No active/pressed state.
- **Fix:** Replaced with `.health-action-item` class that includes `:hover`, `:focus-visible`, and `:active` states in CSS. Removed inline event handlers.

### Fixed: F4 — Hardcoded Gap/Padding Values Throughout CSS
**Severity:** Minor | **Heuristic:** H4 Consistency & Standards
- **Locations:** `.trust-tag` (gap: 3px), `.inline-trust-tags` (gap: 3px), `.ai-thinking-dots` (gap: 4px), `.blast-node` (gap: 2px), `.role-dropdown-item` (gap: 1px), `.btn-xs` (padding: 2px), `.ai-prompt-bar kbd` (padding: 2px 7px), `.blast-node-sub` (font-size: 10px)
- **Fix:** Replaced all with nearest `--sds-space-*` tokens (`--sds-space-050`, `--sds-space-100`, `--sds-space-200`) and `--sds-type-overline` for the 10px font size.

### Fixed: F5 — Missing focus-visible States on Multiple Interactive Elements
**Severity:** Major | **Heuristic:** H7 Flexibility & Efficiency, Fitts's Law
- **Location:** `.ai-section-link`, `.insights-footer button`, `.hero-stat-link`, `.conversation-back`, `.help-dropdown-item`, `.role-dropdown-item`
- **Problem:** These buttons/links had hover states but no `focus-visible` outline for keyboard navigation.
- **Fix:** Added `focus-visible` styles with `outline: 2px solid var(--sds-border-focus); outline-offset: 2px; border-radius: 4px;` to all affected elements.

### Fixed: F6 — Hero Stat Links Not Keyboard Accessible
**Severity:** Major | **Heuristic:** H7 Flexibility & Efficiency
- **Location:** "14 sources connected" and "3,240 assets cataloged" links in hero subtitle
- **Problem:** `<a>` tags without `href` attribute are not focusable via Tab key. Users relying on keyboard navigation could not reach these navigation links.
- **Fix:** Added `href="#sources"` and `href="#catalog"` with `event.preventDefault()` in onclick.

### Fixed: F7 — Insights Footer Button Missing Accessible Label
**Severity:** Minor | **Heuristic:** H6 Recognition Rather Than Recall
- **Location:** "+5 more insights today" button
- **Problem:** Button text is terse; screen readers benefit from a more descriptive label.
- **Fix:** Added `aria-label="View 5 more insights from today"`.

---

## Laws of UX Assessment

| Law | Assessment |
|-----|-----------|
| **Hick's Law** | Good. Suggestion chips limited to 5; insight cards limited to 3. Choice overload avoided. |
| **Fitts's Law** | Good. Prompt bar is large (52px height) and centered. Touch targets are 32px+ throughout. |
| **Law of Proximity** | Good. Insights grouped in a 3-column grid; Data Health dimensions are visually grouped. |
| **Law of Common Region** | Good. Cards provide clear visual boundaries. AI note uses left-border accent to group. |
| **Von Restorff Effect** | Good. AI badge reduced to icon-only (sparkle) to avoid competing emphasis. Critical severity badge stands out appropriately. |
| **Jakob's Law** | Good. Search bar pattern, chip/tag patterns, and card layouts match common SaaS conventions. |
| **Peak-End Rule** | Good. Onboarding ends with "Get started" CTA; conversation mode ends with follow-up input. |
| **Cognitive Load** | Good. Single-column layout below the fold reduces extraneous load. Role switching personalizes to reduce irrelevant content. |
| **Zeigarnik Effect** | Good. Data Health score (74/100) with "+8 pts" incentives on next actions creates completion motivation. |
| **Serial Position Effect** | Good. Prompt bar is first element; Popular Queries row at the bottom bookends the content. |

---

## Design System Compliance Summary

| Component | Status | Notes |
|-----------|--------|-------|
| Insight Cards (`.sds-insight`) | PASS | Matches DS reference exactly: icon 36px/8px, padding `--sds-space-500`, gap `--sds-space-400`, severity badges, meta, action links |
| Data Health Card | FIXED | Was inline styles; now uses proper token-based CSS classes |
| Popular Queries | FIXED | Was inline styles; now uses `.popular-chip` class with proper tokens |
| Typography tokens | PASS | All type sizes use `--sds-type-*` tokens |
| Spacing tokens | FIXED | 8 instances of hardcoded px values replaced with `--sds-space-*` tokens |
| Color tokens | PASS | All colors use `--sds-color-*` or semantic aliases |
| Interactive states | FIXED | Added focus-visible to 6 elements that were missing it |
| Responsive layout | PASS | Breakpoints at 1200px, 900px, 600px with proper token adjustments |

---

## Quick Wins (Completed)

1. Replaced inline styles in Data Health card with token-based CSS classes
2. Added focus-visible states to 6 interactive elements
3. Fixed `<a>` tags missing `href` for keyboard accessibility
4. Replaced 8 hardcoded px values with DS spacing tokens
5. Added `aria-label` to insights footer button

---

## Remaining Items for Round 2

### R2-1: Skeleton Loading Uses Hardcoded Inline Styles
**Severity:** Minor | **Location:** Skeleton placeholder (lines ~1617-1663)
The skeleton loading cards use extensive inline `style` attributes with hardcoded `px` values (e.g., `width:40%;height:10px;margin-bottom:12px`). These should be converted to utility classes or CSS rules using DS tokens. Lower priority since skeletons are transient (1.2s visibility).

### R2-2: Response Templates in JS Use Inline Styles
**Severity:** Minor | **Location:** `responseTemplates` object in `<script>` (risk template)
The risk summary response template builds HTML with inline `style` attributes (grid layouts, padding, font sizes). These should use CSS classes for consistency.

### R2-3: Placeholder Pages Use Inline Styles
**Severity:** Minor | **Location:** Security, Sources, Lineage, Governance placeholder screens
Each uses inline `style` for spacing, typography. Should use shared CSS classes or `.empty-state` pattern.

### R2-4: Data Health Card Should Use `.sds-card` Pattern
**Severity:** Minor | **Location:** Data Health card wrapper
Currently uses `.home-card` with custom header/body structure. Could be refactored to use the DS `.sds-card` / `.sds-card-header` / `.sds-card-body` / `.sds-card-footer` pattern from `cards.html` for full DS alignment. The current custom class works but diverges from the canonical card component.

### R2-5: Autocomplete Keyboard Navigation
**Severity:** Minor | **Location:** Autocomplete dropdown
Arrow key navigation through autocomplete suggestions is not implemented. Users can only click suggestions, not navigate with keyboard. Add `aria-activedescendant` pattern.

### R2-6: Home Card Border Radius Inconsistency
**Severity:** Minor | **Location:** `.home-card` uses `border-radius: 12px`
DS `.sds-card` spec uses `border-radius: 8px`. The 12px radius on `.home-card` diverges from the DS card spec. Consider aligning to 8px or documenting the variation.

### R2-7: Tooltip Accessibility
**Severity:** Minor | **Location:** Trust tag tooltips
Tooltips appear on hover only, with no keyboard trigger. Add `aria-describedby` or `role="tooltip"` with focus support for keyboard users.

### R2-8: Color Contrast Audit
**Severity:** Minor
Run WCAG 2.1 AA contrast checks on all text/background combinations, particularly the lighter status backgrounds with their text colors (e.g., warning yellow text on yellow-025 background).

### R2-9: Mobile Touch Target Audit
**Severity:** Minor
At 600px breakpoint, verify all interactive elements meet the 44px minimum touch target size per WCAG 2.5.5. Suggestion chips at small viewports may compress below this threshold.

### R2-10: `prefers-reduced-motion` Coverage
**Severity:** Minor
The global `prefers-reduced-motion` rule disables animations, but some JS-driven animations (e.g., `msgSlideIn` applied via JS) may not be caught. Verify all animation pathways respect the preference.
