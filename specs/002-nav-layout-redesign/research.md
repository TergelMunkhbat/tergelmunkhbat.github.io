# Research: Navigation & Layout Redesign

**Feature**: 002-nav-layout-redesign | **Date**: 2025-11-19

## Overview

This document captures research decisions for implementing context-aware headers and responsive navigation on a static Jekyll website using existing HTML/CSS/JavaScript (jQuery 3.1.1, Skeleton CSS).

## Research Questions & Decisions

### 1. Context-Aware Header Rendering in Jekyll

**Question**: How to conditionally render different headers (hero header vs slim navbar) based on page type without JavaScript?

**Decision**: Use Jekyll Liquid templating with page layout detection

**Rationale**:
- Jekyll provides `page.layout` variable in templates to distinguish page types
- Can create conditional includes: `{% if page.layout == 'post' %}` for article pages
- Server-side rendering means zero JavaScript overhead and works with JS disabled
- Aligns with static site philosophy - decision made at build time

**Implementation Approach**:
```liquid
{% if page.layout == 'post' %}
  {% include slim-navbar.html %}
{% else %}
  {% include hero-header.html %}
{% endif %}
```

**Alternatives Considered**:
- JavaScript detection of page type: Rejected - causes flash of wrong header, breaks with JS disabled
- CSS-only hiding: Rejected - loads both headers on all pages, wastes bandwidth
- Separate layout files: Considered viable but adds duplication; conditional includes cleaner

### 2. Sticky Navbar Implementation

**Question**: Best approach for sticky/fixed navbar using only CSS (no new frameworks)?

**Decision**: Use CSS `position: sticky` with fallback to `position: fixed`

**Rationale**:
- `position: sticky` is modern CSS standard with excellent browser support (>95% as of 2025)
- Falls back gracefully to `position: fixed` in older browsers
- No JavaScript required for basic functionality
- Skeleton CSS doesn't provide sticky positioning, but it's a standard CSS feature

**Implementation Approach**:
```css
.slim-navbar {
  position: -webkit-sticky; /* Safari compatibility */
  position: sticky;
  top: 0;
  z-index: 1000;
  max-height: 100px;
}
```

**Alternatives Considered**:
- JavaScript scroll listeners: Rejected - unnecessary complexity, breaks progressive enhancement
- `position: fixed` only: Works but less elegant (navbar always fixed even at top of page)
- Intersection Observer API: Rejected - requires JavaScript, overkill for simple sticky behavior

### 3. Mobile Hamburger Menu Pattern

**Question**: How to implement hamburger menu collapse/expand with minimal JavaScript using existing jQuery?

**Decision**: Use checkbox hack for CSS-only toggle with optional jQuery enhancement

**Rationale**:
- Checkbox hack (hidden checkbox + label) provides CSS-only functionality
- Works without JavaScript (progressive enhancement)
- Can enhance with jQuery for animations if desired
- Lightweight - no new libraries needed
- Accessibility: checkbox is keyboard-navigable by default

**Implementation Approach**:
```html
<input type="checkbox" id="nav-toggle" class="nav-toggle">
<label for="nav-toggle" class="nav-toggle-label">☰</label>
<nav class="nav-menu">...</nav>
```

```css
.nav-menu { display: none; }
.nav-toggle:checked ~ .nav-menu { display: block; }

@media (min-width: 768px) {
  .nav-menu { display: flex; }
  .nav-toggle-label { display: none; }
}
```

**Alternatives Considered**:
- jQuery click handlers only: Rejected - breaks without JS
- CSS `:target` pseudo-class: Rejected - changes URL hash, affects browser history
- Bootstrap collapse component: Rejected - violates "no new frameworks" constraint

### 4. Blog Index Page Structure

**Question**: How to create `/blog/index.html` with Jekyll to list all blog posts?

**Decision**: Create `blog/index.html` using Jekyll's `site.posts` iteration

**Rationale**:
- Jekyll automatically populates `site.posts` collection from `_posts/` directory
- Standard Jekyll pattern for blog index pages
- Already using Jekyll, no additional dependencies
- Supports pagination if needed in future (jekyll-paginate plugin)

**Implementation Approach**:
```liquid
---
layout: default
title: Blog
---
<div class="blog-index">
  {% for post in site.posts %}
    <article>
      <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
      <time>{{ post.date | date: "%B %d, %Y" }}</time>
      <p>{{ post.excerpt }}</p>
    </article>
  {% endfor %}
</div>
```

**Alternatives Considered**:
- Manual YAML data file with post list: Rejected - duplicates Jekyll's built-in functionality
- JavaScript-generated index: Rejected - breaks without JS, worse SEO
- Collections instead of posts: Posts are standard Jekyll pattern for blogs

### 5. Page Type Detection for Article Pages

**Question**: How to identify "article pages" vs homepage for header logic?

**Decision**: Use Jekyll layout `post` for all article/blog pages

**Rationale**:
- Jekyll convention: blog posts use `layout: post` in front matter
- Already established in existing trilingual blog feature (001-trilingual-blog)
- Clear semantic distinction: `default` layout = homepage, `post` layout = articles
- Easy to extend for future content types

**Detection Logic**:
```liquid
{% if page.layout == 'post' %}
  <!-- Article page: slim navbar + footer bio -->
{% else %}
  <!-- Homepage/other: hero header -->
{% endif %}
```

**Alternatives Considered**:
- URL path matching: Rejected - fragile, breaks if URLs change
- Custom front matter variable (e.g., `article: true`): Rejected - redundant with layout
- File location detection: Possible but less explicit than layout check

### 6. Responsive Breakpoint Strategy

**Question**: What breakpoint should trigger hamburger menu collapse?

**Decision**: Use Skeleton CSS's existing breakpoint at 768px (medium devices)

**Rationale**:
- Skeleton CSS already defines breakpoints: 550px (mobile), 768px (tablet), 1000px (desktop)
- Consistency with existing framework reduces cognitive overhead
- 768px is industry standard (matches Bootstrap, Tailwind default "md" breakpoint)
- Below 768px, horizontal menu links would be cramped in slim navbar

**Breakpoint Logic**:
```css
/* Mobile: hamburger menu */
@media (max-width: 767px) {
  .nav-links { display: none; }
  .nav-toggle-label { display: block; }
  .nav-toggle:checked ~ .nav-links { display: block; }
}

/* Tablet and up: horizontal menu */
@media (min-width: 768px) {
  .nav-links { display: flex; }
  .nav-toggle-label { display: none; }
}
```

**Alternatives Considered**:
- Custom breakpoint at 640px: Rejected - inconsistent with Skeleton CSS
- Multiple breakpoints with different menu styles: Rejected - unnecessary complexity
- No breakpoint (always show hamburger): Rejected - wastes space on desktop

### 7. Bio Section Reusability

**Question**: How to avoid duplicating bio content between hero header and article footer?

**Decision**: Create shared `_includes/bio-section.html` partial with parameter for styling variant

**Rationale**:
- DRY principle - single source of truth for bio content
- Jekyll includes support parameters for variant rendering
- Content comes from `_data/main_info.yaml` (already established pattern)
- Easy to maintain - update bio once, applies everywhere

**Implementation Approach**:
```liquid
<!-- _includes/bio-section.html -->
<div class="bio-section {% if include.variant %}bio-{{ include.variant }}{% endif %}">
  {% assign info = site.data.main_info %}
  <img src="{{ info.profile_pic }}" alt="{{ info.name }}">
  <h1>{{ info.name }}</h1>
  <p>{{ info.title }}</p>
  <!-- social icons loop -->
</div>

<!-- Usage -->
{% include bio-section.html variant="hero" %}
{% include bio-section.html variant="footer" %}
```

**Alternatives Considered**:
- Duplicate HTML in two files: Rejected - violates DRY, maintenance burden
- JavaScript to move DOM elements: Rejected - breaks without JS, complex
- CSS to hide/show same element: Rejected - loads content twice, accessibility issues

### 8. Navbar Height Constraint Implementation

**Question**: How to enforce maximum 100px height constraint while maintaining flexibility?

**Decision**: Use `max-height: 100px` CSS property with flexbox for content alignment

**Rationale**:
- `max-height` provides upper bound while allowing smaller heights if content allows
- Flexbox alignment ensures vertical centering within available space
- Responsive padding can scale down on mobile if needed
- Maintains accessibility - content determines minimum height, not arbitrary fixed value

**Implementation Approach**:
```css
.slim-navbar {
  max-height: 100px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1rem 2rem;
}
```

**Alternatives Considered**:
- Fixed `height: 100px`: Rejected - too rigid, could clip content or leave empty space
- No height constraint: Rejected - violates SC-002 success criteria
- JavaScript dynamic height calculation: Rejected - unnecessary, CSS handles this elegantly

## Technology Best Practices

### Jekyll Static Site Best Practices
- Use `_includes/` for reusable components (navbar, bio section)
- Use `_layouts/` for page templates with conditional logic
- Store configuration in `_data/` YAML files for easy updates
- Test locally with `jekyll serve --livereload` for fast iteration
- Validate front matter in all pages/posts

### Skeleton CSS Best Practices
- Use existing grid system (`.container`, `.row`, `.columns`) for navbar layout
- Leverage Skeleton's responsive utilities where available
- Extend with custom CSS in `libs/custom/my_css.css` (existing pattern)
- Maintain mobile-first approach (Skeleton's default philosophy)

### Progressive Enhancement Best Practices
- Core functionality (navigation, sticky positioning) works without JavaScript
- Enhance with jQuery for animations/transitions if desired
- Use semantic HTML (`<nav>`, `<header>`, `<footer>`) for accessibility
- Ensure keyboard navigation works (checkbox hack is keyboard-friendly)
- Test with JavaScript disabled to verify baseline functionality

### Performance Best Practices
- Minimize CSS specificity for faster rendering
- Use CSS transforms for smooth sticky transitions (GPU-accelerated)
- Avoid layout thrashing - read then write DOM properties
- Lazy-load bio section images if large (use `loading="lazy"` attribute)
- Minify CSS additions before deployment (if size becomes concern)

## Risk Mitigation

### Potential Issues & Mitigation

1. **Risk**: Sticky navbar causes content jump on page load
   - **Mitigation**: Reserve space for navbar with padding-top on content container

2. **Risk**: Hamburger menu breaks accessibility
   - **Mitigation**: Use checkbox hack (keyboard accessible) + ARIA labels for screen readers

3. **Risk**: Bio section footer looks awkward on short articles
   - **Mitigation**: Apply consistent padding/margins regardless of article length (edge case addressed in spec)

4. **Risk**: `/blog/index.html` conflicts with existing `/blog/` structure
   - **Mitigation**: Verify current directory structure before implementation; adjust path if needed

5. **Risk**: CSS-only sticky doesn't work in older browsers
   - **Mitigation**: Include `position: -webkit-sticky` prefix; graceful degradation to static positioning acceptable

## Implementation Dependencies

### Files to Create
- `_includes/slim-navbar.html` - Slim navbar component
- `_includes/bio-section.html` - Reusable bio component with variant support
- `blog/index.html` - Blog listing page
- CSS additions in `libs/custom/my_css.css` for navbar styling

### Files to Modify
- `_layouts/default.html` - Add conditional header logic
- `_layouts/post.html` - Include footer bio section
- Potentially `index.html` if hero header needs extraction to include

### No New Dependencies Required
All functionality achievable with:
- Existing Jekyll (already in use)
- Existing Skeleton CSS (already vendored)
- Existing jQuery 3.1.1 (already vendored)
- Standard CSS3 features (sticky positioning, flexbox, media queries)

## Validation Checklist

- [x] All research questions answered with concrete decisions
- [x] Rationale provided for each decision
- [x] Alternatives considered and documented
- [x] Implementation approaches outlined
- [x] Best practices identified for relevant technologies
- [x] Risks identified and mitigations proposed
- [x] No new dependencies required (constitutional compliance)
- [x] All decisions align with Simplicity First principle
- [x] Progressive enhancement maintained throughout

## Next Steps

Proceed to Phase 1: Design & Contracts
- Generate `data-model.md` (likely minimal for this UI-focused feature)
- Create `quickstart.md` for development workflow
- Update agent context with research findings
