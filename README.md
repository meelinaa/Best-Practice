# Best Practices

## Inhaltsverzeichnis

1. Namenskonventionen
2. Lesbarkeit und Struktur
3. Methoden und Klassen
4. Fehlerbehandlung
5. Kommentare und Dokumentation
6. Clean Code Prinzipien
7. Teamarbeit und Kommunikation
8. Häufige Fehler und wie man sie vermeidet
9. Umgang mit Unsicherheit im Code
10. Code Reviews und Feedbackkultur

---

## 1. Namenskonventionen

- Namen sollten klar, beschreibend und konsistent sein.
- Für Klassen, Methoden und Properties wird **PascalCase** verwendet:
  ```csharp
  public class ProduktManager
  {
      public void LadeProdukte() {}
  }
  ```
- Für lokale Variablen und Parameter wird **camelCase** verwendet:
  ```csharp
  string produktName = "Tastatur";
  ```
- Abkürzungen sollten vermieden werden, sofern sie nicht allgemein etabliert sind (`id`, `url`, `xml`).
- Booleans beginnen idealerweise mit `ist`, `hat`, `kann` etc.:
  ```csharp
  bool istAktiv;
  bool hatRechte;
  ```

---

## 2. Lesbarkeit und Struktur

- Methoden sollten nur einen Zweck erfüllen (Single Responsibility).
- Logik kann durch Methodenaufrufe lesbarer gemacht werden:
  ```csharp
  public void BearbeiteAnfrage()
  {
      if (!IstBerechtigt()) return;
      LadeDaten();
      VerarbeiteDaten();
  }
  ```
- Einrückung und Klammerung sollte konsequent und teamweit einheitlich gehandhabt werden.
- Komplexe Bedingungen sollten ausgelagert oder dokumentiert werden:
  ```csharp
  if (IstBenutzerVerifiziertUndVolljährig())
  {
      ZugriffErlauben();
  }
  ```

---

## 3. Methoden und Klassen

- Methoden sollten möglichst kurz sein (idealerweise unter 30 Zeilen).
- Klassen sollten klar abgrenzbare Aufgabenbereiche behandeln.
- Wiederverwendbare Logik gehört in Hilfsmethoden oder Service-Klassen.
- Abhängigkeiten sollten über Konstruktoren oder Interfaces injiziert werden:
  ```csharp
  public class ProduktController
  {
      private readonly IProduktService _service;
      public ProduktController(IProduktService service)
      {
          _service = service;
      }
  }
  ```

---

## 4. Fehlerbehandlung

- Fehler sollten nicht verschluckt werden. Ziel ist es, aussagekräftige Logs zu erzeugen.
- Nicht alle Exceptions sollten global abgefangen werden:
  ```csharp
  try
  {
      VerbindungHerstellen();
  }
  catch (SqlException ex)
  {
      logger.LogError(ex, "Verbindung zur Datenbank fehlgeschlagen");
  }
  ```
- Im `finally`-Block können Ressourcen zuverlässig freigegeben werden (z. B. Streams oder Datenbankverbindungen).
- Bei Nutzung von `using` entfällt `finally`, da die Ressource automatisch geschlossen wird:
  ```csharp
  using (var reader = new StreamReader("daten.txt"))
  {
      var inhalt = reader.ReadToEnd();
  }
  ```

---

## 5. Kommentare und Dokumentation

- Kommentare sollen **nicht** beschreiben, *was* passiert, sondern **warum** etwas passiert.
- XML-Dokumentation wird für öffentliche Methoden verwendet, insbesondere bei Libraries und APIs:
  ```csharp
  /// <summary>
  /// Berechnet den finalen Preis inklusive Steuer.
  /// </summary>
  /// <param name="netto">Preis ohne Steuer</param>
  /// <returns>Bruttopreis</returns>
  public decimal BerechnePreis(decimal netto) => netto * 1.19m;
  ```
- Temporäre Kommentare sollten durch `TODO`, `FIXME` oder `HACK` markiert werden.

---

## 6. Clean Code Prinzipien

- **DRY – Don't Repeat Yourself:** Wiederholungen vermeiden, Methoden auslagern.
- **KISS – Keep It Simple, Stupid:** Einfache Lösungen bevorzugen.
- **YAGNI – You Aren’t Gonna Need It:** Nur das implementieren, was wirklich gebraucht wird.
- **SOLID Prinzipien:**
  - **S**: Eine Klasse hat nur eine Verantwortung.
  - **O**: Offen für Erweiterung, geschlossen für Veränderung.
  - **L**: Subtypen müssen durch ihre Basisklassen ersetzbar sein.
  - **I**: Keine unnötig großen Interfaces verwenden.
  - **D**: High-Level-Module sollten nicht von Low-Level-Modulen abhängig sein.

---

## 7. Teamarbeit und Kommunikation

- Arbeitsfortschritte sollten regelmäßig kommuniziert werden (z. B. in Dailys).
- Pull Requests sollten gut beschrieben und nicht zu groß sein.
- Commit-Nachrichten sollten Aussagekraft haben:
  ```
  feat: Suchfunktion für Kunden implementiert
  refactor: Duplikate im UserService entfernt
  fix: Fehler beim Speichern von Bestellungen behoben
  ```
- Bestehende Konventionen und Code-Stilrichtlinien des Teams sind verbindlich.

---

## 8. Häufige Fehler und wie man sie vermeidet

| Fehlerart                               | Vermeidungsempfehlung |
|----------------------------------------|------------------------|
| Magic Numbers (z. B. `if (5)`)         | `const int GRENZWERT = 5;` |
| Methoden mit zu vielen Parametern     | Objekt-Wrapper verwenden oder Methoden aufteilen |
| Nichtbeachtete `null`-Werte            | Nullprüfung vor Verwendung oder Null-Objektmuster |
| Zu große Klassen (God Classes)         | In kleinere Klassen mit eigener Verantwortung zerlegen |
| Leere `catch`-Blöcke                   | Mindestens Logging durchführen |
| Unerwartete Seiteneffekte in Methoden  | Methoden sollten klar dokumentieren, was sie verändern |
| Doppelte Logik an mehreren Stellen     | Gemeinsame Methoden oder Services verwenden |
| Fehlende Validierung von Eingaben      | Eingaben überprüfen und absichern (z. B. im Controller oder Service) |
| Direkter Zugriff auf `DateTime.Now`    | Abstraktion über `IDateTimeProvider` für testbare Zeitabfragen |
| Verwendung globaler Zustände (z. B. static List) | Zustand über Abhängigkeiten (z. B. DI) kontrollieren |
| Ungeprüfte externe Datenquellen        | Try-Catch und Validierung bei z. B. API-Calls, Datei-Input etc. |
| Logik in UI-Schichten implementiert     | Logik in Services oder Controller verlagern |
| Kommentare statt sauberen Code         | Code umstrukturieren, bis Kommentare nicht nötig sind |
| Wiederholte Verwendung gleicher Literal-Werte | Konstanten oder Konfigurationsdateien verwenden |
| Zu frühes Optimieren                    | Zuerst lesbaren und funktionalen Code schreiben, dann optimieren |
| Fehlendes Logging bei Problemen        | Fehler und Warnungen sinnvoll loggen, inklusive Kontextdaten |
| Inkompatible oder unlesbare Formatierungen | Code Formatierung über Formatierungsrichtlinien automatisieren |
| Fehlende Tests oder ungetestete Änderungen | Unit- oder Integrationstests schreiben, zumindest manuell prüfen |
| Mangelhafte Namensgebung               | Klare, sprechende Namen für Methoden, Variablen und Klassen verwenden |
| Vermischung von Zuständigkeiten        | Klassen nach dem Single-Responsibility-Prinzip strukturieren |

---

## 9. Umgang mit Unsicherheit im Code

- Bei Unsicherheit im Code oder Verständnis:
  - Den Code debuggen und Schritt für Schritt ausführen.
  - Dokumentation oder Kommentare prüfen.
  - Rücksprache mit Kolleginnen oder Kollegen halten.
  - Eigene Annahmen notieren und gezielt verifizieren.

- Für komplexe Entscheidungen lohnt sich eine schriftliche Dokumentation der Optionen.

---

## 10. Code Reviews und Feedbackkultur

- Bei Code Reviews:
  - Fokus auf Verständlichkeit, Testbarkeit und Sicherheit.
  - Nicht nur auf Formatierung achten, sondern auf Logik und Architektur.
  - Rückmeldungen sollten sachlich, respektvoll und konstruktiv formuliert werden.

- Beim Erhalten von Feedback:
  - Fragen stellen, wenn etwas unklar ist.
  - Kritik nicht persönlich nehmen – der Code wird bewertet, nicht die Person.
  - Änderungen nachvollziehen und dokumentieren.

