# Issues und Entscheidungen - Health Checkup Scheduler

## Bekannte Einschränkungen (beim Start dokumentiert)

### ISSUE-001: Testing-Limitation
**Typ:** Technische Einschränkung
**Status:** Akzeptiert
**Beschreibung:** Playwright-MCP Testing ohne traditionelles Test-Framework bedeutet manuelle Testausführung
**Entscheidung:** Akzeptiert - Tests werden manuell mit Playwright-MCP durchgeführt und dokumentiert
**Alternative:** Separates Test-Framework → abgelehnt wegen "no build process" Constraint

### ISSUE-002: Single-File Constraint
**Typ:** Architektur
**Status:** Akzeptiert
**Beschreibung:** Alle Code in einer Datei macht Organisation schwieriger
**Entscheidung:** Akzeptiert - entspricht Architekturvorgabe für GitHub Pages Deployment
**Begründung:** Einfaches Deployment, keine Build-Pipeline nötig

### ISSUE-003: Medizinische Daten-Genauigkeit
**Typ:** Inhalt/Haftung
**Status:** Offen → Disclaimer erforderlich
**Beschreibung:** Medizinische Empfehlungen müssen korrekt und aktuell sein
**Maßnahme:**
- Disclaimer hinzufügen: "Diese Anwendung bietet nur Orientierung, konsultieren Sie Ihren Arzt"
- Quelle der Empfehlungen dokumentieren
- Jährliche Überprüfung vorsehen

### ISSUE-004: Datumsberechnung Month Overflow
**Typ:** Technisches Risiko
**Status:** Zu testen
**Beschreibung:** Month overflow muss sorgfältig getestet werden (z.B. 31. Jan + 1 Monat)
**Testfälle:**
- 31.01.2025 + 1 Monat = 28.02.2025 (Schaltjahr-unabhängig)
- 31.01.2024 + 1 Monat = 29.02.2024 (Schaltjahr)
- 31.05.2025 + 1 Monat = 30.06.2025
**Status:** Noch nicht getestet

### ISSUE-005: ICS Kompatibilität
**Typ:** Technisches Risiko
**Status:** Zu verifizieren
**Beschreibung:** RFC 5545 Compliance muss sorgfältig geprüft werden
**Testfälle:**
- Import in Google Calendar
- Import in Apple Calendar
- Import in Outlook
- Import in Thunderbird
**Status:** Noch nicht getestet

---

## Implementierungs-Entscheidungen

### DECISION-001: Version Nummer im Footer
**Datum:** 2026-01-04
**Entscheidung:** Version wird im Footer angezeigt als "v0.1.0" → "v1.0.0"
**Format:** Semantic Versioning (MAJOR.MINOR.PATCH)
**Position:** Rechts oder zentriert im Footer

---

## Gefundene Issues während Entwicklung

*(Wird während der Implementierung ergänzt)*

---

## Test-Protokoll

### Test-Session 1: 2026-01-04 - Phase 1 Setup & Foundation

#### Phase 1.1: Basic HTML Structure ✅
- [x] Page loads with correct title: "Gesundheitsvorsorge Planer"
- [x] Google Fonts loaded: "DM Serif Display" erkannt
- [x] Version in footer visible: "Version 0.1.0"
- [x] German lang attribute set: lang="de"
**Status:** Alle Tests bestanden

#### Phase 1.2: CSS Variables and Base Styles ✅
- [x] CSS Variables definiert: --primary #2a5a4a, --accent #e8956f, --bg #faf8f5
- [x] Background color angewendet: rgb(250, 248, 245)
- [x] Text color korrekt: rgb(26, 26, 26)
- [x] Font-family angewendet: Karla für body, DM Serif Display für headings
**Status:** Alle Tests bestanden

#### Phase 1.3: Global State Structure ✅
- [x] userData existiert und ist zugänglich
- [x] calendarEvents existiert und ist Array
- [x] userData hat korrekte Struktur (gender, birthYear, age, insuranceType, checkups, enabledCheckups, customCheckups)
**Status:** Alle Tests bestanden

**Phase 1 Gesamt-Status:** ✅ Abgeschlossen

---

### Test-Session 2: 2026-01-04 - Phase 2 Step Navigation System

#### Phase 2.1: HTML for 3 Steps ✅
- [x] Step 1 container existiert im DOM
- [x] Step 2 container existiert im DOM
- [x] Step 3 container existiert im DOM
- [x] Alle Buttons vorhanden (Next, Back, Restart, Download)
- [x] Progress Indicator mit 3 Steps vorhanden
- [x] Step 1 ist initial aktiv, Steps 2+3 sind versteckt
**Status:** Alle Tests bestanden

#### Phase 2.2: Navigation Logic ✅
- [x] Click "Weiter" in Step 1 → navigiert zu Step 2
- [x] Step 2 wird angezeigt, Step 1 wird versteckt
- [x] Progress Indicator: Step 1 = completed, Step 2 = active
- [x] Click "Zurück" in Step 2 → navigiert zu Step 1
- [x] Click "Weiter" in Step 2 → navigiert zu Step 3
- [x] Progress Indicator: Step 1+2 = completed, Step 3 = active
- [x] Click "Zurück" in Step 3 → navigiert zu Step 2
- [x] Restart-Funktion: userData wird zurückgesetzt (alle Felder null/leer)
- [x] Restart-Funktion: calendarEvents Array wird geleert
- [x] Restart-Funktion: Navigation zu Step 1
- [x] Progress Indicator wird korrekt zurückgesetzt
**Status:** Alle Tests bestanden

**Phase 2 Gesamt-Status:** ✅ Abgeschlossen

---

### Test-Session 3: 2026-01-04 - Phase 3 Step 1 Basic Information Form

#### Phase 3.1: Form Inputs ✅
- [x] Gender Radio Buttons vorhanden (Männlich/Weiblich)
- [x] Birth Year Number Input vorhanden (min: 1920, max: 2024)
- [x] Insurance Type Dropdown vorhanden (GKV/PKV/Empfohlen)
- [x] Alle Felder mit required-Marker (*) gekennzeichnet
- [x] "Weiter" Button initial disabled
**Status:** Alle Tests bestanden

#### Phase 3.2: Validation and Data Storage ✅
- [x] Validation: Leeres Formular → Button bleibt disabled
- [x] Validation: Nur Gender gewählt → Button bleibt disabled
- [x] Validation: Alle Felder ausgefüllt → Button wird enabled
- [x] Click "Weiter" mit vollständigem Formular → Navigation zu Step 2
- [x] userData.gender korrekt gespeichert: "male"
- [x] userData.birthYear korrekt gespeichert: 1972
- [x] userData.insuranceType korrekt gespeichert: "pkv"
- [x] userData.age korrekt berechnet: 54 (2026 - 1972)
- [x] Restart setzt Formular zurück (alle Felder leer)
- [x] Restart setzt userData zurück (alle Werte null)
- [x] Restart setzt Button zurück auf disabled
**Status:** Alle Tests bestanden

**Test-Daten verwendet:**
- Gender: male (Männlich)
- Birth Year: 1972
- Insurance Type: pkv (PKV - Privat)
- Berechnetes Alter: 54 Jahre

**Phase 3 Gesamt-Status:** ✅ Abgeschlossen

---

### Test-Session 4: 2026-01-04 - Phase 4 Checkup Definitions and Filtering

#### Phase 4.1: Checkup Definitions ✅
- [x] checkupDefinitions Object existiert
- [x] Male checkups: 9 Einträge (gp-checkup, colonoscopy, stool-test, skin-screening, prostate-dre, psa-test, eye-exam, cardio-extended, dental)
- [x] Female checkups: 12 Einträge (zusätzlich: gynecological, hpv-test, mammography, breast-ultrasound, osteoporosis)
- [x] Alle Checkups haben korrekte Struktur (id, name, intervals{gkv, pkv, recommended}, description, minAge, optional maxAge)
- [x] Intervals korrekt: gkv/pkv/recommended in Monaten, null = nicht abgedeckt
**Status:** Alle Tests bestanden

#### Phase 4.2: Age-Based Filtering Logic ✅
- [x] Filter mit Alter 25, male: 2 current (gp-checkup, dental), 7 future
- [x] Filter mit Alter 52, male: 9 current (alle), 0 future
- [x] Filter mit Alter 65, female: 12 current (inkl. mammography)
- [x] Filter mit Alter 75, female: 11 current (OHNE mammography wegen maxAge 69)
- [x] maxAge-Logic funktioniert korrekt (mammography nur bis 69 Jahre)
- [x] Future checkups werden korrekt identifiziert (age < minAge)
**Status:** Alle Tests bestanden

**Testfälle:**
- Age 25 male: Nur Basis-Checks (GP, Dental), alle anderen in Zukunft
- Age 52 male: Alle 9 männlichen Checkups aktuell
- Age 65 female: Alle 12 weiblichen Checkups inkl. Mammographie
- Age 75 female: 11 Checkups, Mammographie ausgeschlossen (maxAge)

**Phase 4 Gesamt-Status:** ✅ Abgeschlossen

---

### Test-Session 5: 2026-01-04 - Phase 5 Step 2 Checkup Configuration

#### Phase 5.1-5.3: Rendering Current, Future, Custom Checkups ✅
- [x] Step 2 Navigation: Alle 9 aktuellen Checkups werden gerendert (male, age 54, PKV)
- [x] Alle Checkboxen initial checked
- [x] Date Inputs vorhanden für jedes aktuelle Checkup
- [x] "Länger her" Checkbox vorhanden
- [x] Intervall-Anzeige korrekt (z.B. "Alle 12 Monate" für PKV)
- [x] Custom Checkup Formular vorhanden
**Status:** Alle Tests bestanden

#### Phase 5.4: Checkup State Management ✅
- [x] Checkbox deaktivieren: Card wird disabled, Date Input disabled, "Länger her" disabled
- [x] Checkbox aktivieren: Card wird enabled, Inputs werden enabled
- [x] Custom Checkup hinzufügen: "Vitamin D Test", 12 Monate → erscheint in Liste
- [x] Custom Checkup entfernen: Button "Entfernen" funktioniert
- [x] userData.customCheckups wird korrekt aktualisiert
**Status:** Alle Tests bestanden

**Test-Daten:**
- User: male, 54 Jahre, PKV
- 9 aktuelle Checkups gerendert
- Custom: "Vitamin D Test", 12 Monate
- Toggle: colonoscopy disabled/enabled erfolgreich

**Phase 5 Gesamt-Status:** ✅ Abgeschlossen

---

### Test-Session 6: 2026-01-04 - Phase 6 Calendar Generation Logic

#### Phase 6.1-6.2: Date Calculation and Calendar Generation ✅
- [x] generateCalendar() function exists and executes
- [x] Calendar events array populated with correct number of events
- [x] Events sorted by date chronologically
- [x] Date calculation for current checkups: lastDate + interval months
- [x] Date calculation for "länger her": 1 month from now
- [x] Date calculation for future checkups: current date + (minAge - currentAge) years
- [x] Custom checkups included in calendar
- [x] formatDateGerman() formats dates correctly (DD. Month YYYY)
- [x] formatDateICS() formats dates correctly (YYYYMMDD)
- [x] generateICSFile() creates RFC 5545 compliant structure
- [x] ICS includes VCALENDAR wrapper
- [x] ICS includes VEVENT for each checkup
- [x] ICS includes 7-day reminders (TRIGGER:-P7D)
- [x] downloadICS() triggers file download

**Status:** Alle Tests bestanden

**Phase 6 Gesamt-Status:** ✅ Abgeschlossen

---

### Test-Session 7: 2026-01-04 - Phase 7 Step 3 Summary & Download

#### Phase 7.1: Summary Display ✅
- [x] Summary section displays correct user data
- [x] Gender displayed correctly (Männlich/Weiblich)
- [x] Age displayed correctly with "Jahre" suffix (54 Jahre)
- [x] Insurance type displayed correctly (PKV (Privat))
- [x] Appointment count matches calendar events length (9)

#### Phase 7.2: Render Checkup Cards ✅
- [x] All 9 calendar events rendered as cards
- [x] Cards displayed in chronological order
- [x] Date formatting correct (4. Februar 2026 - German format)
- [x] Card structure: title, date, description complete
- [x] Urgent styling applied (red border) for events within 1 month
- [x] Future styling (yellow background) ready for future checkups
- [x] Future info box HTML ready for future checkups
- [x] No duplicate cards rendered

#### Phase 7.3-7.4: ICS Download ✅
- [x] Download button triggers ICS file download
- [x] Downloaded file has correct name: gesundheitsvorsorge.ics
- [x] ICS file content is valid RFC 5545 compliant
- [x] All 9 events included in ICS file
- [x] Each event has VEVENT structure
- [x] Date format correct: YYYYMMDD (20260204)
- [x] 7-day reminders included (TRIGGER:-P7D)
- [x] Unique UIDs for each event
- [x] German descriptions included

**Status:** Alle Tests bestanden

**Test-Daten:**
- User: male, 54 Jahre, PKV
- 9 events generated, all displayed
- Download successful: gesundheitsvorsorge.ics (135 lines)
- ICS structure verified: VCALENDAR > 9x VEVENT with VALARM

**Phase 7 Gesamt-Status:** ✅ Abgeschlossen
