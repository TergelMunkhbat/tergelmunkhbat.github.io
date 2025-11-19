# Tasks: Navigation & Layout Redesign

**Input**: Design documents from `/specs/002-nav-layout-redesign/`
**Prerequisites**: plan.md ✅, spec.md ✅, research.md ✅, data-model.md ✅, contracts/ ✅

**Tests**: For this static website, testing is manual verification via `jekyll serve` before deployment. No automated tests needed for this UI-focused feature.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Static Website**: `_data/` (content), `_layouts/` (templates), `_includes/` (partials), `assets/` (media), `libs/` (CSS/JS)
- Content updates primarily affect YAML files in `_data/`
- Layout changes affect HTML in `_layouts/` and `_includes/`
- Style changes affect CSS in `libs/custom/my_css.css`
- Deployment via GitHub Pages (automatic on push to master)

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Verify environment and prepare for implementation

- [x] T001 Verify Jekyll installation and start local preview with `jekyll serve --livereload`
- [x] T002 [P] Verify current site structure matches plan.md (check `_layouts/`, `_includes/`, `_data/main_info.yaml` exist)
- [x] T003 [P] Review existing homepage header to understand current bio section structure for extraction
- [x] T003b [P] Measure current hero header height in pixels using browser DevTools - Record baseline for SC-002 verification (target: 70% reduction, e.g., 350px → <100px)

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Create reusable components needed by multiple user stories

**⚠️ CRITICAL**: These components are shared across US1, US2, and US3. Must complete before user story implementation.

**Architecture Decision**: All conditional logic for headers/footers will be centralized in `_layouts/default.html` using `page.layout` checks. This provides a single source of truth and simplifies maintenance.

**Conditional Logic Pattern**:
```liquid
<!-- Header: Navbar for posts, Hero for others -->
{% if page.layout == 'post' %}
  {% include slim-navbar.html %}
{% else %}
  {% include bio-section.html variant="hero" %}
{% endif %}

<!-- Footer: Bio only for posts -->
{% if page.layout == 'post' %}
  <footer class="article-footer">
    {% include bio-section.html variant="footer" %}
  </footer>
{% endif %}
```

- [x] T004 [P] Create `_includes/bio-section.html` - Extract current hero header bio HTML into reusable component with variant parameter support (variants: "hero", "footer")
- [x] T005 [P] Create `_includes/slim-navbar.html` - Implement basic navbar structure with name/logo (left) and placeholder for menu links (right), max-height: 100px
- [x] T006 Add base CSS for bio-section variants in `libs/custom/my_css.css` - Define `.bio-section`, `.bio-hero`, `.bio-footer` classes
- [x] T007 Add base CSS for slim-navbar in `libs/custom/my_css.css` - Define `.slim-navbar` with sticky positioning and max-height constraint

**Checkpoint**: Foundation ready - reusable components created, user story implementation can now begin

---

## Phase 3: User Story 1 - Article Reader Focused Reading Experience (Priority: P1) 🎯 MVP

**Goal**: Replace large bio header with slim navbar on article pages, relocate bio to footer for improved reading focus

**Independent Test**: View any blog article page locally with `jekyll serve`, verify slim navbar at top (not hero header), bio section at bottom of article content. Verify navbar stays visible when scrolling.

### Implementation for User Story 1

- [x] T008 [P] [US1] Review current layout structure - Check `_layouts/default.html` (base template) and `_layouts/post.html` (article template) to understand inheritance
- [x] T009 [US1] Add conditional header logic to `_layouts/default.html` - Replace existing header with: `{% if page.layout == 'post' %}{% include slim-navbar.html %}{% else %}{% include bio-section.html variant="hero" %}{% endif %}`
- [x] T010 [US1] Add conditional footer logic to `_layouts/default.html` - After main content area, add: `{% if page.layout == 'post' %}<footer class="article-footer">{% include bio-section.html variant="footer" %}</footer>{% endif %}`
- [x] T011 [US1] Add sticky positioning CSS for slim-navbar in `libs/custom/my_css.css` - Set `position: -webkit-sticky; position: sticky; top: 0; z-index: 1000;`
- [x] T012 [US1] Style bio-footer variant in `libs/custom/my_css.css` - Compact horizontal layout, smaller profile pic, inline social icons
- [x] T013 [US1] Manual test: View article page with `jekyll serve`, verify slim navbar appears at top instead of hero header
- [x] T014 [US1] Manual test: Scroll down article page, verify navbar remains visible (sticky)
- [x] T015 [US1] Manual test: Scroll to bottom of article, verify bio section appears with photo, name, title, social icons

**Checkpoint**: User Story 1 complete - Article pages have slim navbar and footer bio. Independently testable.

---

## Phase 4: User Story 2 - Homepage Visual Impact Preservation (Priority: P2)

**Goal**: Ensure homepage continues to display large hero header (no slim navbar), maintaining visual impact

**Independent Test**: View homepage locally with `jekyll serve`, verify large hero header with photo, name, title, social icons displays as before. Verify no slim navbar appears.

### Implementation for User Story 2

- [x] T016 [P] [US2] Verify homepage layout value - Check `index.html` front matter to confirm it uses `layout: default` (NOT `layout: post`)
- [x] T017 [US2] Verify conditional logic in `_layouts/default.html` - Confirm the `{% else %}` branch includes bio-section.html with variant="hero" for non-post layouts
- [x] T018 [US2] Style bio-hero variant in `libs/custom/my_css.css` - Large centered layout, large profile pic, centered social icons (preserve existing styling)
- [x] T019 [US2] Manual test: View homepage with `jekyll serve`, verify large hero header displays (NOT slim navbar)
- [x] T020 [US2] Manual test: Scroll down homepage, verify NO bio footer appears (only hero header at top)
- [x] T021 [US2] Manual test: Compare homepage to previous version, verify visual appearance unchanged

**Checkpoint**: User Story 2 complete - Homepage preserves hero header. Both US1 and US2 independently testable.

---

## Phase 5: User Story 3 - Easy Site Navigation (Priority: P3)

**Goal**: Add functional navigation menu (Home, Blog) to slim navbar with working links

**Independent Test**: View article page with `jekyll serve`, click "Home" link and verify navigation to homepage, click "Blog" link and verify navigation to `/blog/index.html`, click name/logo and verify navigation to homepage.

### Implementation for User Story 3

- [x] T022 [P] [US3] Create blog index page at `blog/index.html` - Use Jekyll's site.posts loop to list all blog posts with title, date, excerpt. Handle empty posts gracefully with message like "No posts yet"
- [x] T023 [US3] Update `_includes/slim-navbar.html` - Add navigation menu with "Home" link (href="/") and "Blog" link (href="/blog/index.html")
- [x] T024 [US3] Make name/logo clickable in `_includes/slim-navbar.html` - Wrap name in `<a href="/">` link
- [x] T025 [US3] Style navigation menu in `libs/custom/my_css.css` - Horizontal flexbox layout, right-aligned menu links
- [x] T026 [US3] Style blog index page in `libs/custom/my_css.css` - Format post list with titles, dates, excerpts
- [x] T027 [US3] Manual test: View article page, click "Home" menu link, verify navigation to homepage
- [x] T028 [US3] Manual test: View article page, click "Blog" menu link, verify navigation to `/blog/index.html`
- [x] T029 [US3] Manual test: View article page, click name/logo, verify navigation to homepage
- [x] T030 [US3] Manual test: View blog index page, verify all posts listed with clickable links

**Checkpoint**: User Story 3 complete - Navigation menu functional. All three user stories independently testable.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Mobile responsiveness, accessibility, and final refinements

### Mobile Responsive Design (Addresses Edge Cases from spec.md)

- [x] T031 [P] Implement hamburger menu in `_includes/slim-navbar.html` - Add checkbox hack: hidden checkbox (`id="nav-toggle"`), label with hamburger icon (`☰`), nav menu
- [x] T032 [P] Add mobile-responsive CSS in `libs/custom/my_css.css` - Media query @media (max-width: 767px): hide menu, show hamburger, toggle menu on checkbox :checked
- [x] T033 [P] Add desktop CSS in `libs/custom/my_css.css` - Media query @media (min-width: 768px): show menu, hide hamburger
- [x] T034 Add ARIA labels for accessibility in `_includes/slim-navbar.html` - Add aria-label to checkbox and hamburger label
- [x] T035 Manual test: Resize browser to mobile width (<768px), verify hamburger icon appears and menu links hidden
- [x] T036 Manual test: Click hamburger icon, verify menu expands to show Home and Blog links
- [x] T037 Manual test: Verify hamburger menu works without JavaScript (checkbox hack is CSS-only)

### Cross-Browser and Accessibility Testing

- [x] T038 Manual test: Test in Chrome, Firefox, Safari - verify navbar sticky positioning works (including -webkit-sticky prefix)
- [x] T039 Manual test: Disable JavaScript in browser, verify navbar stays sticky and navigation links work
- [x] T040 Manual test: Use keyboard navigation (Tab key) to navigate through navbar links, verify all focusable
- [x] T041 Manual test: Verify navbar remains readable with browser zoom at 150% and 200%

### Final Validation and Deployment

- [x] T042 Review all functional requirements (FR-001 through FR-012 from spec.md) and verify each is met
- [x] T043 Validate all success criteria (SC-001 through SC-006 from spec.md) - measure navbar height, verify 70% reduction, test scrolling
- [x] T044 Run `jekyll serve` and perform full site smoke test - homepage, multiple article pages, blog index, mobile view
- [x] T045 Commit all changes with descriptive message: "feat(nav): implement context-aware headers with responsive navbar"
- [ ] T046 Push to feature branch (`002-nav-layout-redesign`) and verify GitHub Pages preview build succeeds (if applicable)
- [ ] T047 Create pull request or merge to master branch for production deployment
- [ ] T048 Verify production deployment on live site - test homepage, articles, blog index, mobile responsiveness

**Final Checkpoint**: All user stories complete, mobile responsive, accessibility verified, ready for production.

---

## Dependencies & Execution Order

### User Story Completion Order

```
Setup (Phase 1) → Foundational (Phase 2) → User Stories (Parallel) → Polish (Phase 6)
                                              ↓
                                    ┌─────────┼─────────┐
                                    ↓         ↓         ↓
                                  US1       US2       US3
                              (Article)  (Homepage) (Navigation)
                                (P1)       (P2)        (P3)
```

**Independence**: User Stories 1, 2, and 3 can be implemented in parallel after Foundational phase completes. They are independent and each is testable on its own.

**Recommended MVP**: Implement just **User Story 1 (P1)** for minimal viable product - article pages get slim navbar and footer bio. This delivers core readability improvement.

### Parallel Execution Opportunities

#### Phase 2 (Foundational) - Parallel Tasks
```bash
# These can run simultaneously (different files):
T004: Create _includes/bio-section.html
T005: Create _includes/slim-navbar.html
T006: Add bio-section CSS (once T004 complete, but can start CSS structure)
T007: Add slim-navbar CSS (once T005 complete, but can start CSS structure)
```

#### User Stories - Fully Parallel
```bash
# After Phase 2 complete, all user stories can be implemented in parallel:
US1 (T008-T015): Article pages
US2 (T016-T021): Homepage
US3 (T022-T030): Navigation menu + blog index
```

#### Phase 6 (Polish) - Parallel Tasks
```bash
# Mobile CSS and accessibility can be done in parallel:
T031: Add hamburger HTML
T032: Add mobile CSS
T033: Add desktop CSS
T034: Add ARIA labels
```

## Implementation Strategy

### Architecture: Centralized Conditional Logic

**Key Decision**: All header/footer conditional logic is centralized in `_layouts/default.html` rather than scattered across individual page layouts. This provides:

✅ **Single Source of Truth**: One place to modify header/footer logic
✅ **DRY Principle**: No duplication across `post.html`, `index.html`, etc.
✅ **Easier Maintenance**: Changes to conditional logic only require editing `default.html`
✅ **Clearer Logic**: `{% if page.layout == 'post' %}` is self-documenting

**Pattern**:
- Posts (`layout: post`) → Slim navbar + Bio footer
- Homepage (`layout: default`) → Hero header only
- Other pages → Inherit default behavior (Hero header)

This simplifies tasks T009, T017, and T018 - instead of modifying multiple layouts, we modify `default.html` once.

### MVP First Approach (Recommended)

**Phase 1 MVP** (Minimal viable product - ~6 tasks):
1. Complete Setup (T001-T003b) - includes baseline measurement
2. Create bio-section component only (T004, T006)
3. Create slim-navbar component only (T005, T007)
4. Implement US1 only (T008-T015) - Article pages with navbar
5. Test article pages (T013-T015)

**Result**: Article pages have improved readability with slim navbar. Homepage unchanged. Delivers core value.

**Phase 2 Expansion** (Add remaining stories):
- US2 (T016-T021) - Ensure homepage still works
- US3 (T022-T030) - Add navigation menu

**Phase 3 Polish**:
- Mobile responsiveness (T031-T037)
- Cross-browser testing (T038-T041)
- Final validation (T042-T048)

### Full Feature Approach

Complete all phases in order: Setup → Foundational → US1 → US2 → US3 → Polish

**Estimated effort**: ~49 tasks total, but many are quick manual tests. Core implementation is ~20-25 tasks.

---

## Task Summary

**Total Tasks**: 49
- Setup: 4 tasks
- Foundational: 4 tasks
- User Story 1 (P1): 8 tasks
- User Story 2 (P2): 6 tasks
- User Story 3 (P3): 9 tasks
- Polish: 18 tasks

**Parallelizable Tasks**: 15 tasks marked with [P]

**Independent Test Criteria**:
- **US1**: Article pages show slim navbar (not hero header) + bio footer. Navbar is sticky.
- **US2**: Homepage shows hero header (not navbar). Visual appearance unchanged.
- **US3**: Navigation links work (Home, Blog, logo). Blog index page lists posts.

**Suggested MVP Scope**: User Story 1 only (T001-T015) - ~16 tasks for core readability improvement (includes baseline measurement).

**Format Validation**: ✅ All tasks follow checklist format with checkbox, Task ID, [P] marker (where applicable), [Story] label (for user story tasks), and file paths.
