# Implementation Plan: Navigation & Layout Redesign

**Branch**: `002-nav-layout-redesign` | **Date**: 2025-11-19 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/002-nav-layout-redesign/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Implement context-aware headers for the personal website: maintain the large hero header on the homepage for visual impact, while introducing a slim sticky navbar (max 100px height) on article pages to improve reading focus. The bio section will be relocated from the article header to the footer, and a responsive navigation menu (Home, Blog) will be added. On mobile devices, menu links will collapse into a hamburger menu. All changes will use existing HTML/CSS/JavaScript structure without adding new frameworks.

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: HTML5, CSS3, JavaScript ES5+ (jQuery 3.1.1)
**Primary Dependencies**: Jekyll (local preview only), Skeleton CSS, Font Awesome, Academicons
**Storage**: YAML files in `_data/` directory, static assets in `assets/`
**Testing**: Manual testing via `jekyll serve`, visual verification
**Target Platform**: Static web hosting (GitHub Pages)
**Project Type**: Static website (Jekyll-based)
**Performance Goals**: Fast page load (<2s), minimal JavaScript
**Constraints**: Fast page load (<2s), minimal JavaScript, no build process, automatic GitHub Pages deployment, maintain existing structure
**Scale/Scope**: Single-user personal website, ~5-10 pages/sections

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity First**:
- [x] Change uses existing static HTML/CSS/JS structure (no new frameworks/build tools)
- [x] Dependencies are vendored locally (no new CDN dependencies)
- [x] Change is immediately viewable without compilation
- [x] GitHub Pages compatibility maintained (supported Jekyll plugins only)

**Content-Driven Updates**:
- [ ] If content change: Updates only `_data/` YAML files or assets (CV, images)
- [x] If layout change: Justification provided for modifying `_layouts/`, `_includes/`, or `libs/`

**Layout Change Justification**: This feature modifies `_layouts/` and `_includes/` to implement context-aware headers (homepage vs article pages) and introduces a slim navbar component. This is a presentation layer change that improves article readability (core user value) while maintaining all existing dependencies and deployment processes. The change aligns with Simplicity First by using only existing CSS/JS capabilities (no new frameworks), and all modifications are immediately testable with `jekyll serve`.

## Project Structure

### Documentation (this feature)

```text
specs/[###-feature]/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

```text
# Static Website Structure (Jekyll-based)
_data/
├── experience.yaml      # Work/education history
├── main_info.yaml       # Personal info, links
├── projects.yaml        # Project listings
└── publications.yaml    # Academic publications

_layouts/
├── default.html         # Main page template
└── project.html         # Individual project pages

_includes/
├── google_analytics.html
└── image_with_caption.html

assets/
├── cv/                  # PDF resume
├── logos/               # Project/company logos
└── profile-pics/        # Profile images

libs/
├── custom/
│   ├── my_css.css       # Custom styling
│   └── my_js.js         # Custom scripts
└── external/            # Vendored libraries
    ├── jquery-3.1.1.min.js
    ├── skeleton/
    ├── font-awesome-4.7.0/
    └── academicons-1.8.6/

index.html               # Main page
README.md                # Project documentation
```

**Structure Decision**: This is a static Jekyll website. Content updates occur in `_data/` YAML files. Layout changes occur in `_layouts/` and `_includes/`. Styling changes occur in `libs/custom/`. All external dependencies are vendored in `libs/external/`.

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

**No violations** - All constitution checks passed. This feature uses existing HTML/CSS/JS structure, introduces no new dependencies, and works within Jekyll's static site generation capabilities.

## Phase 0: Research (Completed)

**Output**: `research.md`

**Key Decisions**:
- Use Jekyll Liquid templating for context-aware header rendering (server-side, no JS needed)
- Use CSS `position: sticky` for navbar with `-webkit-sticky` fallback
- Implement mobile hamburger menu using checkbox hack (works without JS)
- Create `/blog/index.html` using Jekyll's `site.posts` iteration
- Detect page type via `page.layout` field (post vs default)
- Use Skeleton CSS's existing 768px breakpoint for responsive behavior
- Create reusable `_includes/bio-section.html` with variant parameter
- Enforce max 100px navbar height using CSS `max-height` property

**No new dependencies required** - All functionality achievable with existing tech stack.

## Phase 1: Design & Contracts (Completed)

**Output**: `data-model.md`, `contracts/component-interfaces.md`, `quickstart.md`

**Data Model Summary**:
- No new data storage required
- Reuses existing `_data/main_info.yaml` for bio information
- Uses Jekyll's built-in `site.posts` for blog index
- State managed via CSS (mobile menu toggle) and server-side rendering (page type detection)

**Component Contracts**:
- `bio-section.html` - Reusable bio component with hero/footer variants
- `slim-navbar.html` - Sticky navigation bar with responsive hamburger menu
- `blog/index.html` - Blog listing page using `site.posts` iteration
- Conditional header logic in layouts based on `page.layout`

**Quickstart Guide**:
- Development workflow with `jekyll serve --livereload`
- Implementation order (4 phases: components, blog index, headers, responsive)
- Testing checklist (homepage, articles, blog index, mobile, JS-disabled)
- Git workflow and commit strategy

## Constitution Check Re-evaluation (Post-Design)

**Simplicity First**:
- [x] No new frameworks or build tools introduced ✅
- [x] No new dependencies (all use existing Skeleton CSS, jQuery, Jekyll) ✅
- [x] Changes immediately viewable with `jekyll serve` ✅
- [x] GitHub Pages compatible (standard Jekyll features only) ✅

**Content-Driven Updates**:
- [x] Layout changes justified (improves article readability, core user value) ✅
- [x] Modifies `_layouts/` and `_includes/` only (no data structure changes) ✅
- [x] Existing `_data/main_info.yaml` structure unchanged ✅

**Design Validation**: All design decisions align with constitution. No complexity violations introduced.
