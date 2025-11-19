# Component Interfaces: Navigation & Layout Redesign

**Feature**: 002-nav-layout-redesign | **Date**: 2025-11-19

## Overview

This document defines the interfaces (inputs/outputs) for reusable Jekyll components created for this feature. Since this is a static site with no REST/GraphQL APIs, "contracts" refer to component parameters, data dependencies, and expected HTML output structure.

## Component Contracts

### 1. Bio Section Component

**File**: `_includes/bio-section.html`

**Purpose**: Renders biographical information (photo, name, title, social icons) with variant styling for different contexts.

#### Input Parameters

```liquid
{% include bio-section.html variant="hero" %}
```

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `variant` | String | No | `"default"` | Styling variant: `"hero"` (large homepage header) or `"footer"` (article footer) |

#### Data Dependencies

**Source**: `site.data.main_info` (from `_data/main_info.yaml`)

**Required Fields**:
```yaml
name: String          # Person's full name
title: String         # Job title or tagline
profile_pic: String   # Path to profile image (relative to site root)
social_links: Array   # List of social media links
  - platform: String  # Platform name (e.g., "github", "linkedin")
    url: String       # Full URL to profile
    icon: String      # Font Awesome class (e.g., "fab fa-github")
```

#### Output HTML Structure

**Variant: "hero"** (Homepage header)
```html
<div class="bio-section bio-hero">
  <img src="/assets/profile-pics/profile.jpg" alt="Tergel Munkhbat" class="profile-pic-large">
  <h1 class="name">Tergel Munkhbat</h1>
  <p class="title">Software Engineer / Data Scientist</p>
  <div class="social-icons">
    <a href="https://github.com/tergelmunkhbat" title="GitHub">
      <i class="fab fa-github"></i>
    </a>
    <!-- Additional social links -->
  </div>
</div>
```

**Variant: "footer"** (Article footer)
```html
<div class="bio-section bio-footer">
  <img src="/assets/profile-pics/profile.jpg" alt="Tergel Munkhbat" class="profile-pic-small">
  <div class="bio-text">
    <h3 class="name">Tergel Munkhbat</h3>
    <p class="title">Software Engineer / Data Scientist</p>
    <div class="social-icons-inline">
      <a href="https://github.com/tergelmunkhbat" title="GitHub">
        <i class="fab fa-github"></i>
      </a>
      <!-- Additional social links -->
    </div>
  </div>
</div>
```

#### CSS Class Contract

Component applies these CSS classes (to be styled in `libs/custom/my_css.css`):

- `.bio-section` - Base styles for all bio sections
- `.bio-hero` - Hero header specific styles (large, centered)
- `.bio-footer` - Footer specific styles (compact, horizontal)
- `.profile-pic-large` - Large profile image (hero variant)
- `.profile-pic-small` - Small profile image (footer variant)
- `.name` - Name heading
- `.title` - Job title text
- `.social-icons` - Social icon container (hero: centered grid)
- `.social-icons-inline` - Social icon container (footer: inline row)

#### Error Handling

- **Missing `profile_pic`**: Renders broken image (browser handles gracefully)
- **Missing `name`**: Renders empty `<h1>` or `<h3>`
- **Empty `social_links`**: Renders empty `.social-icons` div (no icons)
- **Invalid `variant`**: Falls back to "default" styling

---

### 2. Slim Navbar Component

**File**: `_includes/slim-navbar.html`

**Purpose**: Renders sticky navigation bar for article pages with name/logo and menu links. Responsive with mobile hamburger menu.

#### Input Parameters

```liquid
{% include slim-navbar.html %}
```

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| None | - | - | - | Component is self-contained, no parameters needed |

#### Data Dependencies

**Source**: `site.data.main_info` (from `_data/main_info.yaml`)

**Required Fields**:
```yaml
name: String  # Person's name (used as navbar branding/logo)
```

**Navigation Links**: Hardcoded in component (not data-driven)

#### Output HTML Structure

```html
<header class="slim-navbar">
  <div class="navbar-container">
    <!-- Branding -->
    <div class="navbar-brand">
      <a href="/" class="brand-link">Tergel Munkhbat</a>
    </div>

    <!-- Mobile Hamburger Toggle (Checkbox Hack) -->
    <input type="checkbox" id="nav-toggle" class="nav-toggle" aria-label="Toggle navigation menu">
    <label for="nav-toggle" class="nav-toggle-label" aria-label="Menu">
      <span class="hamburger-icon">☰</span>
    </label>

    <!-- Navigation Menu -->
    <nav class="nav-menu" role="navigation">
      <a href="/" class="nav-link">Home</a>
      <a href="/blog/index.html" class="nav-link">Blog</a>
    </nav>
  </div>
</header>
```

#### CSS Class Contract

Component applies these CSS classes:

- `.slim-navbar` - Navbar container (sticky positioning, max-height: 100px)
- `.navbar-container` - Inner wrapper (flexbox layout)
- `.navbar-brand` - Branding section (left side)
- `.brand-link` - Name/logo link
- `.nav-toggle` - Hidden checkbox for mobile menu toggle
- `.nav-toggle-label` - Hamburger icon (visible on mobile)
- `.hamburger-icon` - Hamburger symbol (☰)
- `.nav-menu` - Navigation links container
- `.nav-link` - Individual menu links

#### Responsive Behavior Contract

**Desktop (≥768px)**:
- `.nav-menu` displayed as horizontal flexbox
- `.nav-toggle-label` hidden
- Menu links always visible

**Mobile (<768px)**:
- `.nav-menu` hidden by default
- `.nav-toggle-label` visible (hamburger icon)
- When `.nav-toggle:checked`, `.nav-menu` becomes visible
- Menu links stacked vertically

#### Accessibility Contract

- Navbar uses semantic `<header>` and `<nav>` elements
- Checkbox has `aria-label` for screen readers
- Hamburger label has `aria-label`
- All links are keyboard-navigable (default HTML behavior)
- Focus states styled for keyboard users

#### Error Handling

- **Missing `name`**: Renders empty brand link (still clickable, just no text)
- **Checkbox ID mismatch**: Hamburger toggle breaks (QA should catch this)

---

### 3. Blog Index Page

**File**: `blog/index.html`

**Purpose**: Renders paginated list of all blog posts with title, date, excerpt.

#### Input (Front Matter)

```yaml
---
layout: default
title: "Blog"
---
```

#### Data Dependencies

**Source**: `site.posts` (Jekyll built-in collection from `_posts/` directory)

**Required Post Fields** (from individual post front matter):
```yaml
title: String       # Post title
date: Date          # Publish date
excerpt: String     # Brief summary (auto-generated from content if not specified)
```

#### Output HTML Structure

```html
<div class="blog-index">
  <h1>Blog</h1>

  <div class="post-list">
    <!-- Repeated for each post -->
    <article class="post-preview">
      <h2 class="post-title">
        <a href="/blog/2025/11/19/article-title.html">Article Title</a>
      </h2>
      <time class="post-date" datetime="2025-11-19">November 19, 2025</time>
      <p class="post-excerpt">This is the article excerpt...</p>
    </article>

    <article class="post-preview">
      <!-- Next post -->
    </article>
  </div>
</div>
```

#### CSS Class Contract

- `.blog-index` - Page container
- `.post-list` - List of post previews
- `.post-preview` - Individual post preview
- `.post-title` - Post title heading
- `.post-date` - Post publish date
- `.post-excerpt` - Post summary text

#### Behavior Contract

**Post Ordering**: Posts appear in reverse chronological order (newest first) - handled by Jekyll's default `site.posts` ordering.

**Link Format**: Post URLs follow Jekyll convention: `/blog/:year/:month/:day/:title.html`

#### Error Handling

- **No posts in `_posts/`**: Renders empty `.post-list` (no error, just no content)
- **Missing post title**: Renders "Untitled Post"
- **Missing post excerpt**: Auto-generated from first paragraph of content

---

## Layout Conditional Logic Contract

### Page Type Detection

**File**: `_layouts/default.html` and/or `_layouts/post.html`

**Purpose**: Determine which header to render based on page layout.

#### Conditional Logic

```liquid
{% if page.layout == 'post' %}
  {% include slim-navbar.html %}
{% else %}
  {% include bio-section.html variant="hero" %}
{% endif %}
```

#### Contract

| Page Layout | Header Rendered | Bio in Footer |
|-------------|-----------------|---------------|
| `post` | Slim navbar | Yes (bio-section variant="footer") |
| `default` | Hero header (bio-section variant="hero") | No |
| `project` (or others) | Hero header (fallback to default) | No |

#### Footer Logic (Post Layout Only)

```liquid
<!-- In _layouts/post.html, after article content -->
<footer class="article-footer">
  {% include bio-section.html variant="footer" %}
</footer>
```

---

## Data Flow Diagram

```
_data/main_info.yaml
  ├─> bio-section.html (variant="hero")
  │    └─> Renders on: index.html (layout: default)
  │
  ├─> bio-section.html (variant="footer")
  │    └─> Renders on: _posts/*.md (layout: post)
  │
  └─> slim-navbar.html (uses name field)
       └─> Renders on: _posts/*.md (layout: post)

_posts/*.md (site.posts)
  └─> blog/index.html
       └─> Lists all posts with links
```

---

## CSS Dependency Contract

### Required CSS Variables (Optional Enhancement)

```css
/* Recommended CSS custom properties for consistency */
:root {
  --navbar-max-height: 100px;
  --navbar-bg-color: #ffffff;
  --navbar-text-color: #333333;
  --navbar-shadow: 0 2px 4px rgba(0,0,0,0.1);
  --mobile-breakpoint: 768px;
}
```

### Required Media Queries

```css
/* Mobile-first approach */
@media (max-width: 767px) {
  /* Mobile styles: hamburger menu, stacked layout */
}

@media (min-width: 768px) {
  /* Desktop styles: horizontal menu, full navbar */
}
```

---

## Testing Contract

Each component MUST pass these tests before deployment:

### Bio Section Tests
- [ ] Renders correctly with `variant="hero"`
- [ ] Renders correctly with `variant="footer"`
- [ ] Handles missing `profile_pic` gracefully
- [ ] Displays all social links from `main_info.yaml`

### Slim Navbar Tests
- [ ] Displays on all post layout pages
- [ ] Does NOT display on homepage
- [ ] Sticky positioning works (stays at top when scrolling)
- [ ] Max height does not exceed 100px
- [ ] Hamburger menu toggles on mobile (<768px)
- [ ] Menu links work without JavaScript

### Blog Index Tests
- [ ] Lists all posts from `_posts/` directory
- [ ] Posts ordered newest first
- [ ] Post links navigate to correct URLs
- [ ] Handles zero posts gracefully (empty list)

---

## Versioning

**Contract Version**: 1.0.0
**Last Updated**: 2025-11-19
**Breaking Changes**: N/A (initial version)

If component interfaces change in future, increment version and document breaking changes here.
