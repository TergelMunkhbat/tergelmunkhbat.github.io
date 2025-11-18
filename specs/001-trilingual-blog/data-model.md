# Data Model: Trilingual Blog

**Feature**: 001-trilingual-blog  
**Date**: 2025-11-18  
**Purpose**: Define data structures and schemas for blog posts and related entities

## Front Matter Schema

### Blog Post (Language Version)

Each markdown file represents ONE language version of a blog post. All language versions of the same post share a `post_id`.

**Required Fields**:
```yaml
layout: post                    # Jekyll layout (use 'post')
title: "My First Blog Post"     # Localized title in the post's language
date: 2025-11-18 10:00:00 -0500 # Publication date/time (MUST be same across all language versions)
lang: en                        # Language code: en, ko, or mn
post_id: 2025-11-18-first-post  # Unique identifier shared across translations
```

**Optional Fields**:
```yaml
excerpt: "A brief summary..."   # Custom excerpt (if omitted, Jekyll uses first ~150 chars)
author: "Tergel Munkhbat"       # Author name (optional, can default to site owner)
categories: [tech, ai]          # Post categories (optional)
tags: [jekyll, blog, tutorial]  # Post tags (optional)
```

**Field Validation Rules**:

| Field | Type | Constraints | Example |
|-------|------|-------------|---------|
| `layout` | String | MUST be `"post"` | `post` |
| `title` | String | REQUIRED, 1-200 chars | `"나의 첫 블로그 포스트"` |
| `date` | DateTime | REQUIRED, YAML date format, MUST match across translations | `2025-11-18 10:00:00 -0500` |
| `lang` | String | REQUIRED, MUST be one of: `en`, `ko`, `mn` | `ko` |
| `post_id` | String | REQUIRED, MUST be unique per post (not per language), ASCII only, suggest `YYYY-MM-DD-slug` format | `2025-11-18-first-post` |
| `excerpt` | String | Optional, custom excerpt (if omitted, Jekyll uses first paragraph) | `"This post introduces..."` |

**Example Full Front Matter (English)**:
```yaml
---
layout: post
title: "Building a Trilingual Blog with Jekyll"
date: 2025-11-18 10:00:00 -0500
lang: en
post_id: 2025-11-18-trilingual-blog
excerpt: "Learn how to create a blog that supports multiple languages using Jekyll and GitHub Pages."
categories: [web-development]
tags: [jekyll, i18n, tutorial]
---
```

**Example Full Front Matter (Korean)**:
```yaml
---
layout: post
title: "Jekyll로 다국어 블로그 만들기"
date: 2025-11-18 10:00:00 -0500
lang: ko
post_id: 2025-11-18-trilingual-blog
excerpt: "Jekyll과 GitHub Pages를 사용하여 여러 언어를 지원하는 블로그를 만드는 방법을 알아보세요."
categories: [web-development]
tags: [jekyll, i18n, tutorial]
---
```

---

## File Naming Convention

**Pattern**: `YYYY-MM-DD-slug.LANG.md`

**Components**:
- `YYYY-MM-DD`: Publication date (must match `date` in front matter)
- `slug`: URL-friendly post identifier (ASCII lowercase, hyphens)
- `LANG`: Language code (`en`, `ko`, or `mn`)
- `.md`: Markdown extension

**Examples**:
```
_posts/2025-11-18-first-post.en.md
_posts/2025-11-18-first-post.ko.md
_posts/2025-11-18-first-post.mn.md
```

**Rules**:
- Filenames MUST use ASCII characters only (no Korean, Mongolian, or special characters)
- Date prefix MUST match the `date` field in front matter
- Slug SHOULD match the `post_id` (minus the date prefix) for consistency
- Language suffix (`.en`, `.ko`, `.mn`) helps organize files visually but is NOT used for language detection (use `lang` front matter field)

---

## Directory Structure

```
_posts/
├── 2025-11-18-welcome.en.md
├── 2025-11-18-welcome.ko.md
├── 2025-11-18-welcome.mn.md
├── 2025-11-20-ai-research.en.md
├── 2025-11-20-ai-research.ko.md
└── 2025-11-20-ai-research.mn.md

_layouts/
├── post.html              # Individual blog post layout
└── blog-index.html        # Blog listing page layout

_includes/
└── language_switcher.html # Language switcher component

assets/
└── blog/
    ├── images/
    └── files/

blog/
└── index.html             # Blog index page (uses blog-index layout)
```

---

## Data Relationships

### Post Group (Logical Entity)

A "Post" conceptually consists of 1-3 language versions, all sharing the same `post_id`.

**Cardinality**: 
- One `post_id` → 1 to 3 language versions
- Each language version → exactly 1 `post_id`

**Example**:
```
Post ID: "2025-11-18-first-post"
├── Language Version (EN): _posts/2025-11-18-first-post.en.md
├── Language Version (KO): _posts/2025-11-18-first-post.ko.md
└── Language Version (MN): _posts/2025-11-18-first-post.mn.md
```

**Linking Mechanism**:
- At build time, Jekyll loads all posts into `site.posts` collection
- Liquid template filters posts by `post_id` to find translations
- Language switcher dynamically renders links to available translations

---

## Derived Data (Computed at Build Time)

### Blog Index Entries

The blog index page displays one entry per `post_id`, using language priority fallback.

**Computed Fields**:
- `display_post`: The language version to show (en → ko → mn priority)
- `available_languages`: Array of language codes for which versions exist
- `translation_urls`: Map of language codes to post URLs

**Example Liquid Logic**:
```liquid
{% assign post_groups = site.posts | group_by: "post_id" %}
{% for group in post_groups %}
  {% assign display_post = group.items | where: "lang", "en" | first %}
  {% unless display_post %}
    {% assign display_post = group.items | where: "lang", "ko" | first %}
  {% endunless %}
  {% unless display_post %}
    {% assign display_post = group.items | where: "lang", "mn" | first %}
  {% endunless %}
  
  <!-- Display display_post.title, display_post.date, display_post.excerpt -->
{% endfor %}
```

---

## Invariants & Constraints

### Data Integrity Rules

1. **Unique `post_id` per Post**: Each `post_id` value MUST represent a single logical post (not reused for different posts)
   - ⚠️ Manual validation required (see research.md limitation)
   - Mitigation: Date-based naming convention (`YYYY-MM-DD-slug`)

2. **Consistent `date` across Translations**: All language versions of a post MUST have identical `date` values
   - Purpose: Ensures consistent chronological ordering
   - Validation: Manual testing

3. **Valid `lang` Values**: `lang` field MUST be one of: `en`, `ko`, `mn`
   - Enforced by: Documentation and testing

4. **ASCII Filenames**: All filenames MUST use ASCII characters only
   - Purpose: Avoid GitHub Pages URL encoding issues
   - Enforced by: File system restrictions and documentation

5. **Language Uniqueness per Post**: Each `post_id` can have at most ONE version per language
   - Example: Cannot have two `en` versions for same `post_id`
   - Detection: Language switcher would show duplicates (QA visible)

---

## State Transitions

### Post Lifecycle

```
1. DRAFT (local)
   ├─> Author creates markdown files with front matter
   └─> Author tests locally with `jekyll serve`

2. COMMITTED (git)
   ├─> Files added to _posts/ directory
   └─> Pushed to master branch

3. BUILDING (GitHub Pages)
   ├─> Jekyll processes markdown files
   ├─> Generates HTML pages
   └─> Validates front matter (basic YAML syntax only)

4. PUBLISHED (live)
   ├─> Posts accessible via URLs
   ├─> Listed on blog index
   └─> Language switcher active

5. UPDATED (modification)
   ├─> Edit front matter or content
   └─> Repeat commit → build → publish cycle

6. DEPRECATED/DELETED (removal)
   └─> Delete markdown file(s) from _posts/
```

---

## Edge Case Handling

### Missing Language Versions

**Scenario**: Post has English and Korean versions, but no Mongolian version

**Behavior**:
- Blog index: Shows English version (priority order)
- Language switcher on EN post: Shows EN (active) and KO links, hides MN
- Language switcher on KO post: Shows EN and KO (active) links, hides MN

**Implementation**: Liquid conditional checks if translation exists before rendering link

### Orphaned Language Version

**Scenario**: Post has only Mongolian version (no English or Korean)

**Behavior**:
- Blog index: Shows Mongolian version (fallback to mn)
- Language switcher on MN post: Shows only MN (active), no other links

### Duplicate `post_id` (Error Condition)

**Scenario**: Two different posts accidentally use `post_id: "my-post"`

**Behavior**: 
- Language switcher will show incorrect links (mixing unrelated posts)
- Visible during testing - MUST be caught in QA

**Prevention**: 
- Date-based naming convention reduces collision likelihood
- Manual checklist during post creation

---

## Summary

| Concept | Storage | Schema |
|---------|---------|--------|
| Language Version | Markdown file in `_posts/` | Front matter: layout, title, date, lang, post_id, excerpt |
| Post Group | Logical (not stored) | All files sharing same `post_id` |
| Blog Index Entry | Computed at build | First available: en → ko → mn |
| Language Links | Computed at runtime | Liquid filter: `where: "post_id"` |

**Validation Strategy**: Primarily manual testing with documented conventions and checklists (see quickstart.md)

**GitHub Pages Compatibility**: ✅ All data structures use standard Jekyll features
