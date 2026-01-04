# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Health Checkup Scheduler is a static web application that generates personalized preventive health checkup calendars for German users. The application will be deployed as a **single self-contained HTML file** on GitHub Pages.

**Current Status:** Documentation phase - no implementation yet. The `docs/` directory contains complete technical specifications and architecture documentation.

## Architecture

### Single-File Application
- **One HTML file**: All HTML, CSS, and JavaScript must be in a single `index.html` file
- **No build process**: Direct deployment to GitHub Pages
- **No dependencies**: Vanilla JavaScript only (ES6+), no npm packages or frameworks
- **External resources**: Only Google Fonts CDN allowed

### Technology Stack
- Pure HTML5, CSS3 (Grid + Flexbox), JavaScript ES6+
- No frameworks (React/Vue/Angular)
- No bundlers or build tools
- Client-side only (no backend, no API calls)

### Key Architectural Decisions
1. **Privacy by design**: All processing in browser, data never leaves device
2. **No data persistence**: Session-only state (no localStorage, no cookies)
3. **iCalendar export**: Standard .ics format for universal calendar compatibility
4. **Age-based filtering**: Checkups filtered into "current" and "future" categories
5. **Insurance-aware**: Three interval sets (GKV/PKV/Recommended)

## Implementation Guidelines

### File Structure
The final repository should look like:
```
/
├── index.html          (the entire application)
├── docs/
│   ├── spec.md        (technical specification - READ THIS FIRST)
│   └── arc42.md       (architecture documentation)
├── LICENSE
└── CLAUDE.md          (this file)
```

### Code Organization Within index.html

The single HTML file must be organized in this order:

```html
<!DOCTYPE html>
<html lang="de">
<head>
  <!-- Meta tags, title, Google Fonts link -->
  <style>
    /* 1. CSS Variables (color palette) */
    /* 2. Base styles */
    /* 3. Component styles */
    /* 4. Media queries */
  </style>
</head>
<body>
  <!-- HTML structure for 3-step wizard -->

  <script>
    // 1. Global State
    // 2. Data Definitions (checkupDefinitions)
    // 3. Navigation Functions
    // 4. Rendering Functions
    // 5. Event Handlers
    // 6. Calendar Generation (ICS)
    // 7. Utility Functions
    // 8. Initialization
  </script>
</body>
</html>
```

### Data Model

**User Data Structure:**
```javascript
{
  gender: 'male' | 'female',
  birthYear: number,
  age: number,
  insuranceType: 'gkv' | 'pkv' | 'recommended',
  checkups: { [checkupId]: lastDate | null },
  enabledCheckups: { [checkupId]: boolean },
  customCheckups: [{
    id: string,
    name: string,
    interval: number,
    lastDate: string | null,
    description: string
  }]
}
```

**Checkup Definition Structure:**
```javascript
{
  id: string,
  name: string,
  intervals: {
    gkv: number | null,    // months, null = not covered
    pkv: number | null,
    recommended: number | null
  },
  description: string,
  minAge: number,
  maxAge?: number          // optional
}
```

### Key Business Logic

1. **Age Calculation:**
   - Current year - birth year = age
   - Filter checkups: `age >= minAge AND (no maxAge OR age <= maxAge)`

2. **Date Calculation for Current Checkups:**
   - If "longer ago" checked OR no date: schedule 1 month from now
   - If last date provided: `lastDate + interval months`

3. **Date Calculation for Future Checkups:**
   - `currentDate + (minAge - currentAge) years`

4. **ICS Generation:**
   - RFC 5545 format
   - All-day events (VALUE=DATE, format: YYYYMMDD)
   - Include 7-day reminder (TRIGGER:-P7D)
   - Unique UID: `[id]@gesundheitsvorsorge`

### Checkup Definitions

Refer to `docs/spec.md` Section 5 for complete checkup definitions. Key points:
- **Male**: 9 checkups (GP, colonoscopy, stool test, skin screening, prostate, PSA, eye, cardiovascular, dental)
- **Female**: 12 checkups (includes gynecological, HPV, mammography, breast ultrasound, osteoporosis)
- **Insurance differences**: GKV may have `null` intervals (self-pay), PKV often has shorter intervals

### UI/UX Requirements

**3-Step Wizard:**
1. **Basic Info**: Gender (radio), birth year (1920-2024), insurance type (dropdown)
2. **Checkup Config**: Current checkups (with date inputs), future checkups (no dates), custom checkups
3. **Summary**: Display sorted events, download .ics button

**Responsive Design:**
- Desktop: Multi-column layouts
- Mobile: Single column (min width: 320px)
- Touch-friendly buttons (min 44px)
- Readable text (min 16px to prevent iOS zoom)

**Visual Design:**
- Color palette: Primary #2a5a4a (green), Accent #e8956f (orange), Background #faf8f5 (cream)
- Fonts: 'DM Serif Display' (headings), 'Karla' (body)
- Transitions: 300ms ease
- Focus states: Green border + shadow

### Testing Checklist

Before deployment, verify:
- ✅ All 3 steps navigate correctly
- ✅ Age filtering shows correct checkups for test ages (18, 30, 52, 70)
- ✅ Insurance type changes intervals correctly
- ✅ ICS file downloads and imports into Google Calendar, Apple Calendar, Outlook
- ✅ Responsive on mobile (320px), tablet (768px), desktop (1024px+)
- ✅ Works in Chrome, Firefox, Safari, Edge
- ✅ "Longer ago" checkbox disables date input
- ✅ Custom checkups can be added and removed
- ✅ Date calculations handle month overflow correctly

## Development Workflow

### Starting Implementation

1. **Read documentation first:**
   ```bash
   # Review the complete specification
   cat docs/spec.md

   # Review architecture decisions
   cat docs/arc42.md
   ```

2. **Create the HTML file:**
   - Start with basic HTML5 structure
   - Add CSS variables and base styles
   - Implement step-by-step (start with Step 1, then 2, then 3)
   - Add JavaScript functionality incrementally

3. **Test locally:**
   ```bash
   # Open directly in browser
   open index.html
   # OR use a simple HTTP server
   python3 -m http.server 8000
   ```

### Deployment to GitHub Pages

```bash
# Ensure on main branch
git checkout main

# Add and commit
git add index.html
git commit -m "Add Health Checkup Scheduler implementation"

# Push to GitHub
git push origin main

# Configure GitHub Pages (via GitHub web interface):
# Settings → Pages → Source: main branch, / (root)
# URL will be: https://[username].github.io/HealthCheckupScheduler/
```

### Updating Medical Guidelines

When checkup recommendations change:

1. Locate the `checkupDefinitions` object in the `<script>` section
2. Update the relevant interval values (in months)
3. Add/remove checkup definitions as needed
4. Update descriptions if required
5. Test with various age/insurance combinations
6. Commit and push to deploy

Example:
```javascript
// Change PSA test interval for PKV
{
  id: 'psa',
  name: 'PSA-Test',
  intervals: {
    gkv: null,
    pkv: 12,  // Change this value
    recommended: 12
  },
  // ...
}
```

## Important Constraints

1. **No frameworks or libraries** - Keep it vanilla JavaScript
2. **No build process** - Single HTML file must work as-is
3. **No backend** - Everything client-side
4. **German language** - All text in German
5. **File size target** - Keep under 200KB for fast loading
6. **Browser support** - Modern browsers only (ES6+ is fine)
7. **No data persistence** - Session-only by design (privacy)

## Common Pitfalls to Avoid

1. **Don't create separate files** - Everything in `index.html`
2. **Don't add npm/package.json** - No dependencies allowed
3. **Don't use localStorage** - Privacy requirement, no persistence
4. **Don't skip RFC 5545 compliance** - ICS files must be standard-compliant
5. **Don't hardcode current year** - Calculate dynamically for age
6. **Don't forget month overflow** - JavaScript Date handles this automatically
7. **Don't mix up date formats** - Use YYYYMMDD for ICS, DD.MM.YYYY for display

## Medical Accuracy Notes

- Checkup recommendations are based on German healthcare guidelines as of 2026
- GKV (statutory) and PKV (private) intervals reflect typical coverage
- "Recommended" intervals follow medical best practices
- Include disclaimer: Application provides guidance only, consult healthcare provider
- Review medical guidelines annually for updates

## Future Enhancements (Not in MVP)

These are documented but **not** to be implemented initially:
- Multi-language support (i18n)
- Data persistence (localStorage option)
- PWA manifest
- Recurring events in ICS
- Direct calendar API integration
- Family member profiles
- Print-friendly view

Keep the initial implementation simple and focused on core functionality.
