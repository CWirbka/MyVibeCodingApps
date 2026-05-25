# Wie du die Urlaubspackliste benutzt

## Einleitung

Die Urlaubspackliste hilft dir, für jede Reise automatisch eine vollständige Packliste zu erstellen — angepasst an Reisedauer, Wetter und geplante Aktivitäten. Du kannst Reisen im Archiv speichern, wiederholen und als Obsidian-Notiz exportieren.

---

## Voraussetzungen

- Ein moderner Webbrowser (Chrome, Firefox, Safari, Edge)
- JavaScript muss aktiviert sein
- Keine Registrierung erforderlich — alle Daten bleiben lokal in deinem Browser

---

## Schritt-für-Schritt-Anleitung

### 1. Neue Reise planen

1. Öffne `index.html` in deinem Browser.
2. Du befindest dich automatisch im Tab **🗺️ Reiseplaner**.
3. Fülle das Setup-Formular aus:
   - **Reisename** — z. B. "Teneriffa 2026"
   - **Dauer in Tagen** — bestimmt die Mengenberechnung für Kleidung
   - **Wetter** — Warm oder Kalt (steuert Schwimmzeug-Kategorie)
   - **Sport-Ausrüstung** — aktiviert Laufausrüstung und Polar-Ladegerät
4. Klicke auf **✨ Packliste generieren**.

![Screenshot: Setup-Formular mit ausgefüllten Feldern und Wetter-Auswahl](path/to/screenshots/placeholder-1.png)

**Tipp:** Die Kleidungsmengen werden automatisch berechnet:
- Socken & Unterhosen = Reisetage + 1
- T-Shirts = Reisetage
- Poloshirts = Reisetage ÷ 2 (aufgerundet)

---

### 2. Die Packliste abhaken

1. Tippe auf einen Eintrag oder die Checkbox, um ihn abzuhaken.
2. Abgehakte Einträge werden durchgestrichen und grau dargestellt.
3. Der **Fortschrittsbalken** oben zeigt dir den aktuellen Stand.
4. Bei 100 % erscheint die Meldung **"🎉 Alles eingepackt! Gute Reise!"**

![Screenshot: Aktive Packliste mit Fortschrittsbalken, abgehakten Einträgen und Kategorie-Cards](path/to/screenshots/placeholder-2.png)

**Kategorien ein-/ausklappen:** Klicke auf eine Kategorieüberschrift, um die Liste zu minimieren und Platz zu sparen.

---

### 3. Einträge hinzufügen oder löschen

**Hinzufügen:** Tippe in das Feld **"+ Element hinzufügen …"** am Ende einer Kategorie und drücke **Enter** oder klicke **Hinzufügen**.

**Löschen:** Fahre mit der Maus über einen Eintrag — das **×**-Symbol erscheint rechts. Auf Touch-Geräten ist es immer sichtbar.

![Screenshot: Kategorie mit Hover-Zustand auf einem Eintrag — sichtbares Löschen-Symbol](path/to/screenshots/placeholder-3.png)

---

### 4. Reise speichern und archivieren

1. Klicke auf **💾 Speichern** im Trip-Header, um die aktuelle Reise zu sichern.
2. Wechsle zum Tab **🗄️ Archiv**, um alle gespeicherten Reisen zu sehen.
3. Klicke **📂 Laden**, um eine Reise wieder zu aktivieren.
4. Klicke **🗑️ Löschen**, um eine Reise aus dem Archiv zu entfernen.

![Screenshot: Archiv-Tab mit zwei gespeicherten Reisen, Fortschrittsbalken und Lade-/Löschen-Buttons](path/to/screenshots/placeholder-4.png)

**Neue Reise starten:** Klicke auf **✕ Neu** im Trip-Header. Die aktuelle Reise wird verworfen — stelle sicher, dass du vorher gespeichert hast.

---

### 5. Basisliste anpassen

Die Basisliste ist dein persönliches Template. Jede Kategorie und jeder Eintrag darin wird automatisch in jede neue Packliste übernommen.

1. Wechsle zum Tab **📋 Basisliste**.
2. Füge Einträge zu bestehenden Kategorien hinzu (Eingabefeld am unteren Ende jeder Kategorie).
3. Lösche einzelne Einträge mit dem **×**-Symbol.
4. Erstelle eine neue Kategorie mit dem Formular **"➕ Neue Kategorie hinzufügen"** — wähle ein Emoji und gib einen Namen ein.
5. Klicke **💾 Speichern**, um Änderungen zu sichern.

![Screenshot: Basisliste-Tab mit Kategorien, Eintrags-Feldern und dem Neue-Kategorie-Formular](path/to/screenshots/placeholder-5.png)

**Hinweis:** Die drei Standard-Kategorien (🛡️ Gesundheit, 📄 Dokumente, ⭐ Basics) können nicht gelöscht werden, aber ihre Einträge lassen sich frei bearbeiten.

**Zurücksetzen:** Klicke **🔄 Basisliste zurücksetzen**, um alle eigenen Änderungen rückgängig zu machen und die Standardwerte wiederherzustellen.

---

### 6. Als Obsidian-Notiz exportieren

1. Öffne eine aktive Reise im **🗺️ Reiseplaner**.
2. Scrolle nach oben und klicke auf **📥 Für Obsidian exportieren (.md)**.
3. Eine `.md`-Datei wird heruntergeladen (Name: `<Reisename>-Packliste.md`).
4. Lege die Datei in deinen Obsidian-Vault.
5. Alle Einträge erscheinen als Aufgaben (`- [ ]` nicht abgehakt, `- [x]` abgehakt).

![Screenshot: Reiseplaner mit hervorgehobenem Obsidian-Export-Button](path/to/screenshots/placeholder-6.png)

Die exportierte Datei enthält YAML-Frontmatter mit Datum, Projektname und Tags, damit du sie in Obsidian einfach filtern und verknüpfen kannst.

---

## Häufige Fragen & Probleme

**Meine Daten sind nach dem Browser-Neustart weg.**
Die App speichert alle Daten im `localStorage` deines Browsers. Wenn du den Browser-Cache, den Verlauf oder die Website-Daten gelöscht hast, sind die Daten verloren. Nutze regelmäßig den **💾 Speichern**-Button und halte exportierte `.md`-Dateien als Backup.

**Ich kann eine der Standard-Kategorien nicht löschen.**
Die Kategorien Gesundheit, Dokumente und Basics sind schreibgeschützt. Du kannst aber jederzeit Einträge innerhalb dieser Kategorien hinzufügen oder löschen.

**Die Kleidungsmengen in der Packliste stimmen nicht.**
Die Mengen werden aus der eingetragenen Reisedauer berechnet. Starte mit **✕ Neu** eine neue Reise und gib die korrekte Tageszahl ein — die Mengen werden dann neu berechnet.

---

## Cross-Reference

→ [Technische Spezifikation für Entwickler](../dev/vacation-packing-list-implementation.md)
→ [Berechnungsformeln im Detail](../../Dokumentation/berechnungsformeln.md)
