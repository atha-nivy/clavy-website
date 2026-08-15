# CLAVY Founding Families — Rechtliche Einordnung & technisches Konzept

*Hinweis: Ich bin keine Anwältin. Die folgende Einschätzung ersetzt keine Rechtsberatung, sondern zeigt die relevanten Fragestellungen und wo eine spezialisierte Kanzlei (Datenschutz + FinTech, idealerweise mit Erfahrung bei Minderjährigendaten) eingebunden werden sollte — insbesondere weil CLAVY personenbezogene Daten von Kindern verarbeitet, ein Bereich, den die Aufsichtsbehörden besonders streng prüfen.*

---

## Teil 1 — DSGVO-, AI-Act- und Ethik-Bewertung

### 1.1 Grundeinordnung

Das Programm bewegt sich in einem sensiblen Dreieck: **Minderjährigendaten** (Art. 8 DSGVO, Erwägungsgrund 38), **Finanzdaten** (kein Sonderfall nach Art. 9 DSGVO, aber aufsichtsrechtlich sensibel) und **kommerzielle Weiterverwertung aggregierter Erkenntnisse** (potenzieller Zielkonflikt mit dem eigenen Markenversprechen "keine personenbezogenen Daten verkaufen"). Das Grundkonzept — Community statt Beta, keine Werbung an Kinder, keine Datenverkäufe, Transparenz — ist datenschutzfreundlich angelegt. Die Risiken liegen in den Details der Umsetzung, nicht in der Grundidee.

### 1.2 Wo zusätzliche Einwilligungen nötig werden

Die bestehende Kontoeröffnung deckt vermutlich schon die Verarbeitung für den eigentlichen Bankdienst ab. Für das Founding-Families-Programm kommen **separate, granulare Einwilligungen** hinzu, die nicht an die Kontonutzung gekoppelt sein dürfen (Kopplungsverbot, Art. 7 Abs. 4 DSGVO):

- **Teilnahme am Founding-Families-Programm** (Badge, Status, Vorteile) — vertretbar über berechtigtes Interesse oder Vertrag, sollte aber opt-in sein, da an Kommunikation gekoppelt.
- **Interviews / Produkttests** — explizite, widerrufbare Einwilligung pro Familie (Elternteil), getrennt vom Hauptvertrag.
- **Gründerupdates / Newsletter** — eigenständige Einwilligung nach UWG/ePrivacy-Grundsätzen (Double-Opt-In empfehlenswert).
- **Analytics zu Nutzungsverhalten der Kinder** (Missions, Sparziele, Wallet-Nutzung) — hier ist entscheidend, ob es sich um notwendige Produktanalyse (berechtigtes Interesse, datensparsam, pseudonymisiert) oder um darüberhinausgehendes Profiling handelt. Bei Profiling von Minderjährigen ist die Schwelle für ein berechtigtes Interesse hoch; im Zweifel Einwilligung der Eltern einholen.
- **Verwendung aggregierter Daten für externe Produkte** (Family Finance Report, Whitepaper, Marktanalysen für Banken) — auch wenn "nur aggregiert", braucht es eine **eigene Rechtsgrundlage und eigene Einwilligung**, weil die Familien beim Onboarding nicht automatisch damit rechnen, dass ihre (auch anonymisierten) Verhaltensdaten in kommerzielle Marktforschungsprodukte einfließen. Eigener Consent-Layer "Forschungsnutzung / Insights", jederzeit widerrufbar.

Wichtig: Bei ca. 500 Familien ist "aggregiert" nicht automatisch "anonym" im Rechtssinn. Kleine Kohorten sind re-identifizierbar (k-Anonymitäts-Problem), besonders wenn nach Segmenten (z. B. Region, Kinderanzahl, Einkommensklasse) aufgeschlüsselt wird. Das System erzwingt technisch einen **Mindestgruppengrößen-Schwellenwert von n≥20**, bevor irgendein aggregierter Wert exportiert oder angezeigt wird (siehe Entscheidungen unten).

### 1.3 AI Act — wo er relevant werden könnte

- **Art. 5 Abs. 1 lit. c (verbotene Praktik):** KI-Systeme, die gezielt Schwächen von Kindern ausnutzen, um deren Verhalten materiell zu verzerren und Schaden zu verursachen, sind verboten. Relevant, falls z. B. KI-gestützte Missions- oder Belohnungs-Personalisierung eingesetzt wird — sauber dokumentieren, dass Personalisierung dem Kindeswohl dient (Finanzbildung), nicht der Verhaltensmanipulation.
- **Transparenzpflichten (Art. 50):** Falls KI für Interview-Auswertung, Chat-Support oder automatisierte Kommunikation mit Familien genutzt wird, muss offengelegt werden, dass es sich um KI handelt.
- **Hochrisiko-Einstufung (Annex III):** Nur relevant, falls aus den Insights später ein System zur Bonitätsbewertung/Kreditwürdigkeitsprüfung natürlicher Personen entsteht. "Family Finance Report" als reines Marktforschungsprodukt fällt vermutlich nicht darunter — neu bewerten, sobald daraus scoring-ähnliche Funktionen für Banken entstehen.

### 1.4 Architektur, die heute schon vorbereitet werden sollte

- **Consent-Management als eigener Baustein**, nicht als Checkbox-Feld im User-Profil: versionierte Einwilligungstexte, Zeitstempel, granular pro Zweck, jederzeit einsehbar und widerrufbar.
- **Trennung operative Daten vs. Forschungs-/Insights-Daten**: eigene Read-Models/Pipelines, die erst nach Anonymisierung und Schwellenwert-Prüfung befüllt werden.
- **Event Sourcing / Audit Trail** — notwendig für Rechenschaftspflicht (Art. 5 Abs. 2 DSGVO) und für Auskunfts-/Löschanfragen.
- **Verzeichnis von Verarbeitungstätigkeiten (Art. 30 DSGVO)** frühzeitig um die neuen Zwecke erweitern.
- **DPIA** einplanen, sobald Profiling + Kinderdaten + neue Datenprodukte zusammenkommen (Art. 35 DSGVO).
- **Löschkonzept**, das auch Forschungsdaten und Insight-Datasets erfasst, nicht nur den Haupt-Account.

---

## Teil 2 — Domain Model

| Entität | Zweck |
|---|---|
| `FoundingFamily` | Mitgliedschaft im Programm, Status, Beitrittsdatum, Kohortennummer (von ~500) |
| `CommunityFeedback` | Freitext-Antworten auf Problem-Fragen (nicht Feature-Requests) |
| `ProductInterview` | Durchgeführtes Interview, verknüpft mit `InterviewInvitation` |
| `InterviewInvitation` | Einladung getrennt vom tatsächlichen Interview (Funnel-Tracking) |
| `RoadmapPreview` | Zugriff auf Vorschau-Inhalte (statt "Vote", da bewusst kein Feature-Voting) |
| `Badge` / `BadgeGrant` | Definition des Badges und Zuweisung an eine Familie |
| `Benefit` / `BenefitGrant` | Katalog möglicher Vorteile und deren Zuweisung |
| `PricingLock` | Dauerhafter Vorzugspreis, versioniert |
| `ResearchConsent` | Einwilligung zu Interviews/Produkttests |
| `AnalyticsConsent` | Getrennte Einwilligung zu Verhaltens-Analytics |
| `InsightDataset` | Aggregiertes, exportfähiges Datenprodukt inkl. Mindestgruppengröße-Prüfung |
| `FounderUpdate` / `FounderUpdateRead` | Community-Kommunikation und Lesestatus |
| `ConsentRecord` | Generischer, versionierter Consent-Typ als gemeinsame Basis |
| `DataProcessingPurpose` | Enum/Referenztabelle der Verarbeitungszwecke |

`RoadmapVote` bewusst **nicht** umgesetzt — passt nicht zur "kein Feature-Voting"-Philosophie; stattdessen `RoadmapPreview`.

---

## Teil 3 — Domain Events

`FoundingFamilyJoined`, `FeedbackSubmitted`, `InterviewInvited`, `InterviewAccepted`, `InterviewDeclined`, `InterviewCompleted`, `RoadmapPreviewViewed`, `FounderUpdatePublished`, `FounderUpdateRead`, `CommunityBadgeGranted`, `BenefitGranted`, `PricingLockAssigned`, `ResearchConsentGiven`, `ResearchConsentWithdrawn`, `AnalyticsConsentGiven`, `AnalyticsConsentWithdrawn`, `InsightDatasetGenerated`, `InsightDatasetExported`, `DataErasureRequested`, `DataErasureCompleted`.

Die Consent-/Erasure-Events sind der eigentliche Nachweis für DSGVO-Konformität und sollten von Anfang an mitgedacht werden, auch wenn das UI dafür erst später kommt.

---

## Teil 4 — Datenbankmodell (vereinfacht)

- `founding_families` (1:1 zu bestehender `families`-Tabelle, FK `family_id`)
- `community_feedback` (FK `family_id`, Freitext + Kategorisierung/Tagging für qualitative Auswertung)
- `interview_invitations` → `product_interviews` (1:0..1)
- `roadmap_previews` (FK `family_id`, `viewed_at`)
- `badges` (Katalog) ← `badge_grants` (FK `family_id`, `badge_id`)
- `benefits` (Katalog) ← `benefit_grants` (FK `family_id`, `benefit_id`, `valid_from`, `valid_until`)
- `pricing_locks` (FK `family_id`, `price_tier`, `locked_at`)
- `consent_records` (FK `family_id`, `purpose`, `version`, `given_at`, `withdrawn_at`)
- `insight_datasets` (Metadaten: Zeitraum, Zweck, Mindestgruppengröße, Freigabestatus) — **enthält selbst keine familienbezogenen Daten**, nur Aggregate
- `founder_updates` → `founder_update_reads` (FK `family_id`, `update_id`, `read_at`)
- `domain_events` (Event Store: `aggregate_id`, `event_type`, `payload`, `occurred_at`) als Append-Only-Log für Audit/Nachweis

---

## Teil 5 — Analytics & KPIs

**Funnel:** Signup → Onboarding abgeschlossen → erste Mission → erstes Sparziel → aktive Familie.

**Retention:** D7 / D30 / D90, separat für Founding-Cohorten vs. spätere Nutzer.

**Engagement:** Mission-Completion-Rate, Wallet-Nutzungsfrequenz, Sparziel-Erstellung vs. -Abschluss, App-Sessions pro Woche.

**Programm-Gesundheit:** Founder-Update Öffnungs-/Leserate, Interview-Annahmequote, Feedback-Rücklaufquote.

**Churn:** Inaktivität über X Wochen, Kündigungsgründe, Abwanderung nach Preisänderungen bei Nicht-Founding-Nutzern als Vergleich.

**North-Star-Kandidat:** "Aktive Familien mit mindestens einem laufenden Sparziel pro Monat."

---

## Entscheidungen (Stand 15.08.2026)

1. **`AnalyticsConsent` und `ResearchConsent` bleiben getrennt.** Mehr UI-Aufwand beim Onboarding, aber datenschutzfreundlicher und flexibler.
2. **Mindestgruppengröße für `insight_datasets`: n≥20.** Kein Export/keine Anzeige eines Aggregatwerts unterhalb dieser Schwelle — technisch im `InsightDataset`-Generierungsprozess erzwingen, nicht nur als Richtlinie dokumentieren.
3. **Zugriff auf `insight_datasets`: vorerst nur Atha.** Keine Außenschnittstelle für `insight_datasets`, solange das gilt. Ändert sich das später (z. B. Bank-Partner sollen direkt zugreifen), braucht es einen neuen Consent-Zweck plus eigene DPIA.
