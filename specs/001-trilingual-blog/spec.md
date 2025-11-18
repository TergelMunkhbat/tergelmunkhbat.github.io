# Feature Specification: Trilingual Blog Section

**Feature Branch**: `001-trilingual-blog`  
**Created**: 2025-11-18  
**Status**: Draft  
**Input**: User description: "Add a blog section to this repo. Must support English, Korean, and Mongolian versions for each post, ideally by uploading a Markdown file."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Create New Trilingual Post (Priority: P1)

As the site owner, I want to write a new blog post and provide versions in English, Korean, and Mongolian. I want to do this simply by creating three Markdown files in a folder, linking them together, and pushing them to my GitHub repository.

**Why this priority**: This is the foundation - without the ability to create posts, there's nothing to display. The blog functionality depends entirely on this capability.

**Independent Test**: View locally with `jekyll serve`, create a test post with EN/KO/MN markdown files in the appropriate directory structure, verify the post appears in the blog listing and all three language versions render correctly.

**Acceptance Scenarios**:

1. **Given** I am the site owner, **When** I create three markdown files in the blog posts directory with proper YAML frontmatter (including a shared `post_id: 'my-first-post'` and a unique `lang: en` in each), **Then** all three versions are recognized by Jekyll and accessible via their respective URLs

2. **Given** I have created a new post with all three language versions, **Then** each file contains the same `post_id` value and different `lang` values (en, ko, mn)

3. **Given** I have created a new post with all three language versions, **When** I push the changes to the master branch, **Then** GitHub Pages automatically builds and deploys the new post within a few minutes

4. **Given** I am creating a blog post, **When** I include standard markdown elements (headings, lists, links, images, code blocks), **Then** all formatting renders correctly in all three language versions

---

### User Story 2 - Browse All Posts (Priority: P2)

As a site visitor, I navigate to the new "Blog" section. I see a clean, reverse-chronological list of all blog posts, with the title, date, and a short excerpt for each.

**Why this priority**: Once posts can be created, visitors need to discover them. A browseable index is essential for blog usability and comes before language switching.

**Independent Test**: View locally with `jekyll serve`, navigate to the blog section URL, verify posts appear in reverse chronological order (newest first), check that title, date, and excerpt display correctly for each post.

**Acceptance Scenarios**:

1. **Given** multiple blog posts exist, **When** I navigate to the blog landing page, **Then** I see all posts listed in reverse chronological order (newest first)

2. **Given** I am viewing the blog listing, **When** I look at each post entry, **Then** I see the post title, publication date, and a brief excerpt (first 150-200 characters or custom excerpt from frontmatter)

3. **Given** I am viewing the blog listing, **When** I click on a post title or "Read more" link, **Then** I am taken to the full post page

4. **Given** I am on the blog listing page, **When** I view the page, **Then** I see posts displayed in this language priority order: English, then Korean, then Mongolian (en → ko → mn), with a clear indication of the current viewing language

---

### User Story 3 - Read and Switch Languages (Priority: P3)

As a site visitor, I am reading a blog post in English. I see that a Korean version is available, so I click the "Korean" link and am taken directly to the Korean translation of that same post.

**Why this priority**: While important for trilingual support, language switching is only useful after posts exist and are browseable. Users can still read posts in their default language without this feature.

**Independent Test**: View locally with `jekyll serve`, open a blog post in English, verify language switcher shows available translations (KO, MN), click Korean link, verify the Korean version of the same post loads correctly.

**Acceptance Scenarios**:

1. **Given** I am reading a blog post in English, **When** I look at the page, **Then** I see a language switcher indicating that Korean (한국어) and Mongolian (Монгол) versions are available

2. **Given** I am viewing the English version of a post, **When** I click the Korean language link, **Then** I am taken to the Korean version of the exact same post (same URL structure, different language code)

3. **Given** I am viewing a post in any language, **When** I click a different language link, **Then** the new page loads at the top of the article

4. **Given** a blog post only has English and Korean versions (Mongolian missing), **When** I view the language switcher, **Then** only English and Korean options are shown/active (the switcher dynamically checks which versions exist for that `post_id`)

**Note**: [DEFERRED POST-MVP] Maintaining scroll position when switching languages requires JavaScript complexity and will be considered for a future release.

---

### Edge Cases

- What happens when a post is missing one or two language versions (e.g., only English exists)? (Language switcher shows only available languages; blog index uses priority order: en → ko → mn)
- How does the system handle posts with different publication dates across languages?
- What if the markdown file has malformed YAML frontmatter?
- How are special characters in filenames (non-ASCII) handled for Korean/Mongolian titles?
- What happens if excerpt is too long or contains markdown formatting?
- How are images referenced in posts handled across different language versions?
- What happens if two different posts accidentally use the same `post_id`? (Build must fail with clear error)

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: System MUST support creating blog posts as Markdown files in a designated directory
- **FR-002**: System MUST recognize three language versions by using a unique `post_id` (e.g., `my-first-post`) in the YAML front matter of each related post file
- **FR-003**: System MUST generate a blog index page listing all posts in reverse chronological order, displaying each post in this language priority: English (en) first, then Korean (ko), then Mongolian (mn)
- **FR-004**: System MUST display post title, publication date, and excerpt on the blog index page
- **FR-005**: System MUST render full blog post content with markdown formatting (headings, lists, links, images, code blocks)
- **FR-006**: System MUST provide language switcher UI on each blog post showing available translations (dynamically determined by checking which files share the same `post_id`)
- **FR-007**: System MUST link versions by cross-referencing the `post_id`. The language switcher will query all posts to find others sharing the same `post_id`
- **FR-008**: System MUST support YAML frontmatter in markdown files for metadata including: `title`, `date`, `excerpt`, `lang` (en/ko/mn), and `post_id` (shared identifier for translations)
- **FR-009**: System MUST integrate blog section into existing site navigation
- **FR-010**: System MUST maintain existing site styling and layout conventions (Skeleton CSS framework)
- **FR-011**: System MUST validate `post_id` uniqueness during build and fail with clear error message if duplicate `post_id` values are detected across different posts

### Key Entities

- **Blog Post**: Represents a single article with metadata (title, date, excerpt, language, post_id) and markdown content. One post can have up to 3 language versions (EN, KO, MN). Each language version is a separate markdown file but all share the same `post_id` value to link them together.

- **Language Version**: A specific translation of a blog post. Contains language code (`lang`: en/ko/mn), shared `post_id` identifier, and the translated content.

- **Blog Index**: The landing page listing all blog posts. Shows one entry per post using the English version if available, otherwise Korean, otherwise Mongolian (alphabetical fallback: en → ko → mn) with title, date, and excerpt.

## Assumptions & Risks *(mandatory)*

### Assumptions

- **A-001**: The blog will be built using Jekyll and hosted on GitHub Pages, reusing the existing site's structure
- **A-002**: All 3 language versions of a post will typically be created and pushed at the same time (though system must handle partial sets gracefully)
- **A-003**: Image and asset paths (e.g., `/assets/blog/my-image.png`) will be identical for all language versions of a post
- **A-004**: Post filenames will use ASCII characters only; localized titles will be stored in YAML front matter

### Risks & Mitigations

- **R-001**: Filenames with non-ASCII characters (Korean, Mongolian) may cause issues with GitHub Pages URLs or builds
  - **Mitigation**: All filenames will be kept in ASCII (e.g., `2025-11-18-my-first-post.ko.md`). The post's title in any language will be set in the YAML front matter `title` field, not the filename

- **R-002**: A post may be missing one or two language versions, breaking the language switcher
  - **Mitigation**: The language switcher UI (FR-006) MUST be dynamic. It must check which versions exist for that `post_id` and only render links for languages that are present

- **R-003**: Posts with identical `post_id` but different publication dates could cause confusion in chronological ordering
  - **Mitigation**: All language versions of a post should use the same `date` value in front matter to ensure consistent ordering across language-specific blog indexes

- **R-004**: Two different posts could accidentally use the same `post_id`, causing incorrect language linking
  - **Mitigation**: Build process MUST validate `post_id` uniqueness (FR-011) and fail with clear error message identifying the duplicate, preventing deployment of invalid configuration

## Clarifications

### Session 2025-11-18

- Q: When displaying the blog index (listing page), which language should be the default for blog index entries? → A: English is always the default - index shows English version of each post (if available)
- Q: What should happen if two different posts accidentally use the same `post_id`? → A: Build fails with clear error message identifying the duplicate `post_id`
- Q: How should "first available language" be determined when English is missing? → A: Alphabetical by language code - Korean (ko) first, then Mongolian (mn) if ko also missing

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Site owner can create a new trilingual blog post by adding 3 markdown files and pushing to GitHub, with the post appearing live within 5 minutes of push
- **SC-002**: Visitors can navigate to the blog section and see all posts listed in reverse chronological order within 2 seconds of page load
- **SC-003**: Visitors can switch between language versions of a post with a single click, with the page loading in under 2 seconds
- **SC-004**: All markdown formatting (headings, lists, links, images, code) renders correctly across all three languages
- **SC-005**: Blog section integrates seamlessly with existing site navigation and maintains consistent styling
