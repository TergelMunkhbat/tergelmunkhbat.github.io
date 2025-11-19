# Feature Specification: Navigation & Layout Redesign

**Feature Branch**: `002-nav-layout-redesign`
**Created**: 2025-11-19
**Status**: Draft
**Input**: User description: "Navigation & Layout Redesign\
Goal: Improve readability on blog pages and introduce consistent site-wide navigation. Input: Current header is distracting on article pages; need to add menus.\
The goal is to change the header depending on the page type:Page TypeHeader/LayoutItems to DisplayHome Page (Index)Large Hero HeaderFull Photo, Name, Title, Social Icons (Current look)Article Page, Slim Navbar (Always Visible)Left: Name/Logo. Right: Menu Links.Article FooterBio SectionFull Photo, Name, Title, Social Icons (Moved from header)\
The Menu Architecture
We will create two core menu items - Home and Blog\
\
Implement Slim Navbar on Article Pages\
The large bio header must be replaced with a minimalist navigation bar that sticks to the top of the screen (sticky/fixed) on all article pages.\
\
Relocate Bio Section to Article Footer\
The detailed biographical information must be moved from the top of the article to the bottom, appearing only after the reader finishes the content.\
\
Create Menu Structure\
The navigation links must point to the correct sections of your site."

## Clarifications

### Session 2025-11-19

- Q: Where should the "Blog" menu link navigate to? → A: Navigate to `/blog/index.html` - a blog section with its own index
- Q: How should the slim navbar menu items behave on mobile screens? → A: Collapse menu links into a hamburger menu icon that expands on tap
- Q: What is the maximum height constraint for the slim navbar on article pages? → A: Maximum 100px height - provides comfortable padding with readable branding (definitive upper limit)

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Article Reader Focused Reading Experience (Priority: P1)

As a blog visitor reading an article, I want the header to be minimal and unobtrusive so I can focus on the content without visual distractions from the author's bio information.

**Why this priority**: This is the core value proposition of the redesign - improving content readability. Articles are the primary content type where users spend the most time, and reducing header distraction directly improves the reading experience.

**Independent Test**: View any blog article page locally with `jekyll serve`, verify that the large bio header is replaced with a slim navigation bar at the top, and the full bio section appears at the bottom of the article after the content. Deploy and verify on production.

**Acceptance Scenarios**:

1. **Given** I am on any blog article page, **When** the page loads, **Then** I see a slim navigation bar at the top instead of the large bio header
2. **Given** I am reading an article, **When** I scroll down the page, **Then** the slim navigation bar remains visible (sticky/fixed to the top)
3. **Given** I finish reading an article and scroll to the bottom, **When** I reach the end of the content, **Then** I see the full bio section with photo, name, title, and social icons
4. **Given** I am on an article page, **When** I view the navigation bar, **Then** I see the name/logo on the left and menu links on the right

---

### User Story 2 - Homepage Visual Impact Preservation (Priority: P2)

As a first-time visitor landing on the homepage, I want to see the full biographical header with photo and social icons so I can immediately understand who the author is and connect with them.

**Why this priority**: The homepage serves as the landing page and introduction to the author. Maintaining the large hero header here preserves the personal brand and first impression while the article pages get the streamlined treatment.

**Independent Test**: View the homepage (index) locally with `jekyll serve`, verify that the large hero header with full photo, name, title, and social icons is still displayed as before. Deploy and verify on production.

**Acceptance Scenarios**:

1. **Given** I am on the homepage, **When** the page loads, **Then** I see the large hero header with full photo, name, title, and social icons
2. **Given** I am on the homepage, **When** I compare it to the previous version, **Then** the header appearance and layout remain unchanged

---

### User Story 3 - Easy Site Navigation (Priority: P3)

As a visitor on any page, I want to access navigation links to Home and Blog sections so I can easily move between different parts of the site.

**Why this priority**: Navigation is important for usability but is a supporting feature. The primary value is in the layout changes (P1, P2). Navigation enables discovery but doesn't directly improve the core reading experience.

**Independent Test**: View any page with the slim navbar locally with `jekyll serve`, click on "Home" link and verify it navigates to the homepage, click on "Blog" link and verify it navigates to the blog section. Deploy and verify on production.

**Acceptance Scenarios**:

1. **Given** I am on an article page, **When** I click the "Home" menu link in the navbar, **Then** I am navigated to the homepage
2. **Given** I am on any page, **When** I click the "Blog" menu link in the navbar, **Then** I am navigated to `/blog/index.html` (the blog index page)
3. **Given** I am on an article page, **When** I click the name/logo in the navbar, **Then** I am navigated to the homepage

---

### Edge Cases

- What happens when viewing on mobile devices with narrow screens? The menu links collapse into a hamburger menu icon. When tapped, the hamburger menu expands to show the navigation options (Home, Blog). The name/logo remains visible on the left.
- What happens when JavaScript is disabled? The sticky/fixed positioning should still work using CSS alone, and navigation links should remain functional as standard HTML links.
- What happens when the article content is very short (less than one screen height)? The footer bio section should still appear below the content, even if visible without scrolling.
- What happens when the user has custom browser zoom settings? The navbar and footer bio should scale appropriately and remain readable.
- What happens when the blog has zero posts? The blog index page (`/blog/index.html`) should handle an empty `site.posts` collection gracefully by displaying a friendly message (e.g., "No posts yet") instead of showing a broken or empty list.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST display different header layouts based on page type: large hero header on homepage, slim navbar on article pages
- **FR-002**: The slim navbar MUST remain visible at the top of the screen when users scroll on article pages (sticky/fixed positioning)
- **FR-003**: The slim navbar MUST display the name/logo on the left side and menu links (Home, Blog) on the right side
- **FR-004**: Article pages MUST display the bio section (with photo, name, title, and social icons) at the bottom of the article content, after the main text
- **FR-005**: The homepage MUST continue to display the large hero header with full photo, name, title, and social icons in its current format
- **FR-006**: The "Home" menu link MUST navigate users to the homepage/index
- **FR-007**: The "Blog" menu link MUST navigate users to `/blog/index.html` (a dedicated blog index page listing all blog posts)
- **FR-008**: The name/logo in the slim navbar MUST function as a clickable link that navigates to the homepage
- **FR-009**: The bio section in the article footer MUST contain the same information as the current header: photo, name, title, and social icons
- **FR-010**: All navigation links MUST work without JavaScript (progressive enhancement)
- **FR-011**: On mobile devices, the navbar menu links MUST collapse into a hamburger menu icon that expands to display navigation options when tapped
- **FR-012**: The slim navbar MUST have a maximum height of 100px on article pages, ensuring comfortable padding while maintaining a minimal footprint

### Key Entities

- **Navigation Bar**: A slim, persistent header component that appears on article pages, containing branding (name/logo) and navigation menu links
- **Hero Header**: The existing large biographical header component with photo, name, title, and social icons, displayed on the homepage
- **Bio Section**: A footer component for article pages containing the same biographical information as the hero header (photo, name, title, social icons)
- **Page Types**: Two distinct page types with different header requirements - Homepage (index) and Article Pages (blog posts)
- **Menu Items**: Navigation links including Home (links to homepage) and Blog (links to blog listing/section)

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Article readers can identify the navigation menu within 2 seconds of landing on an article page (measured via assumption based on navbar's fixed top position and visual prominence)
- **SC-002**: The slim navbar (maximum 100px height) reduces header visual space on article pages by at least 70% compared to the current large bio header (baseline measurement to be taken during implementation, e.g., current header ~350px → target <100px = 71% reduction)
- **SC-003**: Users can navigate between Home and Blog sections with a single click from any page
- **SC-004**: 100% of article pages display the bio section at the bottom of the content, maintaining author attribution while prioritizing content readability
- **SC-005**: The navbar remains visible and accessible when scrolling through articles of any length
- **SC-006**: Homepage maintains the same visual impact and first impression quality as the current design (no degradation in hero header presentation)
