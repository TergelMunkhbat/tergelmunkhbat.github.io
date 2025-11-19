# Quickstart Guide: Navigation & Layout Redesign

**Feature**: 002-nav-layout-redesign | **Date**: 2025-11-19

## Prerequisites

Before starting implementation, ensure you have:

- [x] Ruby installed (for Jekyll)
- [x] Jekyll gem installed (`gem install jekyll bundler`)
- [x] Git repository cloned locally
- [x] Familiarity with Liquid templating syntax
- [x] Basic HTML/CSS knowledge
- [x] Text editor or IDE

## Development Workflow

### 1. Start Local Development Server

```bash
# Navigate to repository root
cd /path/to/tergelmunkhbat.github.io

# Start Jekyll with live reload
jekyll serve --livereload

# Server will start at http://localhost:4000
# Live reload will automatically refresh browser on file changes
```

**Tip**: Keep this terminal running throughout development. Changes to HTML/CSS/Liquid templates will auto-refresh the browser.

### 2. Implementation Order

Follow this sequence to minimize breaking changes:

#### Phase A: Create Reusable Components (No Visual Changes Yet)

1. **Extract Bio Section to Include**
   - Create `_includes/bio-section.html`
   - Copy existing bio HTML from current layout
   - Parameterize with `include.variant` for styling variants
   - Test: Homepage should look identical after this change

2. **Create Slim Navbar Component**
   - Create `_includes/slim-navbar.html`
   - Implement basic structure (name/logo + menu links)
   - Don't activate yet - just create the file
   - Add CSS in `libs/custom/my_css.css` (initially hidden)

#### Phase B: Create Blog Index Page

3. **Create Blog Listing Page**
   - Create `blog/index.html`
   - Use Jekyll's `site.posts` loop
   - Test: Navigate to `/blog/` and verify posts appear
   - Verify it uses correct layout (likely `layout: default` initially)

#### Phase C: Implement Context-Aware Headers

4. **Add Conditional Header Logic**
   - Modify `_layouts/default.html` or `_layouts/post.html`
   - Add Liquid conditional: `{ % if page.layout == 'post' % }` (remove spaces)
   - Include slim-navbar for posts, hero header for others
   - Test: Homepage keeps hero header, articles show slim navbar

5. **Add Footer Bio to Article Pages**
   - Modify `_layouts/post.html`
   - Include bio-section at bottom with `variant="footer"`
   - Test: Scroll to bottom of article to verify bio appears

#### Phase D: Add Responsive Behavior

6. **Implement Mobile Hamburger Menu**
   - Add checkbox hack HTML to `_includes/slim-navbar.html`
   - Add mobile-responsive CSS with media queries
   - Test: Resize browser to <768px width to verify hamburger appears

7. **Final Polish**
   - Add sticky positioning CSS to navbar
   - Fine-tune spacing, padding, colors
   - Test all breakpoints and page types

### 3. Testing Checklist

After each implementation phase, verify:

#### Homepage Tests
```bash
# Navigate to http://localhost:4000/
```
- [ ] Large hero header visible
- [ ] Photo, name, title, social icons display correctly
- [ ] No navbar or footer bio section
- [ ] Layout unchanged from original

#### Article Page Tests
```bash
# Navigate to any post URL (e.g., http://localhost:4000/blog/2024/11/18/god-led-life.html)
```
- [ ] Slim navbar at top (max 100px height)
- [ ] Navbar shows name/logo on left, menu links on right
- [ ] Navbar stays visible when scrolling (sticky)
- [ ] No hero header
- [ ] Bio section appears at bottom of article
- [ ] Bio section contains photo, name, title, social icons

#### Blog Index Tests
```bash
# Navigate to http://localhost:4000/blog/
```
- [ ] Page loads successfully
- [ ] All blog posts listed with title, date, excerpt
- [ ] Post titles are clickable links
- [ ] Links navigate to correct post pages

#### Mobile Responsive Tests
```bash
# Resize browser to mobile width (<768px) or use DevTools device emulation
```
- [ ] Hamburger menu icon appears in navbar
- [ ] Menu links hidden by default
- [ ] Clicking hamburger expands menu
- [ ] Clicking again collapses menu
- [ ] Name/logo remains visible

#### JavaScript Disabled Tests
```bash
# Disable JavaScript in browser settings
```
- [ ] Navbar sticky positioning still works (CSS-only)
- [ ] Navigation links are clickable
- [ ] Hamburger menu still functional (checkbox hack)

#### Cross-Browser Tests
- [ ] Chrome/Edge (modern)
- [ ] Safari (webkit-sticky prefix)
- [ ] Firefox
- [ ] Mobile browsers (iOS Safari, Chrome Mobile)

### 4. Key Files Reference

#### Files to Create
```
_includes/
├── slim-navbar.html       # New: Slim navbar component
└── bio-section.html       # New: Reusable bio component (or extract from existing)

blog/
└── index.html             # New: Blog listing page
```

#### Files to Modify
```
_layouts/
├── default.html           # Modify: Add conditional header logic (if hero header lives here)
└── post.html              # Modify: Include slim navbar + footer bio

libs/custom/
└── my_css.css             # Modify: Add navbar styles, responsive rules
```

#### Files to Reference (Read-Only)
```
_data/
└── main_info.yaml         # Read: Bio data for header/footer

_posts/
└── *.md                   # Read: Blog posts for index page
```

### 5. CSS Development Tips

#### Recommended CSS Structure in `libs/custom/my_css.css`

```css
/* ==========================================================================
   Navigation & Layout Redesign (002-nav-layout-redesign)
   ========================================================================== */

/* --- Slim Navbar (Article Pages) --- */
.slim-navbar {
  /* Sticky positioning */
  /* Layout (flexbox) */
  /* Sizing (max-height: 100px) */
  /* Colors, borders, shadows */
}

.slim-navbar .brand { /* Name/logo styles */ }
.slim-navbar .nav-menu { /* Menu container */ }
.slim-navbar .nav-menu a { /* Menu link styles */ }

/* --- Mobile Hamburger Menu --- */
.nav-toggle { /* Hidden checkbox */ }
.nav-toggle-label { /* Hamburger icon */ }

@media (max-width: 767px) {
  /* Mobile: hide menu, show hamburger */
  /* Toggle behavior via :checked pseudo-class */
}

@media (min-width: 768px) {
  /* Desktop: show menu, hide hamburger */
}

/* --- Bio Section Variants --- */
.bio-section { /* Shared bio styles */ }
.bio-hero { /* Hero header specific styles */ }
.bio-footer { /* Footer specific styles */ }

/* ==========================================================================
   End Navigation & Layout Redesign
   ========================================================================== */
```

**Tip**: Use CSS comments to mark feature sections for easy maintenance.

### 6. Debugging Common Issues

#### Issue: Navbar not sticky
**Solution**: Check browser support for `position: sticky`. Add `-webkit-sticky` prefix for Safari.

#### Issue: Hamburger menu not toggling
**Solution**: Verify checkbox ID matches label `for` attribute. Ensure `:checked` CSS selector is correct.

#### Issue: Bio section not appearing in footer
**Solution**: Check `_layouts/post.html` includes bio-section. Verify `site.data.main_info` is accessible.

#### Issue: Blog index page shows 404
**Solution**: Ensure `blog/index.html` exists (not just `blog.html`). Check front matter has `layout` specified.

#### Issue: Changes not appearing in browser
**Solution**: Hard refresh (Cmd+Shift+R or Ctrl+Shift+R). Check Jekyll terminal for build errors.

### 7. Git Workflow

```bash
# All work happens on feature branch (already checked out)
git branch  # Verify you're on 002-nav-layout-redesign

# Stage and commit incrementally
git add _includes/bio-section.html
git commit -m "feat(nav): extract bio section to reusable include"

git add _includes/slim-navbar.html libs/custom/my_css.css
git commit -m "feat(nav): add slim navbar component with mobile responsive design"

git add blog/index.html
git commit -m "feat(nav): create blog index page"

git add _layouts/post.html _layouts/default.html
git commit -m "feat(nav): implement context-aware headers (hero vs navbar)"

# When feature complete, push to remote
git push -u origin 002-nav-layout-redesign

# Create PR for review (or merge directly if personal project)
```

### 8. Performance Validation

Before considering feature complete:

```bash
# 1. Check page load time
# Open browser DevTools > Network tab
# Hard refresh homepage and article page
# Verify < 2s load time (per constitution constraints)

# 2. Lighthouse audit (optional but recommended)
# Chrome DevTools > Lighthouse tab
# Run audit on homepage and article page
# Check Performance, Accessibility, Best Practices scores

# 3. Mobile performance
# Use DevTools device emulation
# Enable CPU throttling (4x slowdown)
# Verify navbar remains responsive
```

### 9. Accessibility Checks

```bash
# 1. Keyboard navigation
# Tab through navbar links - verify all focusable
# Space bar toggles hamburger menu (checkbox)
# Enter key activates links

# 2. Screen reader testing (optional)
# macOS: Enable VoiceOver (Cmd+F5)
# Windows: Enable Narrator
# Verify navbar, menu items, bio section are announced correctly

# 3. Color contrast
# Use browser extension (e.g., WAVE, axe DevTools)
# Verify navbar text meets WCAG AA standards (4.5:1 ratio)
```

### 10. Ready for Review

Feature is ready for review/merge when:

- [x] All acceptance scenarios from spec.md pass
- [x] All success criteria measurable
- [x] Homepage unchanged (hero header preserved)
- [x] Article pages show slim navbar + footer bio
- [x] Blog index page functional
- [x] Mobile hamburger menu works
- [x] No JavaScript errors in console
- [x] No Jekyll build warnings
- [x] Page load times < 2s
- [x] Git commits clean and descriptive

## Quick Commands Reference

```bash
# Start dev server
jekyll serve --livereload

# Build without serving (debugging)
jekyll build

# Check Jekyll version
jekyll --version

# Clear Jekyll cache (if weird issues)
jekyll clean

# Git status
git status

# View local site
open http://localhost:4000  # macOS
start http://localhost:4000 # Windows
```

## Need Help?

- **Jekyll Docs**: https://jekyllrb.com/docs/
- **Liquid Syntax**: https://shopify.github.io/liquid/
- **Skeleton CSS**: http://getskeleton.com/
- **Spec Reference**: [spec.md](./spec.md)
- **Research Decisions**: [research.md](./research.md)
- **Data Model**: [data-model.md](./data-model.md)

## Next Steps

After completing implementation:
1. Run through testing checklist above
2. Commit all changes with descriptive messages
3. Push feature branch to remote
4. Deploy to production (merge to master → GitHub Pages auto-deploy)
5. Verify on live site
6. Mark feature as complete in project tracker
