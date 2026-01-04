# Health Checkup Scheduler - Technical Specification

## 1. Overview

A web-based application that helps users manage their preventive health checkups by generating personalized calendar files (.ics) based on their age, gender, insurance type, and checkup history.

### Key Features
- Age-appropriate checkup recommendations
- Insurance-type specific intervals (GKV/PKV/Recommended)
- Custom checkup management
- .ics calendar file generation
- Mobile-responsive design

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

## 6. Business Logic

### 6.1 Age-Based Filtering
- Calculate current age from birth year
- Filter checkups into two categories:
  - **Current:** age >= minAge AND (no maxAge OR age <= maxAge)
  - **Future:** age < minAge

### 6.2 Calendar Event Generation

#### For Current Checkups:
1. If checkbox unchecked: Skip
2. If insurance interval is null: Skip
3. If "longer ago" checkbox is checked OR no date provided:
   - Schedule: 1 month from now
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
   - Schedule: 1 month from now
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
BEGIN:VALARM
TRIGGER:-P7D
ACTION:DISPLAY
DESCRIPTION:Erinnerung: [checkup-name] in 1 Woche
END:VALARM
END:VEVENT
```

### 7.3 Date Formatting
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
- Recurring event series in ICS
- Family member profiles
- Doctor contact information storage
- Checkup completion tracking

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
- [ ] Restart button clears all data
- [ ] Back button works between steps

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
- Version: 1.0
- Last Updated: January 2026
- Status: Complete Implementation
