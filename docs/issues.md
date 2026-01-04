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

*(Weitere Tests werden während der Implementierung ergänzt)*
