# Berechnungsformeln der Packliste

## Kleidung (Anziehsachen)

Die Mengen werden automatisch aus der eingegebenen **Reisedauer in Tagen** berechnet.

| Artikel | Formel | Beispiel 7 Tage | Beispiel 10 Tage |
|---------|--------|-----------------|------------------|
| Socken | Tage + 1 | 8 Paar | 11 Paar |
| Unterhosen | Tage + 1 | 8 Stück | 11 Stück |
| T-Shirts | Tage | 7 Stück | 10 Stück |
| Poloshirts | ⌈Tage ÷ 2⌉ (aufrunden) | 4 Stück | 5 Stück |

**Poloshirt-Beispiele:**
- 7 Tage → ⌈3,5⌉ = **4 Poloshirts**
- 10 Tage → ⌈5⌉ = **5 Poloshirts**
- 11 Tage → ⌈5,5⌉ = **6 Poloshirts**

Alle anderen Kleidungsstücke (Jacke, Buff, Hose, Leichte Jacke) sind **feste Einträge** ohne Mengenberechnung.

## Umsetzung im Code

```js
// index.html, Funktion generateChecklist()
const socksCount  = durationDays + 1;          // Socken & Unterhosen
const tShirtCount = durationDays;               // T-Shirts
const poloCount   = Math.ceil(durationDays / 2); // Poloshirts (aufgerundet)
```

Die Werte werden als Text direkt in den Artikelnamen eingebettet, z. B.:
```
`Socken (${socksCount} Paar)`
`Poloshirts (${poloCount} Stück)`
```
