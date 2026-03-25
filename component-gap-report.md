# Component Gap Report: Insight Agents Prototype vs. Design System Labs

**Date:** 2026-03-25
**Prototype:** `prototype-final.html` (AI Insight Agents — Strata)
**Design System:** Design System Labs (45 components)

---

## Components Used (matching DS Labs)

| Component | Used In | DS Labs Match | Notes |
|---|---|---|---|
| Avatars | Header (user avatar "JR") | `avatars.html` | Uses initials variant. Follows DS pattern (circle, brand bg, white text). |
| Badges | Sidebar nav count, tab counts, log result badges | `badges.html` | Sidebar badge (`sidenav-badge`) uses custom styling rather than `sds-badge` class. Tab counts follow similar pattern but are custom. Should adopt `sds-badge` variants. |
| Buttons | All screens (primary, secondary, text, danger) | `buttons.html` | Four variants: `btn-primary`, `btn-secondary`, `btn-text`, `btn-danger`. Patterns align well with DS. Class names diverge from DS conventions (`btn-*` vs `sds-btn-*`). |
| Cards | Stat cards, sidebar cards, insight cards, automation cards, find result cards | `cards.html` | Uses `sds-bg-card` token for backgrounds. Cards are custom-built rather than using `sds-card` base class. Border-radius is 12px (DS uses 8px). |
| Empty States | Focus (filtered), Log (filtered), Automate (pending/running) | `empty-states.html` | Pattern matches DS: centered icon circle + heading + description + optional action. Well aligned. |
| Filter Bar | Focus screen, Agent Log screen | `filter-bar.html` | Uses filter chips, separator dividers, sort select, and date range. Structure is similar to DS but implementation is bespoke. |
| Header | App header | `header.html` | Follows DS header pattern: logo, search, actions, profile. Uses `sds-header-*` class namespace correctly. Strong match. |
| Modals | Confirmation dialog (destructive actions) | `modals.html` | Overlay + centered dialog with title, description, action buttons. Standard confirmation modal pattern. Matches DS well. |
| Pagination | Agent Log screen | `pagination.html` | Uses numbered page buttons with prev/next arrows. Follows DS pagination pattern. |
| Progress | Confidence bars, automation running progress, autonomy dial | `progress.html` | Track + fill bar pattern used throughout. Custom implementation but token-aligned. |
| Search | Global header search, Find screen search hero | `search.html` | Header search uses DS pattern (icon + input + kbd shortcut). Find screen has a larger hero variant not in DS. |
| Segmented Control | Settings (autonomy levels), Home sidebar (autonomy dial) | `segmented-control.html` | Three-option segmented control with active state. Matches DS pattern well. Class names differ (`segment` vs DS naming). |
| Selection Controls | Settings (toggles, checkboxes, radio buttons) | `selection-controls.html` | Toggle switches, checkbox rows, and radio groups all present. Custom implementations but follow DS interaction patterns. |
| Side Navigation | Left sidebar | `side-navigation.html` | Uses `sds-sidenav-*` namespace. Sections, items, icons, footer, spacer all present. Strong DS alignment. |
| Skeleton Loaders | Loading states (CSS-only shimmer) | `skeleton-loaders.html` | Shimmer animation on skeleton lines with width variants. Follows DS skeleton pattern. |
| Stat Card | Home screen (4 stat cards in grid) | `stat-card.html` | Label + value + meta pattern. Adds interactive hover/click behavior and link hint not in DS component. |
| Status Indicator | Activity dots (sidebar), timeline status | `status-indicator.html` | Colored dots for success/warning/info states. Follows DS status color tokens. |
| Tabs | Focus screen, Automate screen | `tabs.html` | Underline-style tab bar with active state and count badges. Follows standard DS tab pattern. |
| Tags | Insight cards (priority + category tags) | `tags.html` | Colored tags with icons for critical/warning/info/success. Uses DS status color tokens. Custom class (`insight-tag` vs `sds-tag`). |
| Toasts | Undo toast, save confirmation toast | `toasts.html` | Fixed-bottom toast with message + action button + auto-dismiss timer. Undo variant adds timer bar (extends beyond DS pattern). |
| Tooltips | Confidence scores, settings help icons | `tooltips.html` | Hover-triggered tooltip with arrow. Matches DS tooltip pattern. Adds "help icon" trigger variant. |

---

## Component Gaps (not in DS Labs)

| Component Name | Used In | Description | Closest Existing Component |
|---|---|---|---|
| **Insight Card** | Home, Focus | Complex card with priority border-left color, tags row, title, description, metadata row (blast radius, confidence), action buttons, and expandable reasoning section. The core card for AI agent output. Collapsible variant reduces info-overload for low-priority items. | Insight Cards (DS has a simpler insight card; prototype extends it significantly with reasoning, blast radius, confidence, collapse/expand, and dismissal) |
| **AI Reasoning Disclosure** | Insight Cards | Expandable numbered-step explanation of how the AI agent reached its conclusion. Each step has a number badge + bold label + description. Toggle button shows/hides the reasoning chain. | Accordion (similar expand/collapse, but reasoning has numbered steps with specific formatting) |
| **Confidence Indicator** | Insight Cards, Automation Cards | Horizontal track bar with fill + percentage label. Often paired with a tooltip explaining what the score means. Used as a trust signal for AI-generated suggestions. | Progress (similar track/fill pattern, but confidence is a read-only trust metric, not a progress indicator) |
| **Blast Radius Chain** | Insight Cards | Horizontal chain of impact items connected by arrow icons: "4 models -> 2 dashboards -> ~340 queries". Shows downstream impact of a data issue. | None (completely new pattern for showing cascading data lineage impact) |
| **Autonomy Dial** | Home sidebar, Settings | Three-level control (Manual / Smart / Full auto) that governs agent behavior. Sidebar version is compact with description + link to settings. Settings version is a segmented control per agent type. | Segmented Control (structural match, but the autonomy dial is a domain-specific composite with description, link, and sidebar stats) |
| **Activity Feed** | Home sidebar | Compact list of recent agent actions with colored status dots + description + relative timestamp. Different from notification list in density and purpose. | Timeline (closest match, but the feed is simpler: dot + text + time, no expand) |
| **Automation Card** | Automate screen | Task card showing proposed automation with title, description, metadata (confidence, scope, estimated time), and approve/reject/preview actions. Running variant adds a live progress bar. Completed variant shows timeline format with undo link. | Cards (base structure), but automation cards are a distinct composite with approval workflow, progress states, and undo windows |
| **Batch Action Bar** | Automate screen | Horizontal bar showing count of items + batch approve/reject buttons. Enables bulk operations on pending automations with confirmation dialogs for safety. | None (new pattern for bulk AI-suggested action approval) |
| **Running Indicator** | Automate screen | Pill-shaped inline banner with spinning icon + progress text + navigation arrow. Shows that an automation is actively executing. Clicking navigates to the running tab. | Alerts (similar shape, but running indicator is interactive, transient, and includes animation) |
| **Agent Log Entry** | Agent Log screen | Row with timestamp (mono font), agent type label (color-coded), content, result badge, and expand icon. Expands to show reasoning chain, assets affected, and duration. | Data Lists (row-based, but log entries have unique structure: time + agent type + content + badge + expandable detail) |
| **Notification Entry** | Notifications screen | Row with unread dot, title, description, metadata (agent + time + severity), and inline action buttons. Unread state has left border + blue background highlight. | Notification Center (DS component exists, but prototype adds unread/read visual differentiation, inline action buttons, and mark-as-read) |
| **Find Search Hero** | Find screen | Large centered search input with oversized bar (48px), example prompt chips below, and results area. Conversational AI search pattern. | Search + Command Palette (hybrid of both, but the hero layout with prompt chips is unique) |
| **Find Result Card** | Find screen | Search result with icon + name + type badge, snippet with highlighted matches, trust signals (freshness, popularity, owner), and action links. | Cards (base), but adds search-specific patterns: match highlighting, trust signal row, type badge |
| **Trust Signal Row** | Find result cards | Horizontal row of small icon + label pairs showing data quality indicators (freshness, query count, verified owner). | None (new pattern for communicating AI search result reliability) |
| **Related Questions** | Find screen | Card with list of follow-up questions the user might ask, each with an arrow icon. AI-generated follow-up suggestions. | None (new AI-specific pattern for conversational search continuation) |
| **Settings Save Bar** | Settings screen | Sticky bottom bar that appears when settings are modified. Shows "unsaved changes" message with discard/save buttons. | None (new pattern; could be added as a Banner variant or standalone component) |
| **Undo Toast with Timer** | Global (insight dismiss, actions) | Toast notification with undo button and animated countdown bar showing time remaining to reverse the action. | Toasts (extends DS toast with a visual countdown timer for undo windows) |
| **Date Range Picker (inline)** | Focus, Agent Log | Compact inline date range with calendar icon + two date inputs + "to" separator. Embedded in filter bar. | Date Picker (DS has a date picker, but not an inline range variant designed for filter bars) |

---

## Priority Recommendations for Design System Team

The following gaps represent **genuinely new patterns for AI agent interfaces** and should be prioritized for the design system:

### Tier 1 — Build These First (core AI patterns reused across multiple screens)

1. **Insight Card** — The primary output card for AI agents. Used on Home and Focus screens. Needs variants: full, collapsed, resolved, auto-resolved. Should support priority border, tags, metadata row, actions, and reasoning disclosure.

2. **AI Reasoning Disclosure** — Expandable numbered-step explanation. Critical for AI trust and transparency. Could be built as a sub-component that slots into Insight Cards, Automation Cards, and Log Entries.

3. **Confidence Indicator** — Read-only horizontal bar with percentage label and optional tooltip explanation. Distinct from Progress because it communicates AI certainty, not task completion. Used on every card that shows an AI recommendation.

4. **Blast Radius Chain** — Arrow-connected chain showing downstream impact. Unique to data management but essential for communicating scope of AI-suggested changes.

### Tier 2 — Build Next (automation and workflow patterns)

5. **Automation Card** — Card for proposed AI actions with approval workflow. Needs variants: pending (with approve/reject), running (with progress bar), completed (with undo timer), scheduled.

6. **Batch Action Bar** — Horizontal bar for bulk approval/rejection of AI suggestions. Combines count display with confirmation-protected batch actions.

7. **Running Indicator** — Animated inline pill showing active automation status. Clickable to navigate to details.

8. **Undo Toast with Timer** — Extends existing Toast with visual countdown bar. Critical safety pattern for reversible AI actions.

### Tier 3 — Build When Needed (search and notification patterns)

9. **Find Search Hero** — Large search input with example prompt chips. AI-conversational search entry point.

10. **Trust Signal Row** — Inline data quality indicators for search results. Helps users evaluate AI-surfaced information.

11. **Related Questions** — AI-generated follow-up suggestions for conversational search.

12. **Settings Save Bar** — Sticky unsaved-changes bar. General-purpose pattern, not AI-specific, but currently missing from DS.

13. **Date Range Picker (inline)** — Compact filter-bar variant of the existing Date Picker.
