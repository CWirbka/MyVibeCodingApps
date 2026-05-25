# Technische Spezifikation: Urlaubspackliste

## Überblick

Urlaubspackliste ist eine Single-Page-Webanwendung (SPA) ohne Build-Tooling, geschrieben in Vanilla JavaScript und Tailwind CSS (CDN). Die gesamte Logik befindet sich in einer einzigen Datei (`index.html`). Persistenz erfolgt ausschließlich über `localStorage`.

## Scope: Frontend Only

Keine Server-Komponenten, kein Build-Prozess, keine Abhängigkeiten außer Tailwind CSS und Inter Font (beide via CDN). Die App ist vollständig offline-fähig nach dem ersten Laden.

---

## Architektur

### Datei-Struktur

```
index.html    — Enthält HTML, inline CSS (<style>) + Tailwind-Klassen, und JavaScript (<script>)
```

### Rendering-Ansatz

Die App verwendet imperatives DOM-Rendering über `innerHTML`. Jeder Tab-Wechsel löst eine vollständige Re-Render des jeweiligen Panels aus. Ausnahmen (aus Performance-Gründen):

- `toggleItem()` — patcht nur die betroffene Checkbox und das Label
- `addItemToCategory()` — hängt eine neue Zeile direkt an den DOM
- `updateProgressDisplay()` — aktualisiert nur Fortschrittsbalken und Counter

---

## State-Management

### Persistierter Zustand (`state`-Objekt)

```js
const state = {
  currentTrip: loadFromStorage(STORAGE_KEYS.CURRENT_TRIP, null),
  baseList:    migrateBaseListFormat(loadFromStorage(STORAGE_KEYS.BASE_LIST, null)),
  savedTrips:  loadFromStorage(STORAGE_KEYS.SAVED_TRIPS, []),
};
```

### Flüchtiger UI-Zustand (nicht persistiert)

| Variable | Typ | Bedeutung |
|---|---|---|
| `selectedWeather` | `'warm' \| 'cold'` | Ausgewähltes Wetter im Setup-Formular |
| `newCategoryEmoji` | `string` | Emoji für die nächste neue Basislisten-Kategorie |
| `collapsedCategoryIds` | `Set<string>` | Eingeklappte Kategorie-IDs im Planner |
| `baseListLastSaved` | `Date \| null` | Zeitstempel der letzten manuellen Speicherung |
| `toastTimer` | `number \| null` | Handle für Auto-Dismiss-Timer des Toasts |

---

## Persistenz-Schicht

### localStorage-Schlüssel

```js
const STORAGE_KEYS = {
  BASE_LIST:    'packApp_baseList',
  SAVED_TRIPS:  'packApp_savedTrips',
  CURRENT_TRIP: 'packApp_currentTrip',
};
```

### Kern-Funktionen

| Funktion | Beschreibung |
|---|---|
| `loadFromStorage(key, fallback)` | Liest JSON aus localStorage; gibt `fallback` bei Fehler zurück |
| `persistToStorage(key, value)` | Schreibt JSON in localStorage; schluckt Fehler (z. B. Private-Browsing) |
| `persistAll()` | Schreibt alle drei Schlüssel in einem Durchgang |
| `persistBaseList()` | Ruft `persistAll()` auf und setzt `baseListLastSaved` |

---

## Datenmodelle

### `BaseCategory`

```js
{
  id:    string,    // generateId() oder Built-in-ID ('health', 'documents', 'basics')
  emoji: string,
  name:  string,
  items: string[],  // Eintrags-Texte als einfache Strings
}
```

### `Trip`

```js
{
  id:         string,
  name:       string,
  days:       number,
  weather:    'warm' | 'cold',
  sports:     boolean,
  categories: TripCategory[],
  createdAt:  string,           // ISO 8601
}
```

### `TripCategory`

```js
{
  id:    string,
  emoji: string,
  name:  string,
  items: TripItem[],
}
```

### `TripItem`

```js
{
  id:      string,
  text:    string,
  checked: boolean,
}
```

**Wichtig:** `BaseCategory.items` ist ein `string[]`, `TripCategory.items` ist ein `TripItem[]`. Die Konvertierung erfolgt in `createItems(textArray)`.

---

## Datenmigration (v1 → v2)

`migrateBaseListFormat(rawData)` wird beim App-Start einmalig auf dem geladenen localStorage-Wert ausgeführt.

```
v1 (Legacy):  { health: [...], documents: [...], basics: [...] }
v2 (Aktuell): [{ id, emoji, name, items }, ...]
```

Die Migration ist idempotent: bereits im v2-Format vorliegende Arrays werden unverändert durchgereicht.

---

## Feature: Smart Checklist Generator

**Funktion:** `generateChecklist(tripConfig)` → `Trip`

Baut eine vollständige Reiseliste aus zwei Ebenen:

### 1. Basis-Ebene (immer aktiv)
Alle Kategorien aus `state.baseList` werden 1:1 kopiert (Deep-Copy via `createItems()`).

### 2. Smart-Rules-Ebene
Zusätzliche Kategorien werden je nach Konfiguration eingefügt:

| Kategorie | Bedingung |
|---|---|
| ⏳ Vorher sicherstellen | immer (erste Position) |
| 👕 Anziehsachen | immer |
| 🔌 Elektronik | immer |
| 🏊 Schwimmen + UV-Schutz | `weather === 'warm'` |
| 🏃 Sport + Polar-Ladegerät in Elektronik | `sports === true` |

### Mengenberechnung (Kleidung)

| Artikel | Formel |
|---|---|
| Socken & Unterhosen | `durationDays + 1` |
| T-Shirts | `durationDays` |
| Poloshirts | `Math.ceil(durationDays / 2)` |

Siehe auch: [Berechnungsformeln](../../Dokumentation/berechnungsformeln.md)

---

## Feature: Obsidian Export

**Funktion:** `exportToObsidian()` — kein Rückgabewert, triggert Browser-Download

### Ausgabeformat

```markdown
---
erstellt: YYYY-MM-DD
typ: Notiz
datum: YYYY-MM-DD
projekt: <trip.name>
tags:
  - urlaub
  - packliste
para_category: Note
status: in-progress
---

# ✈️ <trip.name>

> <days> Tage · ☀️ Warm · 🏃 Sport

**Fortschritt:** X / Y erledigt (Z%)

## <emoji> <Kategoriename>

- [ ] Nicht abgehaktes Element
- [x] Abgehaktes Element
```

### Dateiname-Bereinigung

```js
trip.name
  .replace(/[<>:"/\\|?*\x00-\x1f]/g, '')  // Filesystem-unsichere Zeichen entfernen
  .replace(/\s+/g, '-')                     // Leerzeichen → Bindestriche
  + '-Packliste.md'
```

---

## Feature: Basislisten-Verwaltung

### Funktionsübersicht

| Funktion | Beschreibung |
|---|---|
| `addBaseItem(categoryId, text)` | Hängt String an `category.items` an |
| `removeBaseItem(categoryId, index)` | Entfernt per Array-Index (splice) |
| `addBaseCategory()` | Liest Formular, pusht neue Kategorie in `state.baseList` |
| `removeBaseCategory(categoryId)` | Nur für nicht-Built-In-Kategorien verfügbar |
| `explicitSaveBaseList()` | Persistiert und aktualisiert `baseListLastSaved` |
| `resetBaseList()` | Ersetzt `state.baseList` mit `getDefaultBaseList()` (nach Bestätigung) |
| `setNewCategoryEmoji(emoji)` | Aktualisiert `newCategoryEmoji` und Button-Highlights |

### Built-In-Schutz

```js
const BUILT_IN_CATEGORY_IDS = ['health', 'documents', 'basics'];
```

Kategorien mit diesen IDs zeigen keinen Löschen-Button in der UI.

---

## Feature: Trip-Archiv

### Funktionsübersicht

| Funktion | Beschreibung |
|---|---|
| `saveCurrentTrip()` | Upsert: aktualisiert vorhandene oder hängt neue Reise an `savedTrips` an |
| `loadTrip(tripId)` | Deep-Copy aus Archiv in `state.currentTrip`, wechselt zu Planner-Tab |
| `deleteTrip(tripId)` | Entfernt aus `savedTrips`, löscht ggf. `currentTrip` (nach Bestätigung) |
| `newTrip()` | Setzt `currentTrip = null`, zeigt Setup-Formular (nach Bestätigung) |

Das Archiv wird im Tab **neuste zuerst** gerendert (`[...state.savedTrips].reverse()`).

---

## Feature: Tab-Navigation & Planner UI

### Tab-Wechsel

`setTab(tabName)` — aktiviert Panel, deaktiviert die anderen, ruft den passenden Renderer auf.

### Kategorie-Cards im Planner

- **Collapse/Expand:** `toggleCategory(categoryId)` togglet `hidden`-Klasse auf `#catbody-<id>` und aktualisiert das Pfeil-Icon.
- **Counter:** `refreshCategoryCounter(categoryId)` aktualisiert `#cnt-<id>` nach DOM-level Änderungen.
- **Fortschritt:** `calculateProgress(trip)` → `{ total, checked, pct }`, `updateProgressDisplay()` patcht nur die relevanten DOM-Elemente.

---

## Sicherheitshinweise

- Alle User-Eingaben werden über `escapeHtml()` sanitiert, bevor sie via `innerHTML` eingefügt werden. Die Funktion nutzt native Browser-Serialisierung (`document.createTextNode`), kein Regex.
- `onclick`-Attribute in dynamisch generierten HTML-Strings escapen IDs ebenfalls via `escapeHtml()`, um Injection durch manipulierte IDs zu verhindern.

---

## Konstanten

| Konstante | Beschreibung |
|---|---|
| `STORAGE_KEYS` | Zentralisierte localStorage-Schlüssel |
| `BUILT_IN_CATEGORY_IDS` | IDs der drei nicht löschbaren Basiskategorien |
| `QUICK_EMOJI_PICKS` | 15 Emoji-Vorschläge im Kategorie-Picker |
| `CHECKMARK_SVG` | SVG-String für die Checkbox |

---

## Cross-Reference

→ [Benutzerhandbuch: Urlaubspackliste](../user/how-to-vacation-packing-list.md)
→ [Berechnungsformeln](../../Dokumentation/berechnungsformeln.md)
