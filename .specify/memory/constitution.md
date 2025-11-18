<!--
  SYNC IMPACT REPORT
  Version Change: 1.1.0 → 1.2.0
  Rationale: MINOR version bump - Added _posts/ directory to content update mechanisms for blog support
  
  Modified Principles:
  - Principle II: Added "_posts/ markdown files (blog content)" to authorized content directories
  
  Justification:
  - Blog posts are content, not presentation logic
  - Follows same pattern as _data/ YAML files (structured content separate from layout)
  - Enables trilingual blog feature (001-trilingual-blog) without violating principles
  
  Templates Status:
  ✅ All templates remain valid - no changes needed
  
  Follow-up TODOs: None
-->

# Tergel Munkhbat's Personal Website Constitution

## Core Principles

### I. Simplicity First
Static HTML/CSS/JS website with minimal dependencies. No manual build steps are required for deployment. Local preview is handled by `jekyll serve`. Content is managed through YAML data files in `_data/` directory. The build process MUST be fully automated by `jekyll serve` for local preview and by GitHub Pages for deployment.

**Rationale**: A personal academic/professional website requires fast updates and maximum reliability. Build complexity adds friction to content updates and introduces failure points.

### II. Content-Driven Updates
All content updates MUST occur exclusively in `_data/` YAML files (experience, projects, publications, main_info), `_posts/` markdown files (blog content), or static assets (images, CV). Changes to layout/styling in `_layouts/`, `_includes/`, or `libs/` require explicit justification.

**Rationale**: Separating content from presentation enables non-technical updates while preserving design integrity.

## Technology Stack

**Framework**: Jekyll (Used by GitHub Pages for server-side builds and by `jekyll serve` for local preview)
**CSS Libraries**: Skeleton framework, Font Awesome icons, Academicons, custom timeline CSS
**JavaScript**: jQuery 3.1.1 (minimal usage for tabs/interactions)
**Data Format**: YAML files in `_data/` directory
**Deployment**: GitHub Pages

**Constraints**:
- No new CSS/JS frameworks without removing existing ones (prevent bloat)
- New dependencies MUST be vendored locally (no CDN reliance for production)
- Changes MUST work with Jekyll static site generation
- Only GitHub Pages-compatible Jekyll plugins allowed (no custom build process)

## Governance

This constitution guides all website updates and modifications. Changes to core functionality (layout, deployment, data structure) require verification against these principles.

**Amendment Process**:
- Amendments require updating this file via `.specify/templates/commands/constitution.md` workflow
- Version increments follow semantic versioning (MAJOR.MINOR.PATCH)
- All amendments MUST validate template alignment

**Compliance**:
- Content updates in `_data/` do NOT require constitution checks
- Layout/infrastructure changes MUST justify alignment with Simplicity First principle
- Test changes locally with `jekyll serve` before pushing to master
- Verify GitHub Pages build succeeds after deployment

**Version**: 1.2.0 | **Ratified**: 2025-11-18 | **Last Amended**: 2025-11-18
