# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a static HTML portfolio website for Shanthi Acharya, a Product Design Leader with 15+ years of experience in enterprise UX design. The portfolio showcases professional experience, skills, and case studies of design work from companies like Workday, Ellie Mae, GE Digital, and Western Digital.

## Architecture

### Structure

The codebase is organized as a multi-page static website:

- **Root pages**: Main portfolio (`vibrant_portfolio.html`) and legacy case study page (`case-study.html`)
- **Case study pages**: Individual case studies located in `case-studies/[project-name]/index.html`
  - `analytics-data-platform/` - Workday Prism Analytics platform redesign
  - `subscription-management/` - Contract management restructuring
  - `data-prep-editor/` - Data transformation tool
  - `color-palette-tool/` - Personal project for data visualization
  - `senior-companion-app/` - Hackathon project
- **Assets**: Profile photo and other images in `images/` directory

### Design System

The portfolio uses a consistent design system across pages:

**Primary Design System (vibrant_portfolio.html and newer case studies):**
- Color palette based on DataCamp branding:
  - Primary Blue: `#003D82`
  - Accent Teal: `#00C7B1`
  - Accent Orange: `#FF6B35`
  - Neutral backgrounds and borders
- Typography: Poppins font family from Google Fonts
- Spacing and sizing follow a consistent scale

**Legacy Design System (analytics-data-platform/index.html):**
- Uses pastel color palette (lavender, mint, peach, rose, blue, yellow)
- Accent colors: Purple `#9B84C8`, Teal `#7FC8C4`

### Navigation Pattern

All pages include:
- Fixed navigation bar with logo and back button
- Smooth scroll behavior
- Mobile-responsive menu toggle
- Footer with copyright

## Prism Analytics Case Study Architecture (IMPORTANT)

The `workday-prism-analytics-platform` case study uses a **dynamic JavaScript-based side panel** pattern that is different from other case studies. Understanding this architecture is critical to avoid confusion when making updates.

### How the Side Panel Works

```
┌─────────────────────────────────────────────────────────────────┐
│  workday-prism-analytics-platform/index.html                    │
│  ┌──────────────────────┐    ┌──────────────────────────────┐  │
│  │   pillarContent {}   │───▶│  render*CaseStudy()          │  │
│  │   (JavaScript data)  │    │  (renders HTML to side panel)│  │
│  └──────────────────────┘    └──────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ▲
                              │ DOES NOT LOAD
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│  case-studies/data-prep-editor/index.html                       │
│  (Standalone page - only accessed via direct URL navigation)    │
└─────────────────────────────────────────────────────────────────┘
```

**Key Point:** The side panel does NOT load individual case study HTML files. Content is rendered from a JavaScript object.

### Where to Make Updates

| What you want to update | Where to edit |
|------------------------|---------------|
| Side panel content (via Prism Platform) | `workday-prism-analytics-platform/index.html` → `pillarContent['pillar-name']` object |
| Side panel rendering/structure | `workday-prism-analytics-platform/index.html` → `render*CaseStudy()` function |
| Side panel component styles | `workday-prism-analytics-platform/index.html` → `<style>` section |
| Standalone case study page | `case-studies/[project-name]/index.html` |
| Portfolio card title/description | `vibrant_portfolio.html` → project card section |

### Pillar Content Structure

The `pillarContent` object (around line 2050+) contains data for each pillar:

- `'data-integration'` - Data Integration Framework content
- `'data-preparation'` - Transform Canvas content (uses `renderDataPrepCaseStudy()`)
- `'data-management'` - Data Management content
- `'unified-monitoring'` - Unified Monitoring content

### Data Preparation Content Object

The `data-preparation` pillar has this structure:

```javascript
pillarContent['data-preparation'] = {
    title: 'Transform Canvas: Visual Pipeline Editor',
    subtitle: '...',
    heroMeta: { role, timeline, team, platform },
    heroStats: [{ value, label }, ...],      // 4 key metrics
    problemCards: [{ icon, title, description }, ...],  // 6 challenge cards
    researchTimeline: [{ date, title, description }, ...],
    researchPhases: [{ title, items: [] }, ...],
    keyInsights: [{ number, title, description, quote, attribution }, ...],
    personas: [{ name, role, isPrimary, background, goals, painPoints }, ...],
    solutionFeatures: [{ icon, title, description }, ...],  // 6 features
    releaseScope: [{ feature, description, status, release }, ...],
    impact: { efficiency: [], userQuote: {} },
    reflectionCards: [{ icon, title, content }, ...],
    outcomes: [{ value, label }, ...]
};
```

### Render Functions

- `renderDataPrepCaseStudy()` - Renders Transform Canvas (data-preparation) with full 9-section structure
- `renderFullCaseStudy()` - Renders Data Integration Framework with goals/approach/design areas
- `renderPanelContent()` - Default renderer for simpler pillars

### Adding New Sections to Side Panel

1. Add new data fields to the `pillarContent` object
2. Update the appropriate `render*CaseStudy()` function to generate HTML
3. Add CSS styles for new components in the `<style>` section
4. Update TOC sections array in the render function

### Files to Keep in Sync

When updating the Transform Canvas case study, you may need to update multiple places:

1. **Side panel content**: `workday-prism-analytics-platform/index.html` → `pillarContent['data-preparation']`
2. **Standalone page**: `case-studies/data-prep-editor/index.html` (if it should match)
3. **Portfolio card**: `vibrant_portfolio.html` → project card title/description

## Key Features

### Main Portfolio Page (`vibrant_portfolio.html`)

- Hero section with profile photo and CTAs
- About section with professional summary
- Skills grid showcasing 6 core competencies
- Timeline-based experience section with hover effects
- Projects grid with clickable cards linking to case studies
- Contact section with social links
- Mobile-responsive design with hamburger menu

### Case Study Pages

- Consistent header with back navigation to portfolio
- Structured content with:
  - Executive summary
  - Challenge/problem statement
  - Approach and methodology
  - Solutions implemented
  - Results and outcomes
  - Future vision
- Rich typography hierarchy (H2, H3, H4)
- Blockquotes for customer feedback
- Responsive design

## Development Notes

### Viewing the Portfolio

Since this is a static HTML site with no build process:

1. Open `vibrant_portfolio.html` directly in a browser
2. Or serve via local HTTP server: `python -m http.server 8000` (or `python3 -m http.server 8000`)
3. Navigate to case studies by clicking project cards

### Making Design Changes

**Color Scheme:**
- CSS custom properties (CSS variables) are defined in `:root` selector
- Modify colors in one place to update throughout the page
- Main palette defined at lines 17-32 in vibrant_portfolio.html

**Typography:**
- Font family is Poppins (loaded from Google Fonts)
- Font weights: 300, 400, 500, 600, 700, 800
- Adjust font sizes via section-specific classes

**Layout:**
- Maximum content width: 900px (`max-width` on `.container`)
- Responsive breakpoint: 768px for mobile
- Uses CSS Grid for skills and projects sections

### Navigation Links

Internal navigation uses:
- Hash links for same-page navigation (e.g., `#projects`, `#contact`)
- Relative paths for case studies (e.g., `case-studies/analytics-data-platform/`)
- Back navigation to `vibrant_portfolio.html` from case study pages

### Images

- Profile photo: `images/profile-photo.jpg`
- Currently referenced but may need to exist for proper display
- Uses `object-fit: cover` for consistent sizing

## Consistency Guidelines

When adding new case studies or modifying pages:

1. **Use the current design system** from `vibrant_portfolio.html` (DataCamp colors, not pastel)
2. **Maintain navigation pattern**: Fixed nav with logo + back button
3. **Follow case study structure**: Executive Summary → Challenge → Approach → Solution → Results → Future Vision
4. **Keep responsive**: Test at mobile breakpoint (768px)
5. **Typography hierarchy**: H2 for major sections, H3 for subsections, H4 for detailed topics
6. **Use consistent spacing**: Match padding/margin values from existing pages
7. **Link back to portfolio**: All case study pages should link to `vibrant_portfolio.html`

## Common Tasks

### Adding a New Case Study

1. Create directory: `case-studies/[project-name]/`
2. Create `index.html` based on existing case study template
3. Use the DataCamp color palette CSS variables
4. Add project card to `vibrant_portfolio.html` in the projects grid section
5. Link the card to the new case study directory
6. Ensure back navigation points to `vibrant_portfolio.html`

### Updating Colors Site-Wide

Modify CSS custom properties in the `:root` selector of each HTML file. For consistency, update:
- `vibrant_portfolio.html` (lines 17-32)
- All case study pages in `case-studies/*/index.html`

### Updating Contact Information

Contact details appear in:
- `vibrant_portfolio.html` contact section (lines 875-892)
- Email: shanthi.s.acharya@gmail.com
- Phone: 510-333-3372
- Portfolio URL: www.shanthiacharya.com
