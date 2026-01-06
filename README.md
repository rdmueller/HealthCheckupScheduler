# Gesundheitsvorsorge Planer

Eine Single-Page-Anwendung zur Erstellung eines personalisierten Vorsorgekalenders für gesetzliche und private Krankenversicherungen in Deutschland.

**Wichtig**: Dies ist ein Experiment für Spec-Driven-Development und keine echt App. Code und Dokumentation sind von einer KI erstellt worden.

## 🎯 Features

- **3-Schritt-Wizard**: Einfache Eingabe von Basisdaten → Auswahl von Vorsorgeuntersuchungen → Kalenderübersicht
- **21 Vorsorgeuntersuchungen**: 9 für Männer, 12 für Frauen
- **Altersbasierte Filterung**: Automatische Anzeige relevanter Untersuchungen basierend auf Alter (minAge/maxAge)
- **Versicherungsspezifische Intervalle**: Unterschiedliche Empfehlungen für GKV, PKV und Best Practice
- **Intelligente Terminstaffelung**: Prioritätsbasiertes System verhindert, dass alle Termine auf denselben Tag fallen
- **Eigene Untersuchungen**: Hinzufügen individueller Vorsorgeuntersuchungen
- **ICS-Export**: RFC 5545-konformer Kalender-Export für Google Calendar, Apple Calendar, Outlook, etc.
- **7-Tage-Erinnerungen**: Automatische Reminder vor jedem Termin
- **Responsive Design**: Optimiert für Desktop, Tablet und Mobile (ab 320px)
- **Privacy-First**: Alle Daten bleiben im Browser, keine Server-Kommunikation

## 🚀 Live Demo

[https://rdmueller.github.io/HealthCheckupScheduler/](https://rdmueller.github.io/HealthCheckupScheduler/)

## 📋 Unterstützte Vorsorgeuntersuchungen

### Für alle Geschlechter
- Hausarzt Check-up
- Darmspiegelung
- Immunologischer Stuhltest
- Hautkrebs-Screening
- Augenuntersuchung
- Erweiterter Herz-Kreislauf-Check
- Zahnvorsorge

### Zusätzlich für Männer
- Prostata-Untersuchung (DRU)
- PSA-Test

### Zusätzlich für Frauen
- Gynäkologische Untersuchung
- HPV-Test
- Mammographie
- Brust-Ultraschall
- Osteoporose-Screening

## 🛠️ Technologie

- **Single HTML File**: Keine Build-Pipeline erforderlich
- **Vanilla JavaScript**: Keine externen Frameworks oder Bibliotheken
- **CSS3**: Modern, responsive Design
- **Google Fonts**: DM Serif Display, Karla

## 📦 Installation & Deployment

### Lokal testen

```bash
# Repository klonen
git clone https://github.com/rdmueller/HealthCheckupScheduler.git
cd HealthCheckupScheduler

# Im Browser öffnen
open index.html
# oder
firefox index.html
# oder einfach Doppelklick auf index.html
```

### GitHub Pages Deployment

Die Anwendung ist automatisch über GitHub Pages verfügbar. Bei jedem Push auf `main` wird die Seite automatisch aktualisiert.

Alternativ manuell:
1. Repository Settings → Pages
2. Source: Deploy from a branch
3. Branch: `main`, Folder: `/ (root)`
4. Save

## 📖 Nutzung

1. **Schritt 1: Basisdaten**
   - Geschlecht wählen (Männlich/Weiblich)
   - Geburtsjahr eingeben
   - Krankenversicherung auswählen (GKV/PKV/Empfohlen)

2. **Schritt 2: Vorsorgeuntersuchungen**
   - Relevante Untersuchungen werden automatisch angezeigt
   - Datum der letzten Untersuchung eingeben (optional)
   - "Länger her" aktivieren, wenn keine genaue Angabe möglich
   - Eigene Untersuchungen hinzufügen
   - **Hinweis**: Ohne Datumsangaben werden Termine automatisch gestaffelt über mehrere Monate verteilt (Priorität 1-5), um realistische Planung zu ermöglichen

3. **Schritt 3: Zusammenfassung**
   - Übersicht aller geplanten Termine
   - Chronologisch sortiert
   - Farbcodierung: Rot = dringend (innerhalb 1 Monat), Gelb = zukünftig
   - "Kalender herunterladen" klicken für ICS-Export

## 🧪 Testing

Das Projekt wurde mit Test-Driven Development (TDD) unter Verwendung von Playwright-MCP entwickelt.

Getestete Szenarien:
- 25-jährige Frau, GKV
- 54-jähriger Mann, PKV
- 75-jährige Frau, Recommended (maxAge-Tests)
- 16-jähriger Mann, GKV (minAge-Tests)
- Custom Checkups
- Restart-Funktionalität
- Responsive Design

Alle 9 Test-Sessions dokumentiert in `docs/issues.md`.

## 📄 Dokumentation

- **[Technische Spezifikation](docs/spec.md)**: Detaillierte Feature-Beschreibung
- **[Architektur (arc42)](docs/arc42.md)**: Architektur-Dokumentation
- **[Issues & Decisions](docs/issues.md)**: Test-Protokolle und Entscheidungen

## ⚠️ Haftungsausschluss

Diese Anwendung bietet nur Orientierungshilfe. Bitte konsultieren Sie Ihren Arzt oder Ihre Ärztin für individuelle medizinische Beratung.

Die Angaben basieren auf allgemeinen Empfehlungen und können von individuellen medizinischen Bedürfnissen abweichen.

## 📜 Lizenz

MIT License - siehe [LICENSE](LICENSE)

## 👤 Autor

Ralf D. Müller

## 🤝 Beiträge

Dieses Projekt wurde mit [Claude Code](https://claude.com/claude-code) entwickelt.

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>

## 🔖 Version

Aktuelle Version: **1.0.0**

## 📊 Projekt-Status

✅ **Production Ready** - Vollständig getestet und deployment-ready für GitHub Pages
