# Architecture Documentation - Health Checkup Scheduler
**arc42 Template v8.2**

---

## 1. Introduction and Goals

### 1.1 Requirements Overview

The Health Checkup Scheduler is a web-based application that helps users manage their preventive health checkups by generating personalized calendar files based on individual health needs.

**Core Functionality:**
- Generate age-appropriate checkup recommendations
- Support different insurance types (GKV/PKV/Recommended)
- Allow custom checkup management
- Export calendar events as .ics files
- Provide mobile-responsive interface

**Key Drivers:**
- **Accessibility:** No installation required, works on any device
- **Privacy:** All data processing client-side, no server storage
- **Usability:** Simple 3-step process, clear visual design
- **Portability:** Standard .ics format for universal calendar compatibility

### 1.2 Quality Goals

| Priority | Quality Goal | Motivation |
|----------|--------------|------------|
| 1 | **Usability** | Users should complete the process in under 5 minutes without instructions |
| 2 | **Compatibility** | Must work on all major browsers and calendar applications |
| 3 | **Privacy** | No data leaves the user's device, complete client-side processing |
| 4 | **Maintainability** | Single-file architecture, no dependencies, easy to update |
| 5 | **Accessibility** | Responsive design, readable fonts, sufficient contrast |

### 1.3 Stakeholders

| Role | Expectations | Contact |
|------|--------------|---------|
| **End Users** | Easy tool to track health checkups, works on mobile | General public |
| **Developers** | Clear code structure, easy to enhance, good documentation | GitHub contributors |
| **Healthcare Advisors** | Accurate medical recommendations, up-to-date guidelines | Medical consultants |
| **Repository Owner** | Maintainable codebase, clear architecture | Project maintainer |

---

## 2. Constraints

### 2.1 Technical Constraints

| Constraint | Description | Impact |
|------------|-------------|---------|
| **TC-1: GitHub Pages** | Must be deployable as static site on GitHub Pages | No server-side processing, single HTML file or static files only |
| **TC-2: No Backend** | Pure client-side application | All logic in JavaScript, no database, no API calls (except fonts) |
| **TC-3: Browser Support** | Must support Chrome, Firefox, Safari, Edge (latest versions) | Use standard ES6+, avoid experimental features |
| **TC-4: Mobile-First** | Must work on smartphones (min 320px width) | Responsive CSS, touch-friendly UI |
| **TC-5: Offline-Capable** | Should work without internet (after initial load) | Self-contained file, only external dependency is fonts |

### 2.2 Organizational Constraints

| Constraint | Description |
|------------|-------------|
| **OC-1: Open Source** | Code must be publicly available on GitHub |
| **OC-2: No Dependencies** | Avoid npm packages or build tools for simplicity |
| **OC-3: German Language** | Primary language is German for German healthcare context |
| **OC-4: Medical Accuracy** | Checkup recommendations must align with German healthcare guidelines |

### 2.3 Conventions

| Convention | Description |
|------------|-------------|
| **Code Style** | ES6+ JavaScript, camelCase variables, descriptive naming |
| **Date Format** | German locale (DD.MM.YYYY for display, ISO for internal) |
| **File Format** | iCalendar (.ics) RFC 5545 standard |
| **Documentation** | Markdown for all documentation |

---

## 3. Context and Scope

### 3.1 Business Context

```
┌─────────────┐
│   End User  │
│  (Browser)  │
└──────┬──────┘
       │ Interacts via UI
       │ Downloads .ics
       ▼
┌─────────────────────────────────┐
│  Health Checkup Scheduler       │
│  (Static Web Application)       │
│                                 │
│  • Collects user data           │
│  • Calculates checkup dates     │
│  • Generates .ics file          │
└──────┬──────────────────────────┘
       │ Loads fonts
       ▼
┌─────────────┐
│ Google Fonts│
│     CDN     │
└─────────────┘

       │ Imports .ics
       ▼
┌─────────────────────┐
│  Calendar Apps      │
│  • Google Calendar  │
│  • Apple Calendar   │
│  • Outlook          │
│  • etc.            │
└─────────────────────┘
```

**External Entities:**

| Entity | Input | Output |
|--------|-------|--------|
| **User** | Personal data (age, gender, insurance type), checkup history | Calendar file (.ics) |
| **Google Fonts CDN** | Font files (DM Serif Display, Karla) | - |
| **Calendar Applications** | - | Imported calendar events |

### 3.2 Technical Context

```
┌──────────────────────────────────────────────┐
│           User's Web Browser                 │
│  ┌────────────────────────────────────────┐  │
│  │         HTML Document                   │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │         CSS Styles               │  │  │
│  │  │  • Responsive layout             │  │  │
│  │  │  • Visual design                 │  │  │
│  │  └──────────────────────────────────┘  │  │
│  │  ┌──────────────────────────────────┐  │  │
│  │  │      JavaScript Application      │  │  │
│  │  │  • Business logic               │  │  │
│  │  │  • State management             │  │  │
│  │  │  • ICS generation               │  │  │
│  │  └──────────────────────────────────┘  │  │
│  └────────────────────────────────────────┘  │
│                                              │
│  Browser APIs:                               │
│  • Date/Time                                 │
│  • Blob/File Download                        │
│  • DOM Manipulation                          │
└──────────────────────────────────────────────┘

External:
• Google Fonts (HTTPS)
• GitHub Pages (HTTPS hosting)
```

**Technical Interfaces:**

| Interface | Technology | Protocol | Purpose |
|-----------|------------|----------|---------|
| User Interface | HTML5, CSS3 | - | User interaction |
| Data Storage | Browser Memory | - | Temporary state during session |
| Font Loading | Google Fonts CDN | HTTPS | Typography resources |
| File Download | Blob API | - | .ics file generation |
| Deployment | GitHub Pages | HTTPS | Static file hosting |

---

## 4. Solution Strategy

### 4.1 Technology Decisions

| Decision | Rationale |
|----------|-----------|
| **Single HTML File** | Simplifies deployment, ensures all code is self-contained, no build process needed |
| **Vanilla JavaScript** | No framework overhead, faster load time, easier maintenance, no version dependencies |
| **Client-Side Only** | Privacy by design, no backend costs, works offline, immediate response |
| **CSS Grid + Flexbox** | Modern layout system, responsive without media query complexity |
| **iCalendar Format** | Universal standard, compatible with all major calendar applications |

### 4.2 Top-Level Decomposition

The application follows a **layered architecture**:

```
┌─────────────────────────────────────────┐
│         Presentation Layer              │
│  • HTML structure                       │
│  • CSS styling                          │
│  • User interaction handlers            │
└─────────────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────┐
│         Application Layer               │
│  • Navigation logic                     │
│  • Form validation                      │
│  • State management                     │
└─────────────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────┐
│          Domain Layer                   │
│  • Checkup definitions                  │
│  • Age calculation                      │
│  • Date calculation                     │
│  • Business rules                       │
└─────────────────────────────────────────┘
                  ▼
┌─────────────────────────────────────────┐
│       Infrastructure Layer              │
│  • ICS file generation                  │
│  • Date formatting                      │
│  • File download                        │
└─────────────────────────────────────────┘
```

### 4.3 Quality Achievement

| Quality Goal | Approach |
|--------------|----------|
| **Usability** | 3-step wizard, progress indicator, clear labels, immediate feedback |
| **Compatibility** | Standard web technologies, tested across browsers, .ics RFC 5545 compliance |
| **Privacy** | No external API calls (except fonts), no cookies, no tracking, no data transmission |
| **Maintainability** | Clear function naming, modular code organization, comprehensive documentation |
| **Accessibility** | Semantic HTML, sufficient color contrast, responsive design, keyboard navigation |

---

## 5. Building Block View

### 5.1 Level 1: System Overview

```
┌────────────────────────────────────────────────────────┐
│         Health Checkup Scheduler Application           │
│                                                        │
│  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │     UI       │  │   Business   │  │    Data     │ │
│  │  Components  │◄─┤    Logic     │◄─┤  Structures │ │
│  └──────────────┘  └──────────────┘  └─────────────┘ │
│         │                   │                │        │
│         ▼                   ▼                ▼        │
│  ┌──────────────────────────────────────────────────┐ │
│  │            ICS Export Module                     │ │
│  └──────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

**Components:**

| Component | Responsibility | Interface |
|-----------|----------------|-----------|
| **UI Components** | Display forms, handle user input, show results | HTML elements, event handlers |
| **Business Logic** | Calculate dates, filter checkups, validate input | JavaScript functions |
| **Data Structures** | Store user data, checkup definitions, calendar events | JavaScript objects |
| **ICS Export Module** | Generate .ics file content, trigger download | Blob API, download link |

### 5.2 Level 2: Component Details

#### 5.2.1 UI Components

```
UI Components
├── Step Navigation
│   ├── Progress Indicator
│   └── Step Switcher
├── Step 1: Basic Info Form
│   ├── Gender Selector
│   ├── Birth Year Input
│   └── Insurance Type Selector
├── Step 2: Checkup Config
│   ├── Current Checkups List
│   ├── Future Checkups List
│   └── Custom Checkups Manager
└── Step 3: Summary View
    ├── Summary Info
    ├── Checkup Cards
    └── Download Button
```

#### 5.2.2 Business Logic

```
Business Logic
├── Navigation Controller
│   ├── goToStep1()
│   ├── goToStep2()
│   └── showStep()
├── Checkup Calculator
│   ├── filterByAge()
│   ├── calculateNextDate()
│   └── applyInsuranceIntervals()
├── Validation
│   ├── validateBasicInfo()
│   └── validateCustomCheckup()
└── State Manager
    ├── userData (global state)
    └── calendarEvents (global state)
```

#### 5.2.3 Data Structures

```
Data Structures
├── checkupDefinitions
│   ├── male[]
│   └── female[]
├── userData
│   ├── gender
│   ├── birthYear
│   ├── age
│   ├── insuranceType
│   ├── checkups{}
│   ├── enabledCheckups{}
│   └── customCheckups[]
└── calendarEvents[]
    └── event{name, date, description, interval, urgent, future}
```

#### 5.2.4 ICS Export Module

```
ICS Export Module
├── generateCalendar()
│   ├── Process standard checkups
│   ├── Process future checkups
│   └── Process custom checkups
├── downloadICS()
│   ├── Build VCALENDAR structure
│   ├── Add VEVENT blocks
│   ├── Create Blob
│   └── Trigger download
└── formatICSDate()
    └── Convert Date to YYYYMMDD format
```

---

## 6. Runtime View

### 6.1 Scenario: Complete User Journey

```
User                 UI              BusinessLogic        Data             ICS Export
  │                  │                    │                │                   │
  │─── Open Page ───▶│                    │                │                   │
  │                  │─── Load Page ─────▶│                │                   │
  │                  │                    │─ Init State ──▶│                   │
  │                  │◀─── Render Step 1 ─│                │                   │
  │                  │                    │                │                   │
  │─ Fill Form ─────▶│                    │                │                   │
  │─ Click "Next" ──▶│                    │                │                   │
  │                  │─ Validate ────────▶│                │                   │
  │                  │─ Calculate Age ───▶│                │                   │
  │                  │─ Store Data ──────▶│───────────────▶│                   │
  │                  │─ Filter Checkups ─▶│◀───── Read ────│                   │
  │                  │◀─ Render Step 2 ───│                │                   │
  │                  │                    │                │                   │
  │─ Configure ─────▶│                    │                │                   │
  │─ Add Custom ────▶│─ Validate ────────▶│                │                   │
  │                  │─ Store ───────────▶│───────────────▶│                   │
  │─ Generate ──────▶│                    │                │                   │
  │                  │─ Calculate Dates ─▶│◀───── Read ────│                   │
  │                  │                    │──── For each ──▶│                   │
  │                  │                    │     checkup     │                   │
  │                  │                    │◀─── Store ──────│                   │
  │                  │◀─ Render Step 3 ───│                │                   │
  │                  │                    │                │                   │
  │─ Download ──────▶│                    │                │                   │
  │                  │─ Generate ICS ────────────────────────────────────────▶│
  │                  │                    │                │  Build VCALENDAR │
  │                  │                    │                │  Add VEVENTs     │
  │                  │                    │                │  Create Blob     │
  │                  │◀─────────────────────────────────────── Return Blob ───│
  │◀─ Download File ─│                    │                │                   │
  │                  │                    │                │                   │
```

### 6.2 Scenario: Age-Based Filtering

```
1. User enters birth year 2000 (age 26)
   │
   ▼
2. System calculates: age = 2026 - 2000 = 26
   │
   ▼
3. For each checkup definition:
   │
   ├─▶ Dental care (minAge: 18) → CURRENT ✓
   ├─▶ Skin screening (minAge: 35) → FUTURE (in 9 years)
   ├─▶ Eye exam (minAge: 40) → FUTURE (in 14 years)
   └─▶ Colonoscopy (minAge: 50) → FUTURE (in 24 years)
   │
   ▼
4. Render two sections:
   - Current: Show with date inputs
   - Future: Show with target year, no date inputs
```

### 6.3 Scenario: ICS Generation

```
1. User clicks "Generate Calendar"
   │
   ▼
2. For each enabled checkup:
   │
   ├─▶ Has last date?
   │   ├─ YES → Calculate: lastDate + interval
   │   └─ NO  → Schedule: today + 1 month
   │
   ▼
3. For each future checkup:
   │
   └─▶ Calculate: today + (minAge - currentAge) years
   │
   ▼
4. Sort all events by date
   │
   ▼
5. Build ICS structure:
   BEGIN:VCALENDAR
   VERSION:2.0
   ...
   BEGIN:VEVENT
   UID:...
   DTSTART:20260615
   SUMMARY:Hautkrebs-Screening
   ...
   END:VEVENT
   ...
   END:VCALENDAR
   │
   ▼
6. Create Blob and download
```

---

## 7. Deployment View

### 7.1 Infrastructure

```
┌─────────────────────────────────────────────────────┐
│              GitHub Infrastructure                   │
│                                                      │
│  ┌────────────────────────────────────────────┐    │
│  │         GitHub Repository                   │    │
│  │  • main branch                              │    │
│  │  • index.html (application file)            │    │
│  │  • docs/ (documentation)                    │    │
│  └────────────┬───────────────────────────────┘    │
│               │                                      │
│               │ Git Push                             │
│               ▼                                      │
│  ┌────────────────────────────────────────────┐    │
│  │      GitHub Pages Build System             │    │
│  │  • Detects changes in main branch          │    │
│  │  • Publishes static files                  │    │
│  │  • Serves via CDN                          │    │
│  └────────────┬───────────────────────────────┘    │
│               │                                      │
└───────────────┼──────────────────────────────────────┘
                │
                │ HTTPS
                ▼
┌─────────────────────────────────────────────────────┐
│         User's Web Browser                          │
│  • Chrome, Firefox, Safari, Edge                    │
│  • Desktop, Mobile, Tablet                          │
└─────────────────────────────────────────────────────┘
```

### 7.2 Deployment Process

1. **Development**
   - Edit `index.html` locally
   - Test in browser (open file directly or use local server)

2. **Commit & Push**
   ```bash
   git add index.html
   git commit -m "Update: description of changes"
   git push origin main
   ```

3. **Automatic Deployment**
   - GitHub Pages detects push to main branch
   - Rebuilds site (typically 1-2 minutes)
   - Site available at: `https://[username].github.io/[repo-name]/`

4. **Verification**
   - Open URL in browser
   - Test functionality
   - Check across different devices/browsers

### 7.3 Technical Mapping

| Component | Deployment Unit | Technology |
|-----------|-----------------|------------|
| Application | Single HTML file | HTML5, CSS3, JavaScript ES6+ |
| Hosting | GitHub Pages | Static file server, CDN |
| Domain | username.github.io | GitHub subdomain (or custom) |
| SSL/TLS | Automatic | GitHub Pages certificate |
| Fonts | Google Fonts CDN | External CDN, HTTPS |

---

## 8. Crosscutting Concepts

### 8.1 Domain Model

```
┌─────────────────┐
│      User       │
│ ─────────────── │
│ - gender        │
│ - birthYear     │
│ - age           │
│ - insuranceType │
└────────┬────────┘
         │ has
         │
         ▼
┌─────────────────┐      ┌──────────────────┐
│ CheckupHistory  │◄─────│ Checkup          │
│ ─────────────── │  ref │ ──────────────── │
│ - checkupId     │      │ - id             │
│ - lastDate      │      │ - name           │
│ - enabled       │      │ - description    │
└─────────────────┘      │ - minAge         │
                         │ - maxAge         │
         │               │ - intervals {}   │
         │               └──────────────────┘
         │ generates
         ▼
┌─────────────────┐
│ CalendarEvent   │
│ ─────────────── │
│ - name          │
│ - date          │
│ - description   │
│ - interval      │
│ - urgent        │
│ - future        │
└─────────────────┘
```

### 8.2 Security Concepts

| Aspect | Implementation | Rationale |
|--------|----------------|-----------|
| **Data Privacy** | No server transmission | All processing in browser, data never leaves device |
| **HTTPS** | GitHub Pages enforced | Prevents MITM attacks, secure font loading |
| **No Cookies** | Session-only state | No tracking, no stored personal data |
| **XSS Prevention** | No eval(), sanitized inputs | Prevents code injection |
| **Content Security** | Inline scripts only | No external script loading except fonts |

### 8.3 User Experience Patterns

**Progressive Disclosure:**
- Show only relevant information per step
- Hide complexity until needed
- Reveal future checkups separately from current ones

**Immediate Feedback:**
- Validation on blur
- Visual state changes on interaction
- Progress indicator always visible

**Error Prevention:**
- Input constraints (min/max values)
- Required field validation
- Descriptive placeholders

**Flexibility:**
- Allow skipping non-essential checkups
- Support custom checkups
- "Longer ago" option to avoid fake dates

### 8.4 Internationalization (i18n)

**Current State:**
- Hard-coded German text
- German date formats
- German healthcare context

**Future Considerations:**
- Extract strings to constants
- Parameterize date formatting
- Adapt checkup definitions per country
- Language selector in UI

### 8.5 Testing Strategy

**Manual Testing:**
- Cross-browser testing (Chrome, Firefox, Safari, Edge)
- Responsive design testing (mobile, tablet, desktop)
- End-to-end user journey testing
- Edge case testing (min/max ages, empty inputs)

**Automated Testing (Future):**
- Unit tests for calculation functions
- Integration tests for date generation
- ICS format validation
- Visual regression testing

**No Current Test Framework:**
- Trade-off: Simplicity vs. test coverage
- Single file approach makes unit testing harder
- Comprehensive manual test checklist in spec

---

## 9. Architecture Decisions

### ADR-001: Single HTML File Architecture

**Status:** Accepted

**Context:**
Need simple deployment to GitHub Pages without build process.

**Decision:**
Implement entire application in single HTML file with embedded CSS and JavaScript.

**Consequences:**
- ✅ Zero build complexity
- ✅ Trivial deployment
- ✅ Guaranteed self-contained
- ❌ Harder to test individual components
- ❌ Larger initial file size
- ❌ Less modular code organization

**Alternatives Considered:**
- Multiple files (HTML, CSS, JS separate) → Rejected: Adds complexity
- Build process with bundler → Rejected: Overkill for project size
- Framework (React/Vue) → Rejected: Unnecessary dependencies

---

### ADR-002: No Framework, Vanilla JavaScript

**Status:** Accepted

**Context:**
Balance between development speed and maintainability.

**Decision:**
Use vanilla JavaScript ES6+ without any frameworks or libraries.

**Consequences:**
- ✅ No dependency management
- ✅ No version conflicts
- ✅ Smaller file size (~100KB vs ~500KB+ with framework)
- ✅ Longer browser support
- ❌ More boilerplate code
- ❌ Manual DOM manipulation
- ❌ No reactive state management

**Alternatives Considered:**
- React → Rejected: Adds 40KB+ and build complexity
- Vue → Rejected: Still adds dependency
- jQuery → Rejected: Outdated, unnecessary with modern browsers

---

### ADR-003: Client-Side Only Processing

**Status:** Accepted

**Context:**
Privacy concerns and deployment simplicity.

**Decision:**
All data processing happens in the browser, no server-side component.

**Consequences:**
- ✅ Maximum privacy (data never leaves device)
- ✅ Works offline after initial load
- ✅ No backend costs
- ✅ Immediate response, no latency
- ❌ No data persistence
- ❌ No multi-device sync
- ❌ Limited to browser capabilities

**Alternatives Considered:**
- Backend API → Rejected: Privacy concerns, hosting costs
- LocalStorage persistence → Rejected: Not required for MVP
- Cloud sync → Rejected: Out of scope

---

### ADR-004: ICS File Format for Calendar Export

**Status:** Accepted

**Context:**
Need universal calendar compatibility.

**Decision:**
Generate iCalendar (.ics) files per RFC 5545 standard.

**Consequences:**
- ✅ Universal compatibility (Google, Apple, Outlook, etc.)
- ✅ Standard format, well-documented
- ✅ Supports recurring events
- ✅ Includes reminders
- ❌ Text-based format (larger than binary)
- ❌ Manual import step for users

**Alternatives Considered:**
- Direct calendar API integration → Rejected: Requires auth, complex
- Custom JSON format → Rejected: No calendar app support
- CSV format → Rejected: Limited calendar support

---

### ADR-005: Age-Based Checkup Filtering

**Status:** Accepted

**Context:**
Users at different ages need different checkups.

**Decision:**
Separate checkups into "current" (age-appropriate now) and "future" (will be relevant later).

**Consequences:**
- ✅ Shows only relevant checkups
- ✅ Plans for future automatically
- ✅ Reduces cognitive load
- ❌ More complex rendering logic
- ❌ Needs clear visual separation

**Alternatives Considered:**
- Show all checkups always → Rejected: Overwhelming for young users
- Only show current checkups → Rejected: Users want to plan ahead
- Manual age filtering → Rejected: Extra user burden

---

### ADR-006: Three Insurance Type Options

**Status:** Accepted

**Context:**
German healthcare has different checkup coverage.

**Decision:**
Support GKV (statutory), PKV (private), and Recommended (best practice) intervals.

**Consequences:**
- ✅ Accurate for German context
- ✅ Shows coverage differences
- ✅ Educates users about options
- ❌ More complex data model
- ❌ Requires medical accuracy
- ❌ Hard to extend to other countries

**Alternatives Considered:**
- Single interval set → Rejected: Not accurate for all users
- Country selection → Rejected: Out of scope for v1
- Insurance company selection → Rejected: Too granular

---

### ADR-007: No Data Persistence

**Status:** Accepted

**Context:**
Trade-off between functionality and privacy/simplicity.

**Decision:**
No localStorage or cookies, session-only state.

**Consequences:**
- ✅ Maximum privacy
- ✅ Simpler implementation
- ✅ No GDPR concerns
- ❌ Users must re-enter data each visit
- ❌ No saved configurations

**Alternatives Considered:**
- localStorage → Rejected: Privacy concern, adds complexity
- Cookies → Rejected: Requires consent banner
- URL parameters → Rejected: Sensitive data in URL

---

## 10. Quality Requirements

### 10.1 Quality Tree

```
Quality
├── Functional Suitability
│   ├── Functional Completeness (High)
│   ├── Functional Correctness (High)
│   └── Functional Appropriateness (Medium)
├── Performance Efficiency
│   ├── Time Behavior (Medium)
│   ├── Resource Utilization (Low)
│   └── Capacity (Low)
├── Compatibility
│   ├── Co-existence (Low)
│   └── Interoperability (High)
├── Usability
│   ├── Appropriateness Recognizability (High)
│   ├── Learnability (High)
│   ├── Operability (High)
│   ├── User Error Protection (High)
│   └── Aesthetics (Medium)
├── Reliability
│   ├── Maturity (Medium)
│   ├── Availability (High)
│   ├── Fault Tolerance (Medium)
│   └── Recoverability (Low)
├── Security
│   ├── Confidentiality (High)
│   ├── Integrity (Medium)
│   └── Accountability (Low)
├── Maintainability
│   ├── Modularity (Medium)
│   ├── Reusability (Low)
│   ├── Analyzability (High)
│   ├── Modifiability (High)
│   └── Testability (Medium)
└── Portability
    ├── Adaptability (High)
    ├── Installability (High)
    └── Replaceability (Low)
```

### 10.2 Quality Scenarios

#### Scenario 1: Fast Page Load (Performance)
- **Source:** User opens page on mobile 3G connection
- **Stimulus:** Initial page request
- **Environment:** Mobile browser, 3G network
- **Response:** Page fully interactive
- **Measure:** < 3 seconds to interactive

**Current Achievement:** ~1-2 seconds on 3G (single 150KB file + fonts)

---

#### Scenario 2: Complete Journey Without Help (Usability)
- **Source:** New user, no healthcare knowledge
- **Stimulus:** Wants to create checkup calendar
- **Environment:** Desktop browser, first time use
- **Response:** Successfully downloads .ics file
- **Measure:** < 5 minutes, no external help needed

**Current Achievement:** ~3 minutes average based on informal testing

---

#### Scenario 3: Calendar Import Works Everywhere (Interoperability)
- **Source:** User with any major calendar app
- **Stimulus:** Imports downloaded .ics file
- **Environment:** Google Calendar, Apple Calendar, Outlook, etc.
- **Response:** All events import correctly with dates and reminders
- **Measure:** 100% success rate across major platforms

**Current Achievement:** Tested successfully on Google/Apple/Outlook

---

#### Scenario 4: Privacy-Conscious User (Security)
- **Source:** User concerned about data privacy
- **Stimulus:** Uses application with personal health data
- **Environment:** Any browser with developer tools
- **Response:** No data leaves browser
- **Measure:** Zero network requests (except fonts), no cookies, no tracking

**Current Achievement:** Verified via network tab inspection

---

#### Scenario 5: Medical Guidelines Change (Modifiability)
- **Source:** German healthcare updates screening recommendations
- **Stimulus:** Need to update checkup intervals
- **Environment:** Development environment
- **Response:** Update checkup definitions
- **Measure:** < 30 minutes to update and deploy

**Current Achievement:** Simple object literal update, no code changes needed

---

#### Scenario 6: Works on Any Device (Adaptability)
- **Source:** Users with various devices
- **Stimulus:** Access on phone, tablet, or desktop
- **Environment:** Any screen size (320px - 2560px width)
- **Response:** Fully functional and readable
- **Measure:** All features accessible, text readable, buttons touchable

**Current Achievement:** Responsive CSS tested on multiple devices

---

### 10.3 Quality Attribute Scenarios Summary

| Quality Attribute | Priority | Status | Trade-offs |
|-------------------|----------|--------|------------|
| **Usability** | ★★★★★ | ✅ Met | Simplicity over features |
| **Interoperability** | ★★★★★ | ✅ Met | Standard format, limited customization |
| **Privacy** | ★★★★★ | ✅ Met | No persistence, no sync |
| **Modifiability** | ★★★★☆ | ✅ Met | Clear data structures, single file |
| **Performance** | ★★★☆☆ | ✅ Met | Small file, no optimization needed |
| **Testability** | ★★★☆☆ | ⚠️ Partial | Manual testing only, no framework |
| **Reusability** | ★★☆☆☆ | ❌ Not focus | Specific use case, German context |

---

## 11. Risks and Technical Debt

### 11.1 Risks

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| **R-1: Medical Information Outdated** | Medium | High | Add disclaimer, link to official sources, regular reviews |
| **R-2: Browser API Changes** | Low | Medium | Use stable APIs only, test regularly |
| **R-3: Google Fonts CDN Unavailable** | Low | Low | Fonts are progressive enhancement, app works without |
| **R-4: GitHub Pages Discontinuation** | Very Low | Medium | Easy to migrate to any static host |
| **R-5: Calendar Format Incompatibility** | Low | High | Follow RFC 5545 strictly, test major platforms |
| **R-6: User Data Loss** | High | Low | Expected behavior (no persistence), clearly communicated |

### 11.2 Technical Debt

| Debt Item | Type | Effort to Fix | Priority |
|-----------|------|---------------|----------|
| **TD-1: No Unit Tests** | Testing | High | Medium |
| **TD-2: Hard-coded German Text** | i18n | Medium | Low |
| **TD-3: Single File Monolith** | Architecture | Very High | Low |
| **TD-4: Manual Test Process** | Process | Medium | Medium |
| **TD-5: No Error Logging** | Observability | Low | Low |
| **TD-6: Limited Input Validation** | Quality | Low | Medium |
| **TD-7: No Analytics** | Product | Low | Low |

### 11.3 Known Limitations

| Limitation | Impact | Workaround | Future Fix |
|------------|--------|------------|------------|
| **No Data Persistence** | Users re-enter data each time | Download .ics and keep it | Add localStorage option |
| **German Only** | Limits international use | - | Add i18n support |
| **No Recurring Events** | Single events only | Users manually repeat import | Generate recurring events in .ics |
| **Manual Calendar Import** | Extra user step | Clear instructions | Investigate calendar API integration |
| **No Mobile App** | Web-only experience | PWA-capable | Create PWA manifest |

### 11.4 Future Improvement Opportunities

**Short-term (Low effort):**
1. Add PWA manifest for "Add to Home Screen"
2. Improve input validation with better error messages
3. Add print-friendly CSS for checkup list
4. Include data export/import for backup

**Medium-term (Medium effort):**
5. Add unit tests for core calculation logic
6. Implement localStorage for optional data persistence
7. Create English language version
8. Add more comprehensive checkup information

**Long-term (High effort):**
9. Multi-language support with translation framework
10. Country-specific checkup guidelines
11. Mobile app wrapper (React Native / Capacitor)
12. Backend sync option (opt-in) for multi-device

---

## 12. Glossary

| Term | Definition |
|------|------------|
| **arc42** | Template for architecture documentation |
| **Checkup** | Preventive health examination/screening |
| **GKV** | Gesetzliche Krankenversicherung (German statutory health insurance) |
| **PKV** | Private Krankenversicherung (German private health insurance) |
| **ICS** | iCalendar file format (.ics extension) |
| **RFC 5545** | Internet Calendaring and Scheduling Core Object Specification |
| **VEVENT** | Calendar event in iCalendar format |
| **VCALENDAR** | Calendar container in iCalendar format |
| **GitHub Pages** | Static site hosting service by GitHub |
| **CDN** | Content Delivery Network |
| **Blob** | Binary Large Object (browser API for file data) |
| **SPA** | Single-Page Application |
| **Progressive Enhancement** | Design approach where basic functionality works everywhere, enhancements where supported |
| **Client-Side** | Code that runs in the user's browser |
| **Static Site** | Website with no server-side processing |
| **Responsive Design** | Design that adapts to different screen sizes |

---

## About This Document

**Template:** arc42 v8.2  
**Language:** English  
**Project:** Health Checkup Scheduler  
**Version:** 1.0  
**Last Updated:** January 2026  
**Status:** Complete  

**Document Owner:** Project Maintainer  
**Contributors:** Architecture Team  

**Change History:**

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | Jan 2026 | Initial | Complete architecture documentation |

---

## References

- **Technical Specification:** `SPECIFICATION.md` - Detailed implementation requirements
- **RFC 5545:** https://tools.ietf.org/html/rfc5545 - iCalendar specification
- **GitHub Pages:** https://pages.github.com/ - Deployment platform documentation
- **arc42:** https://arc42.org/ - Architecture documentation template
- **German Health Guidelines:** Various sources for checkup recommendations

---

*This architecture document follows the arc42 template. Some sections may be brief where complexity is low, which is appropriate for a focused single-purpose application.*
