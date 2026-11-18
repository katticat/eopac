

# Öffnungszeiten – KÖB Obernau

Kurzanleitung zur Anzeige der Öffnungszeiten aus dem **eOPAC** über **GitHub / Cloudflare Pages**.

```code

https://eopac.pages.dev/medien.html?liste=spiele
https://eopac.pages.dev/opening.html?tag=short
https://eopac.pages.dev/opening.html?tag=utm
```
## Dateien und Adresse

- `opening.html` – lädt die Öffnungszeiten aus dem eOPAC und ergänzt die Statusampel.
- `style.css` – enthält das allgemeine Layout und die Gestaltung.
- Öffentliche Seite: https://eopac.pages.dev/opening

Die Dateien werden im GitHub-Repository gepflegt und über Cloudflare Pages veröffentlicht. Nach einem Commit auf dem verbundenen Produktionsbranch erstellt Cloudflare automatisch ein neues Deployment. **Immer die feste Adresse `eopac.pages.dev` verwenden**, nicht die wechselnden Deployment-URLs.

## Zwei Ansichten

| Aufruf | Anzeige |
| --- | --- |
| `https://eopac.pages.dev/opening` | Normale Ansicht mit regulären Öffnungszeiten, Schließzeiten und Sonderöffnungen |
| `https://eopac.pages.dev/opening?tag=short` | Kurzansicht ohne die zusätzlichen Abschnitte |

In `opening.html` wird der Parameter eingelesen:

```js
const params = new URLSearchParams(window.location.search);
const tag = params.get("tag") || "";

const options = {
  bibid: "BGX429094",
  formHash: "ecjajeo",
  async: true,
  feature: "openings",
  openings_table: false,
  closings: tag !== "short",
  specialopenings: tag !== "short",
  styles: false,
  heading: "h3"
};

document.body.classList.toggle("opening-short", tag === "short");
```

**Wichtig:** `options` nur einmal definieren. Die `short`-Einstellungen müssen am tatsächlich verwendeten Objekt gesetzt werden.

## Statusampel

Ein kleines Skript erkennt die vom eOPAC gelieferte Meldung **„Wir haben aktuell geöffnet“** bzw. **„Wir haben aktuell geschlossen“** und setzt einen Punkt davor:

- Grün (`#299447`) = geöffnet
- Rot (`#cf3838`) = geschlossen

Da der eOPAC die Inhalte nachträglich lädt, wird dafür ein `MutationObserver` verwendet. Die Öffnungszeiten selbst werden **im eOPAC gepflegt**, nicht in der HTML-Datei.

## Kurzansicht gestalten

Wenn in `opening.html` die Klasse `opening-short` am `body` gesetzt wird, kann CSS nur diese Ansicht verändern:

```css
body.opening-short {
  background-color: #354472;
  color: #dedede;
}

body.opening-short #eopac-ecjajeo {
  color: #dedede;
}

/* Nur die Hauptüberschrift und Statuszeile ausblenden */
body.opening-short #eopac-ecjajeo .eopac-embedded-openings > h3,
body.opening-short .opening-status {
  display: none;
}
```

**Hinweis:** `body.opening-short` ist korrekt; `.opening-short > body` funktioniert hier nicht. Wenn der Status samt Ampel sichtbar bleiben soll, die zweite CSS-Auswahl (`body.opening-short .opening-status`) entfernen.

## Änderungen veröffentlichen

1. `opening.html` oder `style.css` im GitHub-Repository bearbeiten.
2. Änderung mit **Commit changes** speichern.
3. Unter **Cloudflare Pages → Deployments** das erfolgreiche Deployment prüfen.
4. Die Normal- und Kurzansicht testen; bei veraltetem CSS Browsercache neu laden oder den Versionsparameter der CSS-URL anpassen (z. B. `style.css?v=6`).

## Einbindung in die Homepage

Beispiel für die Kurzansicht:

```html
<iframe
  src="https://eopac.pages.dev/opening?tag=short"
  title="Öffnungszeiten der KÖB Obernau"
  style="width:100%;height:240px;border:0;"
  loading="lazy">
</iframe>
```

Die Höhe ist ein Ausgangswert und muss ggf. an den tatsächlich angezeigten Inhalt angepasst werden. Die Homepage muss die Einbettung erlauben.
