# Design QA — Book switcher and shared book ordering

## Comparison target

- Source visual truth: `/Users/bytedance/.codex/generated_images/019f7435-1a61-70d2-8436-af36de78bcaa/exec-5ff1a702-4279-4cba-9bd6-568c79b24e2d.png`
- Browser-rendered implementation: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/19-final-clean-desktop.jpg`
- Local preview: `http://localhost:5173/`
- Viewport: 1280 × 720 desktop; additional 390 × 844 mobile check
- State: dark theme, Writing mode, “示例书” active, book-switch menu open, shared sort mode set to “recent”
- Full-view comparison: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/20-source-vs-final-clean.jpg`
- Focused trigger/popover comparison: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/21-trigger-popover-final-clean.jpg`
- Supporting states:
  - Book Management with recent-sort control: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/12-left-aligned-recent.jpg`
  - Mobile menu: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/15-final-mobile-left.jpg`
  - Light theme: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/16-final-light-left.jpg`

## Findings

- No actionable P0, P1, or P2 findings remain.
- The final trigger is intentionally left-aligned beside the Writing/Game mode switch instead of centered as in the original concept. This is the user's explicit follow-up direction and improves the grouping of interactive controls.
- The implementation contains more books than the concept and uses the user's real covers and recent-open timestamps. The list therefore scrolls while the “Manage bookshelf” action remains fixed; this is expected dynamic-data behavior rather than fidelity drift.

## Required fidelity surfaces

- Fonts and typography: the implementation reuses Denova's existing UI font stack and compact type scale. Trigger and metadata weights preserve the source hierarchy; long titles truncate without changing control height.
- Spacing and layout rhythm: the desktop trigger is 28 px high, matching the adjacent mode switch, and the mobile trigger is 32 px high, matching the mobile mode switch. The 352 px popover width, item rhythm, radii, dividers, and elevation closely follow the reference. Long lists scroll inside the list region without hiding the footer.
- Colors and visual tokens: all surfaces use existing Nova background, border, active, hover, text, focus, and shadow tokens. Dark and light states retain readable contrast and consistent selection treatment.
- Image quality and asset fidelity: book covers come from the real book-cover endpoint with a fixed 3:4 crop and object-fit treatment. Missing covers use the existing Lucide book fallback; no CSS art, emoji, or handcrafted SVG substitutes were introduced.
- Copy and content: “Switch book”, current-chapter metadata, recent-open metadata, switch failure, empty state, “Manage bookshelf”, and the “Recently opened / Manual order” control are available in both Chinese and English.
- Icons and affordances: BookOpen, Check, ChevronDown, LibraryBig, ArrowDownUp, and drag handles use the existing Lucide icon family and align to the current design system.
- Accessibility: the trigger and sort selector have explicit accessible names; the current book exposes `aria-current`; decorative covers do not duplicate menu-item names; Radix supplies keyboard menu/select behavior and focus management.

## Primary interactions tested

- Opened the switcher by mouse and keyboard; ArrowDown focus navigation reaches menu items.
- Switched from “示例书” to `project101`; the Writing mode remained selected and `project101` became the first recently opened book.
- Switched back to “示例书”; the original workspace and Writing mode were restored.
- Changed Book Management from “Recently opened” to “Manual order”; ten drag handles appeared and the API persisted `manual`.
- Restored “Recently opened”; drag handles disappeared, the API returned `recent`, and “示例书” remained first.
- Opened “Manage bookshelf” from the fixed menu footer.
- Checked desktop, 390 px mobile, long-list scrolling, real covers, dark theme, and light theme.
- Fresh-page browser console check after the backend restart and final interaction: 0 errors, 0 warnings.

## Comparison history

1. P2 — The first desktop pass let a long real-world book list push the fixed bookshelf action below the viewport.
   - Fix: constrained the list group to a viewport-aware maximum height and made only that group scroll.
   - Post-fix evidence: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/02-desktop-open-footer.jpg`
2. P2 — The first mobile pass truncated the current title because a redundant BookOpen icon consumed the narrow trigger width.
   - Fix: removed the redundant icon only in the compact trigger while retaining the chevron and accessible label.
   - Post-fix evidence: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/10-mobile-final-open.jpg`
3. P2 — Follow-up product review found the centered trigger separated an interactive control from the mode controls and its height did not align.
   - Fix: moved the switcher beside the mode switch, changed the desktop height to 28 px and mobile height to 32 px, aligned surface/border treatment, and anchored the popover to the trigger's left edge.
   - Post-fix evidence: `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/19-final-clean-desktop.jpg`
4. P2 — The original component pinned the current book ahead of the shared order, so a manual bookshelf order could not be represented faithfully in the quick switcher.
   - Fix: introduced one backend-persisted sort mode and order for both surfaces, defaulted new data to recent-first, preserved legacy manual order, and stopped independently pinning the current book.
   - Post-fix evidence: Book Management and quick-switch ordering verified together in `/Users/bytedance/.codex/visualizations/2026/07/18/019f7435-1a61-70d2-8436-af36de78bcaa/book-switch-implementation/12-left-aligned-recent.jpg`; backend and component tests cover manual-order persistence.

## Implementation checklist

- [x] Shared recent/manual sort mode persisted in `books.json`
- [x] Default recent-first order with deterministic fallback
- [x] Manual drag order preserved across mode changes and book opens
- [x] Quick switcher consumes the same backend order as Book Management
- [x] Left-aligned, mode-sized trigger on desktop and mobile
- [x] Fixed footer and scrollable long list
- [x] Chinese and English UI copy
- [x] Full Go tests, full frontend tests, i18n validation, and production build

## Follow-up polish

- No P3 follow-up is required for this scope.

final result: passed
