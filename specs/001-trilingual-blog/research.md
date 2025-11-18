# Research: Trilingual Blog Implementation

**Feature**: 001-trilingual-blog  
**Date**: 2025-11-18  
**Purpose**: Resolve technical unknowns for implementing trilingual blog with Jekyll on GitHub Pages

## Research Questions

### 1. Jekyll Collections for Blog Posts

**Decision**: Use Jekyll's built-in `posts` collection (default `_posts/` directory)

**Rationale**:
- Jekyll has native support for blog posts via the `_posts/` collection
- Automatic date-based sorting (reverse chronological)
- No additional configuration needed in `_config.yml`
- GitHub Pages fully supports the posts collection
- Standard permalink structure: `/blog/:year/:month/:day/:title`

**Alternatives Considered**:
- Custom collection (e.g., `collections: [blog_posts]`) - Rejected: Unnecessary complexity, posts collection does everything needed
- Pages with custom front matter - Rejected: No automatic sorting or blog-specific features

**Implementation Notes**:
- Post filenames follow Jekyll convention: `YYYY-MM-DD-slug.md` (e.g., `2025-11-18-my-first-post.en.md`)
- Language suffix in slug (`.en`, `.ko`, `.mn`) keeps posts organized while maintaining ASCII filenames
- Posts automatically available in `site.posts` Liquid variable

---

### 2. Cross-Referencing Posts by `post_id`

**Decision**: Use Liquid filters to find related posts by matching `post_id` in front matter

**Rationale**:
- GitHub Pages Jekyll doesn't allow custom Ruby plugins
- Liquid templating has `where` filter: `site.posts | where: "post_id", page.post_id`
- This provides dynamic runtime linking without build-time processing
- No plugin required - pure Liquid logic

**Example Liquid Code**:
```liquid
{% assign translations = site.posts | where: "post_id", page.post_id %}
{% for translation in translations %}
  {% if translation.lang != page.lang %}
    <a href="{{ translation.url }}">{{ translation.lang | upcase }}</a>
  {% endif %}
{% endfor %}
```

**Alternatives Considered**:
- Jekyll plugin for validation - Rejected: GitHub Pages doesn't support custom plugins
- Data files mapping - Rejected: Requires manual maintenance, defeats purpose of `post_id`
- Filename parsing - Rejected: Less explicit than front matter, harder to debug

**Limitations**:
- No build-time validation of `post_id` uniqueness possible with GitHub Pages
- Must rely on testing/QA to catch duplicate `post_id` values
- **Mitigation**: Document `post_id` naming convention clearly in quickstart guide

---

### 3. Language Priority Fallback Logic (en → ko → mn)

**Decision**: Implement priority fallback using Liquid conditionals with `where` filter

**Rationale**:
- Blog index needs to show one entry per `post_id`, preferring English
- Liquid supports chaining `where` filters and conditional logic
- Can check existence of English version, fallback to Korean, then Mongolian

**Example Liquid Code for Blog Index**:
```liquid
{% assign unique_posts = site.posts | group_by: "post_id" %}
{% for group in unique_posts %}
  {% assign en_post = group.items | where: "lang", "en" | first %}
  {% assign ko_post = group.items | where: "lang", "ko" | first %}
  {% assign mn_post = group.items | where: "lang", "mn" | first %}
  
  {% assign display_post = en_post | default: ko_post | default: mn_post %}
  
  <!-- Display display_post title, excerpt, date -->
{% endfor %}
```

**Alternatives Considered**:
- JavaScript client-side filtering - Rejected: Violates Simplicity First principle, adds complexity
- Separate index pages per language - Rejected: User wants single unified blog index
- Always require English version - Rejected: Spec allows missing translations

**Performance**:
- Grouping and filtering happens once during Jekyll build
- No runtime performance impact on visitors
- Acceptable for expected scale (10-50 posts/year)

---

### 4. `post_id` Uniqueness Validation

**Decision**: Manual validation only (no automated build-time check possible on GitHub Pages)

**Note**: See spec.md FR-011 for the normative requirement. Duplicate validation is handled through documentation and manual testing.

**Rationale**:
- GitHub Pages Jekyll doesn't support custom plugins for build-time validation
- Jekyll doesn't have built-in uniqueness checking for arbitrary front matter fields
- Travis CI or GitHub Actions could add validation, but violates Simplicity First principle

**Mitigation Strategy**:
1. **Documentation**: Quickstart guide will emphasize `post_id` uniqueness requirement
2. **Naming Convention**: Recommend date-based `post_id` format: `YYYY-MM-DD-title` (e.g., `2025-11-18-my-first-post`)
3. **Testing Checklist**: Manual verification step when creating new posts
4. **Visual QA**: Language switcher will show incorrect links if duplicates exist (discoverable during testing)

**Alternatives Considered**:
- GitHub Actions workflow for validation - Rejected: Adds build complexity, violates constitution
- Pre-commit hook - Rejected: Not enforceable for all contributors
- Custom Jekyll plugin - Rejected: GitHub Pages incompatible

**Risk Acceptance**:
- Spec requires build failure on duplicates (FR-011), but this is not technically achievable on GitHub Pages
- Must update spec to reflect this limitation or accept manual validation
- **Recommendation**: Update FR-011 to state "SHOULD validate" instead of "MUST validate" for GitHub Pages compatibility

---

### 5. Language Switcher UI Patterns

**Decision**: Dynamic language switcher using Liquid include with conditional rendering

**Rationale**:
- Include file (`_includes/language_switcher.html`) can be reused across post layouts
- Liquid filters find available translations for current `post_id`
- Conditionally render only languages that exist
- Uses existing Skeleton CSS for styling (buttons/links)

**Example Structure**:
```liquid
<!-- _includes/language_switcher.html -->
<div class="language-switcher">
  {% assign translations = site.posts | where: "post_id", page.post_id %}
  {% for translation in translations %}
    {% if translation.lang == page.lang %}
      <span class="active">{{ translation.lang | upcase }}</span>
    {% else %}
      <a href="{{ translation.url }}">{{ translation.lang | upcase }}</a>
    {% endif %}
  {% endfor %}
</div>
```

**Display Labels**:
- English: EN or English
- Korean: KO or 한국어
- Mongolian: MN or Монгол

**Positioning**: Top-right of post content area (consistent with typical language switcher placement)

**Alternatives Considered**:
- Flags instead of text - Rejected: Accessibility concerns, flags don't always map to languages
- Dropdown menu - Rejected: Overkill for only 3 languages, less discoverable
- Footer placement - Rejected: Less visible, users expect language switchers in header area

---

## Technology Decisions Summary

| Component | Technology | GitHub Pages Compatible |
|-----------|------------|------------------------|
| Blog Posts | Jekyll `_posts/` collection | ✅ Yes |
| Front Matter | YAML (title, date, excerpt, lang, post_id) | ✅ Yes |
| Cross-referencing | Liquid `where` filter | ✅ Yes |
| Language Priority | Liquid conditionals with `default` filter | ✅ Yes |
| Uniqueness Validation | Manual (documentation + testing) | ⚠️ Limitation |
| Language Switcher | Liquid include with dynamic rendering | ✅ Yes |
| Styling | Existing Skeleton CSS | ✅ Yes |
| Navigation Integration | Update `index.html` or header include | ✅ Yes |

---

## Implementation Readiness

✅ All core technical questions resolved  
✅ Solutions compatible with GitHub Pages  
⚠️ One limitation identified: `post_id` uniqueness validation must be manual  
✅ Ready to proceed to Phase 1 (Data Model & Design)

**Next Steps**:
1. Create data-model.md defining front matter schema
2. Create quickstart.md with `post_id` naming conventions and testing checklist
3. Update FR-011 in spec to reflect manual validation limitation (or accept risk)
