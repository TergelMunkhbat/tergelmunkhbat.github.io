# Implementation Plan: Trilingual Blog Section

**Branch**: `001-trilingual-blog` | **Date**: 2025-11-18 | **Spec**: [spec.md](spec.md)
**Input**: Feature specification from `/specs/001-trilingual-blog/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Add a trilingual blog section to the personal website supporting English, Korean, and Mongolian versions of each post. Site owners create posts by adding markdown files with YAML front matter containing `post_id` (shared identifier) and `lang` (en/ko/mn). Jekyll automatically processes these files, generates a blog index page in reverse chronological order (prioritizing English → Korean → Mongolian), and provides language switcher UI to toggle between translations. Blog posts must validate `post_id` uniqueness at build time and fail with clear errors on duplicates. Technical approach uses Jekyll collections with custom Liquid logic for cross-referencing posts by `post_id`.

## Technical Context

**Language/Version**: HTML5, CSS3, JavaScript ES5+ (jQuery 3.1.1), Liquid templating (Jekyll)
**Primary Dependencies**: Jekyll 3.9+ (GitHub Pages compatible), Skeleton CSS, Font Awesome, Academicons, jQuery 3.1.1
**Storage**: Markdown files in `_posts/` collection, static assets in `assets/blog/`
**Testing**: Manual testing via `jekyll serve`, visual verification of language switching and post rendering
**Target Platform**: GitHub Pages (automatic Jekyll build on push to master)
**Project Type**: Static website (Jekyll-based) with blog collection
**Performance Goals**: Fast page load (Blog index load <2s on a 4G mobile connection), individual post load <2s, language switch <2s
**Constraints**: 
- Must use GitHub Pages-compatible Jekyll (no custom plugins)
- ASCII-only filenames (localized titles in front matter)
- Same `date` across language versions of a post
- No manual build artifacts (fully automated by GitHub Pages)
**Scale/Scope**: Personal blog, estimated 10-50 posts per year, 3 languages per post

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**Simplicity First**: 
- [x] Change uses existing static HTML/CSS/JS structure (no new frameworks/build tools) - Uses Jekyll collections (already part of Jekyll), Liquid templating, existing Skeleton CSS
- [x] Dependencies are vendored locally (no new CDN dependencies) - No new external dependencies required
- [x] Change is immediately viewable without compilation - Jekyll serves blog posts automatically via `jekyll serve`
- [x] GitHub Pages compatibility maintained (supported Jekyll plugins only) - Uses only built-in Jekyll features (collections, Liquid), no custom plugins

**Content-Driven Updates**:
- [x] If content change: Updates only `_data/` YAML files or assets (CV, images) - Blog posts are markdown files (content), not layout changes
- [x] If layout change: Justification provided for modifying `_layouts/`, `_includes/`, or `libs/` - Will add new blog layouts (`_layouts/post.html`, `_layouts/blog-index.html`) and include for language switcher (`_includes/language_switcher.html`). **Justification**: Blog functionality requires dedicated templates; this is a new feature section, not modification of existing pages. Follows same pattern as `_layouts/project.html`.

**Constitution Check Result**: ✅ PASSED  
This feature aligns with both principles. It extends the content-driven architecture (markdown posts) and maintains simplicity (no build tools, GitHub Pages compatible).

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

### Blog Feature Additions (This Feature)

```text
_posts/                  # NEW: Blog posts collection
├── 2024-01-15-first-post.en.md
├── 2024-01-15-first-post.ko.md
└── 2024-01-15-first-post.mn.md

_layouts/
├── blog-index.html      # NEW: Blog listing page
└── post.html            # NEW: Individual post template

_includes/
└── language_switcher.html  # NEW: Cross-language navigation

assets/
└── blog/                # NEW: Blog-specific images/media
    └── [post-images]/

blog/
└── index.html           # NEW: Blog landing page
```

**Blog Structure Decision**: 
- **_posts/**: Jekyll standard collection, automatic date parsing from filename
- **File naming**: `YYYY-MM-DD-slug.LANG.md` (ASCII-only slugs for GitHub Pages compatibility)
- **Front matter**: `post_id` enables cross-referencing, `lang` determines language
- **No custom collections**: Use built-in `_posts` to avoid custom plugins (GitHub Pages restriction)
- **No subdirectories**: All posts flat in `_posts/` for simplicity (Jekyll convention)

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

**No violations identified.** Feature adheres to all constitutional principles:
- Uses existing Jekyll framework (no new frameworks)
- Content-driven via Markdown files (no code required for posts)
- Automatic GitHub Pages deployment (no build tools)
