# Quickstart Guide: Creating Trilingual Blog Posts

**Feature**: 001-trilingual-blog  
**Audience**: Site owners creating new blog posts  
**Time to Complete**: ~10 minutes per post

## Overview

This guide shows you how to create a new blog post with English, Korean, and Mongolian versions.

## Prerequisites

- ✅ Jekyll installed locally (`gem install jekyll bundler`)
- ✅ Git repository cloned locally
- ✅ Text editor (VS Code, Sublime, etc.)
- ✅ Basic understanding of Markdown and YAML

## Step-by-Step: Create a New Trilingual Post

### Step 1: Choose a Unique `post_id`

**Format**: `YYYY-MM-DD-slug`

**Rules**:
- Use today's date (or publication date)
- Use lowercase letters and hyphens only (no spaces, no special characters)
- Make it descriptive but concise
- **MUST be unique** - check existing `_posts/` files to avoid duplicates

**Examples**:
- ✅ `2025-11-18-my-first-post`
- ✅ `2025-12-01-ai-research-update`
- ❌ `my post` (has spaces)
- ❌ `2025-11-18` (not descriptive)
- ❌ `first-post` (missing date prefix)

### Step 2: Create Three Markdown Files

Create three files in the `_posts/` directory:

```
_posts/2025-11-18-my-first-post.en.md
_posts/2025-11-18-my-first-post.ko.md
_posts/2025-11-18-my-first-post.mn.md
```

**Naming Convention**:
- Start with date: `YYYY-MM-DD-`
- Add slug (should match `post_id` minus date)
- Add language code: `.en`, `.ko`, or `.mn`
- End with `.md`

### Step 3: Add Front Matter to Each File

Copy this template and customize for each language:

**English Version** (`2025-11-18-my-first-post.en.md`):
```markdown
---
layout: post
title: "My First Blog Post"
date: 2025-11-18 10:00:00 -0500
lang: en
post_id: 2025-11-18-my-first-post
excerpt: "This is my first trilingual blog post. Learn how I set up this blog."
---

# My First Blog Post

Welcome to my blog! This is the English version.

## Introduction

Here's some content in English...

```

**Korean Version** (`2025-11-18-my-first-post.ko.md`):
```markdown
---
layout: post
title: "나의 첫 블로그 포스트"
date: 2025-11-18 10:00:00 -0500
lang: ko
post_id: 2025-11-18-my-first-post
excerpt: "이것은 나의 첫 번째 삼국어 블로그 포스트입니다. 블로그 설정 방법을 알아보세요."
---

# 나의 첫 블로그 포스트

블로그에 오신 것을 환영합니다! 한국어 버전입니다.

## 소개

한국어로 된 내용...

```

**Mongolian Version** (`2025-11-18-my-first-post.mn.md`):
```markdown
---
layout: post
title: "Миний анхны блог пост"
date: 2025-11-18 10:00:00 -0500
lang: mn
post_id: 2025-11-18-my-first-post
excerpt: "Энэ бол миний анхны гурван хэл дээрх блог пост юм."
---

# Миний анхны блог пост

Блогт тавтай морил! Энэ бол монгол хувилбар.

## Танилцуулга

Монгол хэл дээрх контент...

```

### Step 4: Critical Checklist

Before proceeding, verify:

- [ ] All three files have **IDENTICAL** `date` values (including time and timezone)
- [ ] All three files have **IDENTICAL** `post_id` values
- [ ] Each file has a **DIFFERENT** `lang` value (`en`, `ko`, `mn`)
- [ ] The `post_id` is **UNIQUE** - not used by any other post
- [ ] Filenames use ASCII characters only (no Korean, Mongolian, or special chars)
- [ ] `layout: post` is set in all three files

### Step 5: Test Locally

Run Jekyll locally to preview your post:

```bash
cd /path/to/your/repo
jekyll serve
```

Then open your browser:
- Main site: `http://localhost:4000`
- Blog index: `http://localhost:4000/blog/`
- Your post (English): `http://localhost:4000/blog/2025/11/18/my-first-post.en.html`

**What to Check**:
1. ✅ All three posts appear in blog index
2. ✅ Post title, date, and excerpt display correctly
3. ✅ Clicking post title opens the full post
4. ✅ Language switcher shows EN, KO, MN links
5. ✅ Clicking language link switches to that version
6. ✅ Markdown formatting renders correctly (headings, lists, links, images)

### Step 6: Commit and Push

Once testing looks good:

```bash
git add _posts/2025-11-18-my-first-post.en.md
git add _posts/2025-11-18-my-first-post.ko.md
git add _posts/2025-11-18-my-first-post.mn.md
git commit -m "Add new blog post: My First Post (EN/KO/MN)"
git push origin master
```

GitHub Pages will automatically build and deploy within ~2-5 minutes.

### Step 7: Verify Production

After GitHub Pages finishes building:

1. Visit your live site: `https://yourusername.github.io/blog/`
2. Verify the new post appears in the blog index
3. Click through all three language versions
4. Test language switcher

---

## Optional: Adding Only 1 or 2 Languages

You don't have to create all three languages. Partial sets are allowed.

**Example**: English and Korean only (no Mongolian)
```
_posts/2025-11-18-partial-post.en.md
_posts/2025-11-18-partial-post.ko.md
```

**What happens**:
- Blog index: Shows English version
- Language switcher: Shows only EN and KO (MN hidden)

**Note**: The system gracefully handles missing translations!

---

## Common Mistakes & Troubleshooting

### Mistake 1: Different `date` values

**Problem**: English post has `date: 2025-11-18`, Korean has `date: 2025-11-19`

**Symptom**: Posts appear separately in blog index

**Fix**: Make all `date` fields identical (including time and timezone)

### Mistake 2: Typo in `post_id`

**Problem**: English has `post_id: 2025-11-18-first-post`, Korean has `post_id: 2025-11-18-frist-post` (typo)

**Symptom**: Language switcher doesn't link them together

**Fix**: Copy-paste `post_id` to avoid typos

### Mistake 3: Reusing `post_id` from different post

**Problem**: Two different posts use `post_id: 2025-11-18-test`

**Symptom**: Language switcher shows links to unrelated posts

**Fix**: Choose a unique `post_id` (check existing files)

### Mistake 4: Non-ASCII filename

**Problem**: Filename is `_posts/2025-11-18-한글제목.ko.md`

**Symptom**: May cause URL encoding issues on GitHub Pages

**Fix**: Use ASCII slugs in filenames; put localized titles in `title` field

### Mistake 5: Forgot `lang` field

**Problem**: Front matter missing `lang: ko`

**Symptom**: Language switcher doesn't know which language the post is

**Fix**: Add `lang` field with value `en`, `ko`, or `mn`

---

## `post_id` Naming Convention (Best Practices)

### Recommended Format

`YYYY-MM-DD-descriptive-slug`

**Examples**:
- `2025-11-18-welcome-to-my-blog`
- `2025-12-01-ai-research-findings`
- `2026-01-15-kaist-graduation-reflection`

### Why This Format?

1. **Date prefix prevents duplicates** - unlikely two posts on same date have same slug
2. **Chronological sorting** - easy to see post dates at a glance
3. **Consistency** - matches Jekyll's filename convention
4. **Readability** - clear what the post is about

### What to Avoid

- ❌ Generic slugs: `post-1`, `test`, `new-post`
- ❌ Too short: `ai`, `blog`
- ❌ Too long: `this-is-my-very-long-post-title-about-artificial-intelligence-and-machine-learning`
- ❌ Special characters: `post!@#`, `데이터-분석` (use ASCII only)

---

## Advanced: Adding Images

### Step 1: Add image to assets

```
assets/blog/images/2025-11-18-my-image.jpg
```

### Step 2: Reference in markdown

All three language versions use the **same image path**:

```markdown
![Description](/assets/blog/images/2025-11-18-my-image.jpg)
```

**Localization**: If you need different images per language, use language suffix:

```markdown
<!-- In .en.md -->
![Description](/assets/blog/images/2025-11-18-diagram.en.jpg)

<!-- In .ko.md -->
![설명](/assets/blog/images/2025-11-18-diagram.ko.jpg)
```

---

## Advanced: Custom Excerpts

By default, Jekyll uses the first ~150 characters as excerpt.

To set a custom excerpt, add the `excerpt` field in front matter:

```yaml
excerpt: "This is my custom excerpt. It will appear on the blog index page."
```

**Or** use the `excerpt_separator`:

```yaml
excerpt_separator: <!--more-->
```

Then in your content:

```markdown
This will be the excerpt.

<!--more-->

This will NOT be in the excerpt.
```

---

## Summary Checklist

Creating a new post:

1. [ ] Choose unique `post_id` in format `YYYY-MM-DD-slug`
2. [ ] Create 3 markdown files in `_posts/` with `.en`, `.ko`, `.mn` suffixes
3. [ ] Add front matter to each file with IDENTICAL `date` and `post_id`
4. [ ] Set unique `lang` field in each file (`en`, `ko`, `mn`)
5. [ ] Write content in each language
6. [ ] Test locally with `jekyll serve`
7. [ ] Verify blog index shows post (in English if available)
8. [ ] Verify language switcher works on each version
9. [ ] Commit and push to master
10. [ ] Verify on production site after GitHub Pages build

**Estimated Time**: 10-15 minutes per post (after first post)

**Questions?** Refer to [data-model.md](data-model.md) for detailed schema documentation.
