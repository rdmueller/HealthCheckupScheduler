# Health Checkup Scheduler - Technical Specification

## 1. Overview

A web-based application that helps users manage their preventive health checkups by generating personalized calendar files (.ics) based on their age, gender, insurance type, and checkup history.

### Key Features
- Mandatory modal disclaimer on app launch (spec-driven development experiment notice)
- Age-appropriate checkup recommendations
- Insurance-type specific intervals (GKV/PKV/Recommended)
- Priority-based appointment staggering (prevents scheduling all checkups on the same day)
- Custom checkup management
- .ics calendar file generation
- Mobile-responsive design
- AI-generated disclaimer for transparency and user awareness

### Deployment
- **Platform:** GitHub Pages
- **Architecture:** Static single-page application (SPA)
- **File:** Single self-contained HTML file
- **URL Pattern:** `https://[username].github.io/[repository-name]/`
- **No Build Process:** Direct deployment of HTML file
- **No Server Required:** Pure client-side application

---

## 2. User Interface

### 2.1 Layout Structure
- Single-page application with 3-step wizard
- Progress indicator showing current step
- Responsive design (mobile-first approach)
- Health-themed color scheme (green primary, warm accent)

### 2.2 Color Palette
```css
--primary: #2a5a4a (dark green)
--primary-light: #3d7a66
--accent: #e8956f (warm orange)
--bg: #faf8f5 (cream background)
--text: #1a1a1a
--text-muted: #6b6b6b
--card: #ffffff
```

### 2.3 Typography
- Display font: 'DM Serif Display' (headings)
- Body font: 'Karla' (all other text)

---

## 3. Step-by-Step Flow

### Step 1: Basic Information
**Purpose:** Collect user demographics

**Fields:**
1. Gender (required)
   - Radio buttons: Male / Female
   - Visual selection with border highlight on selection
   
2. Birth Year (required)
   - Number input
   - Range: 1920-2024
   - Default: 1972
   
3. Insurance Type (required)
   - Dropdown select
   - Options:
     - GKV (Gesetzlich) - Statutory health insurance
     - PKV (Privat) - Private health insurance
     - Empfohlen (Best Practice) - Recommended intervals

**Validation:** All fields must be filled before proceeding

---

### Step 2: Checkup Configuration
**Purpose:** Configure which checkups to track and their history

#### 3.1 Current Checkups Section
Displays checkups appropriate for user's current age.

**For each checkup:**
- Checkbox (default: checked) - Enable/disable this checkup
- Checkup name
- Description
- Interval information (varies by insurance type)
- Age range (if applicable)
- Last checkup date input (if currently applicable)
- "Longer than X months ago" checkbox (disables date input when checked)

**Checkup disabled state:**
- Gray out entire checkup card
- Disable all inputs
- Visual opacity: 0.5

**Not available in tariff:**
- Show message: "In deinem gewählten Tarif nicht verfügbar"
- Suggest self-pay option

#### 3.2 Future Checkups Section
Displays checkups not yet age-appropriate but will be in the future.

**For each future checkup:**
- Checkbox (default: checked) - Include in calendar
- Checkup name
- Description
- Minimum age requirement
- Target year when user reaches minimum age
- Gray background to distinguish from current checkups
- NO date input fields (automatically scheduled)

#### 3.3 Custom Checkups Section
Allows users to add their own checkups not in the standard list.

**Input fields:**
1. Name of examination (text input)
2. Interval in months (number input, min: 1)
3. Last examination date (date input, optional)

**Add button:** Adds checkup to list below

**Custom checkup list:**
- Shows all added custom checkups
- Displays: name, interval, last date (if provided)
- Remove button for each entry

---

### Step 3: Summary & Download
**Purpose:** Review and download calendar file

#### 3.1 Summary Information
- Gender
- Age
- Insurance type
- Number of planned appointments

#### 3.2 Upcoming Checkups List
Displays all scheduled checkups in chronological order.

**For each checkup card:**
- Checkup name
- Date (formatted: DD. Month YYYY)
- Description
- Special indicators:
  - Red card border + red date for overdue/urgent
  - Yellow info box for future age-based checkups

#### 3.3 Actions
- Download .ics file button (prominent, orange accent color)
- Restart button (secondary style)

---

## 4. Data Model

### 4.1 User Data Structure
```javascript
{
  gender: 'male' | 'female',
  birthYear: number,
  age: number,
  insuranceType: 'gkv' | 'pkv' | 'recommended',
  checkups: {
    [checkupId]: lastDate | null
  },
  enabledCheckups: {
    [checkupId]: boolean
  },
  customCheckups: [
    {
      id: string,
      name: string,
      interval: number,
      lastDate: string | null,
      description: string
    }
  ]
}
```

### 4.2 Checkup Definition Structure
```javascript
{
  id: string,
  name: string,
  intervals: {
    gkv: number | null,  // months
    pkv: number | null,
    recommended: number | null
  },
  description: string,
  minAge: number,
  maxAge?: number  // optional
}
```

---

## 5. Checkup Definitions

### 5.1 Male Checkups
1. **General Practitioner Check-up**
   - GKV: 24 months, PKV: 12 months, Recommended: 12 months
   - Min age: 18
   - Description: Blood pressure, cholesterol, blood sugar, liver values, kidney values

2. **Colonoscopy**
   - GKV: 120 months, PKV: 60 months, Recommended: 96 months
   - Min age: 50
   - Description: Colorectal cancer screening

3. **Immunological Stool Test**
   - GKV: 24 months, PKV: 12 months, Recommended: 12 months
   - Min age: 50
   - Description: Alternative to colonoscopy

4. **Skin Cancer Screening**
   - GKV: 24 months, PKV: 12 months, Recommended: 12 months
   - Min age: 35
   - Description: Full body examination by dermatologist

5. **Prostate Check (Digital Rectal Exam)**
   - GKV: 12 months, PKV: 12 months, Recommended: 12 months
   - Min age: 45
   - Description: Urological examination

6. **PSA Test**
   - GKV: null, PKV: 12 months, Recommended: 12 months
   - Min age: 45
   - Description: Blood test for prostate cancer screening (GKV self-pay)

7. **Eye Examination**
   - GKV: 24 months, PKV: 12 months, Recommended: 24 months
   - Min age: 40
   - Description: Glaucoma screening, retinal check

8. **Extended Cardiovascular Check**
   - GKV: null, PKV: 24 months, Recommended: 24 months
   - Min age: 50
   - Description: ECG, stress ECG, cardiac ultrasound (PKV)

9. **Dental Care**
   - GKV: 6 months, PKV: 6 months, Recommended: 6 months
   - Min age: 18
   - Description: Professional teeth cleaning

### 5.2 Female Checkups
1. **General Practitioner Check-up** (same as male)

2. **Colonoscopy** (same as male)

3. **Immunological Stool Test** (same as male)

4. **Skin Cancer Screening** (same as male)

5. **Gynecological Examination**
   - GKV: 12 months, PKV: 12 months, Recommended: 12 months
   - Min age: 20
   - Description: Cervical cancer screening

6. **HPV Test**
   - GKV: 60 months, PKV: 36 months, Recommended: 36 months
   - Min age: 35
   - Description: Cervical cancer screening

7. **Mammography**
   - GKV: 24 months, PKV: 24 months, Recommended: 24 months
   - Min age: 50, Max age: 69
   - Description: Breast cancer screening

8. **Breast Ultrasound**
   - GKV: null, PKV: 12 months, Recommended: 12 months
   - Min age: 40
   - Description: Supplement to mammography (PKV/self-pay)

9. **Eye Examination** (same as male)

10. **Extended Cardiovascular Check** (same as male)

11. **Osteoporosis Screening**
    - GKV: null, PKV: 24 months, Recommended: 24 months
    - Min age: 50
    - Description: Bone density measurement (PKV/self-pay)

12. **Dental Care** (same as male)

---

## 5.3 Appointment Staggering Rationale

### The Problem
When users check "Länger her" (longer ago) for all checkups or don't provide any last checkup dates, without staggering all appointments would be scheduled on the same date (1 month from today). This is unrealistic because:
- It's impossible to complete 5-10 different preventive checkups on the same day
- Different checkups require appointments at different medical facilities
- Some checkups have specific preparation requirements
- Users need time to schedule and attend multiple appointments

### The Solution
The application implements a priority-based staggering system that automatically spreads appointments over several months based on medical importance and practical considerations:

**Example Distribution for a User with No Dates:**
- February 2026: GP Check-up (Priority 1)
- February 2026: Dental Care (Priority 1)
- March 2026: Gynecological/Prostate Exam (Priority 2)
- April 2026: Skin Screening (Priority 3)
- May 2026: Colonoscopy (Priority 4)
- June 2026: Extended Cardiovascular Check (Priority 5)

### Priority Assignment Criteria
Priorities are assigned based on:
1. **Medical urgency**: GP check-ups and dental care are foundational
2. **Frequency**: More frequent checkups (like dental) get higher priority
3. **Planning requirements**: Checkups requiring extensive preparation (colonoscopy) have lower priority
4. **Practical access**: Common checkups are prioritized over specialized ones

### Custom Checkup Staggering
Custom checkups without dates are staggered incrementally starting from the base priority (3 months), with each additional custom checkup scheduled one month later. This ensures:
- Custom checkups don't conflict with standard ones
- Multiple custom checkups are spread out
- Users have flexibility in scheduling

---

## 6. Business Logic

### 6.1 Age-Based Filtering
- Calculate current age from birth year
- Filter checkups into two categories:
  - **Current:** age >= minAge AND (no maxAge OR age <= maxAge)
  - **Future:** age < minAge

### 6.2 Checkup Priority System

To prevent all checkups from being scheduled on the same date when no last checkup date is provided, the application uses a priority-based staggering system. Each checkup has an assigned priority level that determines how many months from today it will be scheduled.

#### Priority Levels
- **Priority 1 (1 month):** Most urgent/important checkups
  - General Practitioner Check-up
  - Dental Care
  
- **Priority 2 (2 months):** Regular important checkups
  - Gynecological Examination
  - Prostate Examination (DRE)
  - Mammography
  
- **Priority 3 (3 months):** Standard preventive checkups (DEFAULT_PRIORITY)
  - Skin Cancer Screening
  - HPV Test
  - Eye Examination
  
- **Priority 4 (4 months):** Checkups requiring more planning
  - Immunological Stool Test
  - Colonoscopy
  - PSA Test
  - Breast Ultrasound
  
- **Priority 5 (5 months):** Extended/specialized checkups
  - Extended Cardiovascular Check
  - Osteoporosis Screening

#### Priority Constants
```javascript
const DEFAULT_PRIORITY = 3; // Default for checkups not in priority list
const CUSTOM_CHECKUP_BASE_PRIORITY = 3; // Starting priority for custom checkups
```

#### Staggering Logic
- Checkups without a last date are scheduled at: `current date + priority months`
- Custom checkups without dates are staggered: `current date + (CUSTOM_CHECKUP_BASE_PRIORITY + index) months`
- This spreads appointments over 1-5+ months instead of scheduling all on the same day

### 6.3 Calendar Event Generation

#### For Current Checkups:
1. If checkbox unchecked: Skip
2. If insurance interval is null: Skip
3. If "longer ago" checkbox is checked OR no date provided:
   - Schedule using priority: `current date + priority months`
   - Priority from `checkupPriorities` or DEFAULT_PRIORITY (3 months)
   - Mark as urgent if "longer ago" checked
   - Description: Add "(überfällig - bitte zeitnah vereinbaren)" or "(erstmalig)"
4. If last date provided:
   - Calculate: lastDate + interval months
   - Description: Standard description

#### For Future Checkups:
1. If checkbox unchecked: Skip
2. If insurance interval is null: Skip
3. Calculate: current date + (minAge - currentAge) years
4. Description: Add "(ab X Jahren empfohlen)"
5. Mark with future flag for UI distinction

#### For Custom Checkups:
1. If last date provided:
   - Calculate: lastDate + interval months
2. If no last date:
   - Schedule using staggered priority: `current date + (CUSTOM_CHECKUP_BASE_PRIORITY + index) months`
   - Index ensures each custom checkup is staggered by an additional month
   - Description: Add "(erstmalig)"

---

## 7. ICS File Generation

### 7.1 Calendar Structure
```
BEGIN:VCALENDAR
VERSION:2.0
PRODID:-//Gesundheitsvorsorge Planer//DE
CALSCALE:GREGORIAN
METHOD:PUBLISH
X-WR-CALNAME:Gesundheitsvorsorge
X-WR-TIMEZONE:Europe/Berlin

[Multiple VEVENT blocks]

END:VCALENDAR
```

### 7.2 Event Structure
```
BEGIN:VEVENT
UID:[unique-id]@gesundheitsvorsorge
DTSTAMP:[current-timestamp]
DTSTART;VALUE=DATE:[date-YYYYMMDD]
SUMMARY:[checkup-name]
DESCRIPTION:[checkup-description]
STATUS:CONFIRMED
TRANSP:TRANSPARENT
RRULE:FREQ=MONTHLY;INTERVAL=[interval-in-months]
BEGIN:VALARM
TRIGGER:-P7D
ACTION:DISPLAY
DESCRIPTION:Erinnerung: [checkup-name] in 1 Woche
END:VALARM
END:VEVENT
```

### 7.3 Recurring Events (RRULE)

The application generates recurring events using the RRULE (Recurrence Rule) property according to RFC 5545 standards.

**Implementation:**
- **Current checkups**: Include RRULE with monthly frequency based on the checkup's interval
- **Future checkups**: Do NOT include RRULE (single notification events only)
- **Custom checkups**: Include RRULE if they are current/active checkups

**RRULE Format:**
```
RRULE:FREQ=MONTHLY;INTERVAL=X
```
Where X is the interval in months (e.g., 6 for dental care, 12 for annual checkups, 24 for biennial checkups)

**Examples:**
- Dental care (6 months): `RRULE:FREQ=MONTHLY;INTERVAL=6`
- GP checkup - GKV (24 months): `RRULE:FREQ=MONTHLY;INTERVAL=24`
- Colonoscopy - PKV (60 months): `RRULE:FREQ=MONTHLY;INTERVAL=60`

**Rationale:**
- Current checkups are ongoing preventive measures that repeat regularly
- Future checkups are one-time notifications for when the user becomes age-eligible
- Recurring events ensure calendar applications automatically schedule follow-up appointments
- Users don't need to manually create new events after each checkup

### 7.4 Date Formatting
- Use VALUE=DATE for all-day events
- Format: YYYYMMDD (e.g., 20260504)
- Add reminder 7 days before (TRIGGER:-P7D)

---

## 8. UI/UX Details

### 8.1 Form Validation
- Step 1: All fields required before proceeding
- Step 2: Custom checkup fields validated on add (name and interval required)
- Visual feedback for invalid inputs

### 8.2 Interactive Elements

**Checkboxes:**
- Default checked for all standard checkups
- Disabling checkup disables all related inputs
- Visual feedback (opacity change)

**Date Inputs:**
- Max date: Today
- Disabled when "longer ago" checkbox is checked
- Clear date when "longer ago" is checked

**Custom Checkup Management:**
- Real-time list update on add
- Immediate removal on delete
- Clear input fields after successful add

### 8.3 Responsive Design
- Desktop: 3-column grid for custom checkup inputs
- Mobile: Single column layout
- Touch-friendly button sizes (min 44px)
- Readable text sizes (min 16px to prevent zoom on iOS)

### 8.4 Visual Feedback
- Hover states on all interactive elements
- Focus states with green border + shadow
- Disabled states with reduced opacity
- Transition animations (300ms ease)

### 8.5 Modal Disclaimer (Initial Launch)
The application displays a mandatory disclaimer modal on first load that users must accept before accessing the application:

**Modal Disclaimer Requirements:**
- Appears immediately when the application loads (before any other content is accessible)
- Blocks all interaction with the application until accepted
- Modal overlay with semi-transparent background
- Modal content centered on screen
- Clear, prominent "Accept" button to proceed
- No way to bypass or close the modal without accepting
- Text content (German):
  - "Dies ist ein Experiment für Spec-Driven-Development und keine echte App."
  - "Code und Dokumentation sind von einer KI erstellt worden."
  - "Alle Angaben müssen selbst überprüft werden."
  - "Bitte konsultieren Sie Ihren Arzt oder Ihre Ärztin für individuelle medizinische Beratung."

**Implementation Notes:**
- Modal is shown only once per session (no persistence across sessions)
- Modal uses z-index to appear above all other content
- Accept button should be styled prominently (accent color)
- Modal text should be easily readable (adequate font size and line spacing)

### 8.6 Footer & Disclaimer
The application footer includes an important disclaimer about AI-generated content:

**Disclaimer (Haftungsausschluss):**
- Clearly states that the application is completely AI-generated
- Emphasizes that all information must be verified by the user
- Advises users to consult their healthcare provider for individual medical advice
- Positioned prominently in the footer for visibility on all pages
- Text content (German):
  - "Diese Anwendung ist vollständig KI-generiert und bietet nur Orientierungshilfe."
  - "Alle Angaben müssen selbst überprüft werden."
  - "Bitte konsultieren Sie Ihren Arzt oder Ihre Ärztin für individuelle medizinische Beratung."

**Version Information:**
- Display current version number (e.g., "Version 1.0.0")
- Provides transparency about the application state

---

## 9. Technical Implementation Notes

### 9.1 Dependencies
- None required (vanilla HTML/CSS/JavaScript)
- Fonts: Google Fonts CDN
- Self-contained single HTML file

### 9.2 Browser Compatibility
- Modern browsers (Chrome, Firefox, Safari, Edge)
- ES6+ JavaScript
- CSS Grid and Flexbox
- Date input type support

### 9.3 File Download
- Create Blob from ICS content
- Generate download link dynamically
- Trigger download, then clean up

### 9.4 State Management
- Single global userData object
- Re-render UI sections on state changes
- No persistence (session-based)

---

## 10. Localization

**Language:** German (de-DE)

**Key Terms:**
- GKV: Gesetzliche Krankenversicherung (Statutory Health Insurance)
- PKV: Private Krankenversicherung (Private Health Insurance)
- Empfohlen: Recommended
- Vorsorge: Preventive care/screening
- Untersuchung: Examination
- Termin: Appointment

**Date Formatting:**
- Long format: "DD. Month YYYY" (e.g., "15. Juni 2026")
- Short format: "DD.MM.YYYY" (e.g., "15.06.2026")

---

## 11. Edge Cases & Error Handling

### 11.1 Age Edge Cases
- User under 18: Only dental care shown
- User over 70: Some checkups may have maxAge restrictions (e.g., mammography)
- Exact age boundaries: Use >= and <= comparisons

### 11.2 Date Calculations
- Handle month overflow (e.g., Jan 31 + 1 month = Feb 28/29)
- Use setMonth() which automatically handles overflow
- Ensure dates don't exceed current date for "last checkup"

### 11.3 Empty States
- No enabled checkups: Still allow download (empty calendar)
- No custom checkups: Hide list section
- Future checkups only: Show appropriate message

### 11.4 Data Validation
- Birth year range: 1920-2024
- Custom interval: Must be positive integer
- Date inputs: Must not be future dates

---

## 12. Deployment on GitHub Pages

### 12.1 Repository Setup
1. Create new GitHub repository
2. Name: `health-checkup-scheduler` (or any preferred name)
3. Initialize with README.md (optional)
4. Add the HTML file to repository root

### 12.2 File Structure in Repository
```
repository-root/
├── index.html (rename health-checkup-scheduler.html to index.html)
├── README.md (optional documentation)
└── LICENSE (optional)
```

### 12.3 GitHub Pages Configuration
1. Go to repository Settings
2. Navigate to "Pages" section
3. Source: Deploy from branch
4. Branch: `main` (or `master`)
5. Folder: `/ (root)`
6. Save configuration

### 12.4 Access
- URL: `https://[username].github.io/[repository-name]/`
- Example: `https://johndoe.github.io/health-checkup-scheduler/`
- If file is named `index.html`, it loads automatically
- Otherwise: `https://[username].github.io/[repository-name]/health-checkup-scheduler.html`

### 12.5 Updates
- Push changes to main branch
- GitHub Pages automatically rebuilds (usually within 1-2 minutes)
- No build process needed
- Changes are live immediately after rebuild

### 12.6 Custom Domain (Optional)
1. Add CNAME file to repository root
2. Content: your custom domain (e.g., `health.example.com`)
3. Configure DNS at domain registrar:
   - Add CNAME record pointing to `[username].github.io`
4. Enable HTTPS in GitHub Pages settings (recommended)

### 12.7 Technical Requirements
- Single HTML file with embedded CSS and JavaScript
- No external dependencies except Google Fonts (loaded via CDN)
- No server-side processing required
- All logic runs in browser
- Compatible with GitHub Pages' static hosting

### 12.8 Best Practices
- Use semantic versioning in commits
- Add meaningful commit messages
- Consider branch protection rules for main branch
- Use pull requests for significant changes
- Add repository description and topics for discoverability

---

## 13. Future Enhancement Possibilities

### 13.1 Potential Features
- Multi-language support
- Data persistence (localStorage)
- Email calendar invite option
- PDF export of checkup plan
- Print-friendly view
- Family member profiles
- Doctor contact information storage
- Checkup completion tracking
- End date/count limit for recurring events (currently infinite recurrence)

### 13.2 Integration Options
- Google Calendar direct integration
- Outlook Calendar integration
- Apple Health export
- Health insurance API connections

---

## 14. Testing Checklist

### 14.1 Functionality
- [ ] All three steps navigate correctly
- [ ] Form validation works on step 1
- [ ] Age filtering shows correct checkups
- [ ] Insurance type changes intervals correctly
- [ ] Checkboxes enable/disable checkups
- [ ] Date inputs work correctly
- [ ] "Longer ago" checkbox disables date input
- [ ] Custom checkups can be added
- [ ] Custom checkups can be removed
- [ ] Custom checkups with dates calculate correctly
- [ ] ICS file downloads successfully
- [ ] ICS file imports into calendars correctly
- [ ] Current checkups have RRULE and repeat automatically in calendar
- [ ] Future checkups do NOT have RRULE (single events only)
- [ ] RRULE intervals match checkup intervals (e.g., 6 months for dental)
- [ ] Recurring events display correctly in Google Calendar, Apple Calendar, Outlook
- [ ] Restart button clears all data
- [ ] Back button works between steps
- [ ] Checkups without dates are staggered (not all on same day)
- [ ] Priority 1 checkups scheduled 1 month from today
- [ ] Priority 2 checkups scheduled 2 months from today
- [ ] Priority 3+ checkups scheduled appropriately
- [ ] Multiple custom checkups without dates are staggered incrementally

### 14.2 UI/Visual
- [ ] Responsive on mobile (320px width)
- [ ] Responsive on tablet (768px width)
- [ ] Responsive on desktop (1024px+ width)
- [ ] All text is readable
- [ ] Colors have sufficient contrast
- [ ] Buttons are touch-friendly
- [ ] Form inputs align correctly
- [ ] Progress indicator updates correctly
- [ ] Urgent checkups show red styling
- [ ] Future checkups show yellow info box

### 14.3 Browser Testing
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest)
- [ ] Edge (latest)
- [ ] Mobile Safari (iOS)
- [ ] Chrome Mobile (Android)

### 14.4 Data Scenarios
- [ ] 18-year-old user
- [ ] 30-year-old user
- [ ] 52-year-old user (born 1972)
- [ ] 70-year-old user
- [ ] Male vs Female differences
- [ ] GKV vs PKV vs Recommended differences
- [ ] All checkups disabled
- [ ] All checkups enabled
- [ ] Mix of past and no dates
- [ ] Multiple custom checkups
- [ ] All checkups with "longer ago" checked (verify staggering)
- [ ] All checkups without dates (verify staggering)
- [ ] Scenario: Multiple priority 1 checkups should both be scheduled at 1 month
- [ ] Scenario: GP Check-up (priority 1) and Skin Screening (priority 3) should be 2 months apart

---

## 15. File Structure

```
health-checkup-scheduler.html
├── HTML Structure
│   ├── Header
│   ├── Step 1: Basic Information
│   ├── Step 2: Checkup Configuration
│   └── Step 3: Summary & Download
├── CSS Styles
│   ├── Global Variables
│   ├── Base Styles
│   ├── Component Styles
│   └── Media Queries
└── JavaScript
    ├── Data Structures
    ├── Checkup Definitions
    ├── Navigation Functions
    ├── Rendering Functions
    ├── Event Handlers
    ├── Calendar Generation
    └── Utility Functions
```

---

## 16. Code Organization Guidelines

### 16.1 Naming Conventions
- camelCase for variables and functions
- kebab-case for CSS classes
- Descriptive names (e.g., `generateCalendar` not `genCal`)

### 16.2 Function Organization
```javascript
// Global State
let userData = {...}
let calendarEvents = []

// Data Definitions
const checkupDefinitions = {...}

// Navigation Functions
function goToStep1() {...}
function goToStep2() {...}
function showStep(stepNumber) {...}

// Rendering Functions
function renderCheckupList() {...}
function renderCustomCheckups() {...}
function renderSummary() {...}

// Event Handlers
function toggleDateInput(checkupId) {...}
function toggleCheckupEnabled(checkupId) {...}
function addCustomCheckup() {...}
function removeCustomCheckup(index) {...}

// Calendar Generation
function generateCalendar() {...}
function downloadICS() {...}
function formatICSDate(date) {...}

// Utility Functions
function restart() {...}
```

### 16.3 Code Style
- Use ES6+ features (arrow functions, template literals, destructuring)
- Keep functions focused and single-purpose
- Add comments for complex logic
- Use meaningful variable names
- Maintain consistent indentation (2 or 4 spaces)

---

## Document Version
- Version: 1.1
- Last Updated: January 2026
- Status: Complete Implementation
- Changelog:
  - v1.1: Added recurring events (RRULE) for current checkups
  - v1.0: Initial implementation
