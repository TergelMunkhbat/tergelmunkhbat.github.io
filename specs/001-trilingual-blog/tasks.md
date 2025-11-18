---

description: "Task list for trilingual blog implementation"
---

# Tasks: Trilingual Blog Section

**Input**: Design documents from `/specs/001-trilingual-blog/`
**Prerequisites**: plan.md, spec.md, research.md, data-model.md, quickstart.md

**Tests**: Manual verification via `jekyll serve` before deployment. No automated tests required for this static site feature.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `- [ ] [ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions

- **Blog Posts**: `_posts/YYYY-MM-DD-slug.LANG.md` (e.g., `_posts/2025-11-18-first-post.en.md`)
- **Layouts**: `_layouts/post.html` (individual post), `_layouts/blog-index.html` (listing page)
- **Includes**: `_includes/language_switcher.html` (cross-language navigation)
- **Assets**: `assets/blog/` (blog-specific images/media)
- **Blog Index**: `blog/index.html` (blog landing page)
- **Navigation**: Update in `index.html` or `_layouts/default.html`

---

## Phase 1: Setup (Project Initialization)

**Purpose**: Initialize blog structure and verify Jekyll environment

- [X] T001 Verify Jekyll installation works by running `jekyll serve` in repository root
- [X] T002 Create `blog/` directory in repository root for blog landing page
- [X] T003 [P] Create `assets/blog/` directory for blog-specific media files
- [X] T004 [P] Create `assets/blog/images/` subdirectory for post images

**Checkpoint**: Directory structure ready for blog implementation

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core layouts and infrastructure that ALL user stories depend on

- [X] T005 [P] Create `_layouts/post.html` base template extending `_layouts/default.html` for individual blog posts - Layout MUST render page.title as an `<h1>` element and page.content within an `<article>` tag. It MUST reuse existing styles from `libs/custom/my_css.css`
- [X] T006 [P] Create `_layouts/blog-index.html` base template extending `_layouts/default.html` for blog listing page - Layout MUST render page.title as an `<h1>` and contain the Liquid loop for listing posts. It MUST reuse existing styles from `libs/custom/my_css.css`
- [X] T007 Update navigation in `index.html` to add "Blog" link pointing to `/blog/` - The main index.html file's navigation section MUST be updated to include a link with the text "Blog"

**Checkpoint**: Foundation ready - user stories can now be implemented independently

---

## Phase 3: User Story 1 - Create New Trilingual Post (Priority: P1) 🎯 MVP

**Goal**: Enable site owner to create and publish blog posts with English, Korean, and Mongolian versions by adding markdown files with YAML front matter

**Independent Test**: 
1. Create test post files: `_posts/2025-11-18-test-post.en.md`, `_posts/2025-11-18-test-post.ko.md`, `_posts/2025-11-18-test-post.mn.md`
2. Each file has proper front matter: `layout: post`, `title`, `date`, `lang`, `post_id: 2025-11-18-test-post`
3. Run `jekyll serve` and verify post appears in `site.posts` collection
4. Navigate to `/2025/11/18/test-post.en.html` and verify post renders correctly

### Implementation for User Story 1

- [X] T008 [P] [US1] Implement post content rendering in `_layouts/post.html` - display `page.title`, `page.date`, and `page.content`
- [X] T009 [P] [US1] Add CSS styling for blog post layout in `libs/custom/my_css.css` - headings, paragraphs, code blocks, lists
- [X] T010 [US1] Create sample post `_posts/2025-11-18-welcome.en.md` with front matter (layout, title, date, lang: en, post_id: 2025-11-18-welcome, excerpt)
- [X] T011 [US1] Create Korean version `_posts/2025-11-18-welcome.ko.md` with identical date and post_id, lang: ko
- [X] T012 [US1] Create Mongolian version `_posts/2025-11-18-welcome.mn.md` with identical date and post_id, lang: mn
- [ ] T013 [US1] Test locally with `jekyll serve` - verify all three versions render with proper markdown formatting (headings, lists, links)
- [ ] T014 [US1] Test markdown features: add image link to `assets/blog/images/sample.jpg`, verify image displays
- [ ] T015 [US1] Test code blocks: add fenced code block with syntax highlighting, verify rendering
- [ ] T016 [US1] Verify `post_id` uniqueness - confirm no duplicate post_id values exist across all posts
- [ ] T017 [US1] Commit and push to master branch - verify GitHub Pages build succeeds within 5 minutes
- [ ] T018 [US1] Verify live site shows all three language versions accessible via their URLs

**User Story 1 Complete** ✅ - Site owner can create trilingual posts

---

## Phase 4: User Story 2 - Browse All Posts (Priority: P2)

**Goal**: Display blog listing page showing all posts in reverse chronological order with title, date, and excerpt using language priority (en → ko → mn)

**Independent Test**:
1. Create 2-3 test posts with different dates (some with all 3 languages, some with only 2)
2. Navigate to `/blog/` on local Jekyll server
3. Verify posts appear newest first
4. Verify each post shows correct language version (English if available, else Korean, else Mongolian)
5. Verify title, date, and excerpt display for each post

### Implementation for User Story 2

- [X] T019 [P] [US2] Implement blog index logic in `_layouts/blog-index.html` - group posts by post_id using Liquid
- [X] T020 [P] [US2] Implement language priority fallback in `_layouts/blog-index.html` - filter for en first, then ko, then mn
- [X] T021 [US2] Display post metadata in blog index: `display_post.title`, `display_post.date | date: "%B %d, %Y"`, `display_post.excerpt`
- [X] T022 [US2] Add "Read more" link for each post pointing to `display_post.url`
- [X] T023 [US2] Create `blog/index.html` using layout `blog-index` with page title "Blog"
- [X] T024 [US2] Add CSS styling for blog index in `libs/custom/my_css.css` - post list layout, excerpt styling, date formatting
- [ ] T025 [US2] Test reverse chronological sorting - create posts with dates: 2025-11-20, 2025-11-18, 2025-11-15, verify order
- [ ] T026 [US2] Test language priority - create post with only Korean version, verify it displays on index
- [ ] T027 [US2] Test excerpt display - verify custom excerpt from front matter displays correctly, test auto-generated excerpt
- [ ] T028 [US2] Test locally with `jekyll serve` - verify blog index loads in under 2 seconds
- [ ] T029 [US2] Verify "Read more" links navigate to correct post pages
- [ ] T030 [US2] Commit and push to master branch - verify blog index appears on live site

**User Story 2 Complete** ✅ - Visitors can browse all posts in reverse chronological order

---

## Phase 5: User Story 3 - Read and Switch Languages (Priority: P3)

**Goal**: Provide language switcher UI on each blog post showing available translations, allowing visitors to switch between language versions with one click

**Independent Test**:
1. Open blog post in English (e.g., `/2025/11/18/welcome.en.html`)
2. Verify language switcher shows "Korean" and "Mongolian" links
3. Click "Korean" link - verify Korean version loads at top of article
4. Verify language switcher on Korean page shows "English" and "Mongolian" links
5. Test post with missing Mongolian version - verify only English and Korean links appear

### Implementation for User Story 3

- [X] T031 [P] [US3] Create `_includes/language_switcher.html` - filter `site.posts` where post_id matches `page.post_id`
- [X] T032 [P] [US3] Implement dynamic language detection in `_includes/language_switcher.html` - check which lang versions exist
- [X] T033 [US3] Render language links in switcher - display "English", "한국어", "Монгол" with links to `translation.url`
- [X] T034 [US3] Add active language indicator in switcher - highlight current `page.lang` in different style
- [X] T035 [US3] Include language switcher in `_layouts/post.html` - add `{% include language_switcher.html %}` below post title
- [X] T036 [US3] Add CSS styling for language switcher in `libs/custom/my_css.css` - horizontal layout, hover states, active indicator
- [ ] T037 [US3] Test language switching - click Korean link on English post, verify correct Korean version loads
- [ ] T038 [US3] Test with missing translation - create post with only EN and KO versions, verify MN link doesn't appear
- [ ] T039 [US3] Test with single language - create post with only MN version, verify switcher shows only MN (active)
- [ ] T040 [US3] Verify page loads at top after language switch (default browser behavior, no scroll position maintenance)
- [ ] T041 [US3] Test locally with `jekyll serve` - verify all language combinations work correctly
- [ ] T042 [US3] Commit and push to master branch - verify language switcher works on live site

**User Story 3 Complete** ✅ - Visitors can switch between language versions of posts

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final refinements, documentation, and validation

- [ ] T043 [P] Add documentation to quickstart.md about creating new posts (already completed in planning phase)
- [ ] T044 [P] Validate `post_id` uniqueness manually - review all posts in `_posts/` directory
- [ ] T045 Test all markdown formatting - verify headings (h1-h6), bold, italic, links, images, code blocks, lists (ordered/unordered)
- [ ] T046 Test responsive design - verify blog index and posts render correctly on mobile, tablet, desktop viewports
- [ ] T047 Verify Font Awesome icons display correctly if used in blog posts
- [ ] T048 Test navigation flow - index → blog index → post → language switch → back to blog index
- [ ] T049 Validate HTML markup - check for broken links, missing alt text on images
- [ ] T050 Performance check - verify blog index loads in <2s, individual posts load in <2s, language switch <2s
- [ ] T051 [P] Update README.md with blog section documentation and link to quickstart.md
- [ ] T052 Final visual review - verify consistent styling with existing site (Skeleton CSS framework)
- [ ] T053 Merge feature branch `001-trilingual-blog` into `master` via pull request
- [ ] T054 Verify production deployment - check live site at GitHub Pages URL

**All Phases Complete** ✅ - Trilingual blog feature fully implemented

---

## Dependencies & Execution Strategy

### User Story Dependency Graph

```
Phase 1 (Setup) → Phase 2 (Foundational) → {
  Phase 3 (US1: Create Post),
  Phase 4 (US2: Browse Posts),
  Phase 5 (US3: Language Switch)
} → Phase 6 (Polish)
```

**Key Dependencies**:
- Phase 2 MUST complete before any user story phases (blocking)
- US1, US2, US3 are **independent** - can be implemented in parallel after Phase 2
- Phase 6 requires all user stories complete

### Parallelization Opportunities

**Within User Story 1** (after foundational layouts exist):
- T008, T009 can run in parallel (different files: post.html, my_css.css)
- T010, T011, T012 can run in parallel (different post files: .en.md, .ko.md, .mn.md)

**Within User Story 2**:
- T019, T020 are sequential (same file: blog-index.html)
- T024 can run in parallel with T019-T023 (different file: my_css.css)

**Within User Story 3**:
- T031, T032, T033, T034 are sequential (same file: language_switcher.html)
- T036 can run in parallel with T031-T035 (different file: my_css.css)

**Across User Stories** (after Phase 2 complete):
- US1 (T008-T018), US2 (T019-T030), US3 (T031-T042) can run **fully in parallel**
- Each delivers independent, testable functionality

### Implementation Strategy

**Recommended Approach - MVP First**:
1. **Sprint 1**: Phase 1 (Setup) + Phase 2 (Foundational)
2. **Sprint 2**: Phase 3 (US1 - Create Post) ← **MVP COMPLETE** at this point
3. **Sprint 3**: Phase 4 (US2 - Browse Posts)
4. **Sprint 4**: Phase 5 (US3 - Language Switch)
5. **Sprint 5**: Phase 6 (Polish)

**Alternative - Parallel Development** (if multiple developers):
- Team A: Phase 1-2 (Setup/Foundation)
- After Phase 2 complete:
  - Team A: Phase 3 (US1) + Phase 5 (US3)
  - Team B: Phase 4 (US2)
- Both teams: Phase 6 (Polish)

**MVP Scope** (minimum viable product):
- Phase 1 (Setup): T001-T004
- Phase 2 (Foundational): T005-T007
- **Phase 3 (US1)**: T008-T018 ← Delivers core value: ability to create and view posts

**Incremental Delivery**:
- After US1: Site owner can publish posts, visitors can view individual posts
- After US2: Visitors can discover posts via blog index
- After US3: Visitors can switch languages seamlessly
- After Phase 6: Production-ready, polished feature

---

## Task Summary

**Total Tasks**: 54

### By Phase
- **Phase 1 (Setup)**: 4 tasks (T001-T004)
- **Phase 2 (Foundational)**: 3 tasks (T005-T007)
- **Phase 3 (US1 - Create Post)**: 11 tasks (T008-T018)
- **Phase 4 (US2 - Browse Posts)**: 12 tasks (T019-T030)
- **Phase 5 (US3 - Language Switch)**: 12 tasks (T031-T042)
- **Phase 6 (Polish)**: 12 tasks (T043-T054)

### By User Story
- **User Story 1 (P1)**: 11 tasks
- **User Story 2 (P2)**: 12 tasks
- **User Story 3 (P3)**: 12 tasks
- **Infrastructure (Setup + Foundational + Polish)**: 19 tasks

### Parallelization
- **[P] Parallel Tasks**: 15 tasks can run in parallel with other tasks
- **Sequential Tasks**: 39 tasks have dependencies

### Independent Test Criteria
- **US1**: Create 3 markdown files, verify all render with proper formatting ✅
- **US2**: Navigate to `/blog/`, verify reverse chronological listing with correct language priority ✅
- **US3**: Click language switcher, verify translation loads correctly ✅

### Format Validation
✅ All 54 tasks follow checklist format: `- [ ] [TaskID] [P?] [Story?] Description with file path`

**MVP Delivery**: Phase 1-3 (18 tasks) delivers minimum viable blog functionality

---

## Notes

**Tests**: No automated tests required - manual verification via `jekyll serve` is appropriate for this static site feature.

**GitHub Pages Compatibility**: ✅ All tasks use standard Jekyll features (no custom plugins).

**Constitution Compliance**: ✅ Feature uses existing Jekyll framework, content-driven via markdown files, automatic deployment.

**Manual Validation Required**:
- `post_id` uniqueness (T016, T044) - GitHub Pages cannot enforce at build time
- Markdown formatting (T045) - Visual verification needed
- Performance (T050) - Manual testing with browser dev tools
