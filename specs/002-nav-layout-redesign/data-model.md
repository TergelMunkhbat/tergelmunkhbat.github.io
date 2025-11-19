# Data Model: Navigation & Layout Redesign

**Feature**: 002-nav-layout-redesign | **Date**: 2025-11-19

## Overview

This feature is primarily UI/layout-focused and does not introduce new data entities or storage requirements. All data needed for the navigation and bio sections already exists in the current `_data/main_info.yaml` structure.

## Existing Data Structures (Used by Feature)

### Main Info (Source: `_data/main_info.yaml`)

**Purpose**: Stores personal/biographical information used in hero header and bio footer

**Structure**:
```yaml
name: "Tergel Munkhbat"
title: "Software Engineer / Data Scientist"
profile_pic: "/assets/profile-pics/profile.jpg"
email: "tergel.munkhbat@example.com"
social_links:
  - platform: "github"
    url: "https://github.com/tergelmunkhbat"
    icon: "fab fa-github"
  - platform: "linkedin"
    url: "https://linkedin.com/in/tergelmunkhbat"
    icon: "fab fa-linkedin"
  # ... additional social platforms
```

**Usage in Feature**:
- Hero header (homepage): Displays name, title, profile_pic, social_links
- Bio footer (article pages): Same fields, different styling/layout
- Navbar: Uses name as branding/logo text

**No modifications required** - existing structure sufficient

### Blog Posts (Source: `_posts/` directory, Jekyll built-in)

**Purpose**: Jekyll automatically generates `site.posts` collection from markdown files in `_posts/`

**Front Matter Structure** (per post):
```yaml
---
layout: post
title: "Article Title"
date: 2025-11-19
lang: en  # from trilingual blog feature
categories: [category1, category2]
excerpt: "Brief summary for blog index"
---
```

**Usage in Feature**:
- Blog index page (`/blog/index.html`) iterates over `site.posts`
- Post layout determines header type (slim navbar vs hero header)

**No modifications required** - Jekyll's built-in post handling sufficient

## Page Metadata

### Page Layout Detection

**Purpose**: Determines which header variant to render

**Source**: Jekyll front matter `layout` field

**Values**:
- `layout: default` → Hero header (homepage)
- `layout: post` → Slim navbar (article pages)

**Access Pattern**:
```liquid
{% if page.layout == 'post' %}
  {% include slim-navbar.html %}
{% else %}
  {% include hero-header.html %}
{% endif %}
```

**Validation Rules**:
- Every page MUST have a `layout` specified in front matter
- Article pages MUST use `layout: post`
- Homepage MUST use `layout: default`

## Navigation Menu Configuration

### Menu Items

**Purpose**: Define navigation links in slim navbar

**Storage**: Hardcoded in `_includes/slim-navbar.html` template (intentionally not data-driven for simplicity)

**Structure**:
```html
<nav class="nav-menu">
  <a href="/">Home</a>
  <a href="/blog/index.html">Blog</a>
</nav>
```

**Rationale for Hardcoding**:
- Only 2 menu items (Home, Blog) - unlikely to change frequently
- Simplicity First principle - avoid over-engineering with YAML config
- No dynamic menu generation needed
- Easy to extend to YAML if menu grows beyond 3-4 items

**Future Extension** (if menu grows):
Could migrate to `_data/navigation.yaml`:
```yaml
menu_items:
  - label: "Home"
    url: "/"
  - label: "Blog"
    url: "/blog/index.html"
  - label: "Projects"
    url: "/projects/"
```

## Component State (CSS-Driven)

### Mobile Menu Toggle

**Purpose**: Controls hamburger menu expansion on mobile devices

**Mechanism**: CSS checkbox hack (stateless from data perspective)

**State Management**:
```html
<input type="checkbox" id="nav-toggle" class="nav-toggle">
```

- **Unchecked**: Menu collapsed (default)
- **Checked**: Menu expanded

**Persistence**: None - state resets on page navigation (intentional)

**No JavaScript state required** - CSS handles show/hide logic

## Relationships

```
_data/main_info.yaml
  ├─> Hero Header (_includes/hero-header.html)
  │    └─> Used on: index.html (layout: default)
  │
  ├─> Bio Footer (_includes/bio-section.html variant="footer")
  │    └─> Used on: All posts (layout: post)
  │
  └─> Slim Navbar (_includes/slim-navbar.html)
       └─> Used on: All posts (layout: post)

_posts/*.md (Jekyll site.posts)
  └─> Blog Index (/blog/index.html)
       └─> Linked from: Slim Navbar "Blog" menu item
```

## Data Flow

### Homepage Rendering
1. User navigates to `/` (index.html)
2. Jekyll detects `layout: default`
3. Template includes hero-header.html
4. Hero header loads data from `site.data.main_info`
5. Renders name, title, profile_pic, social_links

### Article Page Rendering
1. User navigates to article (e.g., `/blog/2025/11/19/my-post.html`)
2. Jekyll detects `layout: post`
3. Template includes slim-navbar.html (top)
4. Template includes bio-section.html (footer)
5. Both components load data from `site.data.main_info`
6. Navbar renders name + menu links
7. Bio footer renders full bio information

### Blog Index Rendering
1. User navigates to `/blog/index.html`
2. Template iterates `site.posts` collection
3. For each post: extracts title, date, excerpt, url
4. Renders as linked list of articles

## Validation Rules

### Data Integrity
- `_data/main_info.yaml` MUST contain: name, title, profile_pic
- `_data/main_info.yaml.social_links` MUST be an array
- Each social link MUST have: platform, url, icon

### Layout Constraints
- Article pages MUST use `layout: post` in front matter
- Homepage MUST use `layout: default` in front matter
- Invalid layout falls back to default (hero header)

### URL Patterns
- Blog index MUST be accessible at `/blog/index.html`
- Individual posts follow Jekyll convention: `/blog/:year/:month/:day/:title.html`

## Performance Considerations

### Data Loading
- All data loaded at build time (static site)
- No runtime database queries
- No API calls
- Bio information loaded once per page render

### Caching
- Static HTML output cached by CDN/browser
- No dynamic data invalidation needed
- Changes require Jekyll rebuild + redeployment

## No New Storage Required

**Summary**: This feature requires zero new data storage. It repurposes existing data structures and relies on Jekyll's built-in templating and front matter capabilities. All state is managed through CSS (mobile menu toggle) or server-side rendering (page layout detection).

## Migration Notes

**N/A** - No data migration required. Feature works with existing data as-is.

## Testing Data Requirements

### Manual Testing Checklist
- Verify `_data/main_info.yaml` contains all required fields
- Confirm at least one post exists in `_posts/` for blog index testing
- Test with missing profile_pic (should degrade gracefully)
- Test with empty social_links array (should render without icons)

### Edge Case Data Scenarios
- Very long name (>30 characters): Verify navbar layout doesn't break
- Missing title field: Verify graceful fallback or error handling
- Non-existent profile_pic path: Verify broken image handling
