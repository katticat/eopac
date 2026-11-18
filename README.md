# eOPAC-Webseiten – KÖB Obernau

Kurzdokumentation für **Öffnungszeiten und Medienlisten** auf GitHub / Cloudflare Pages.

---

```code
[https](https://github.com/katticat/eopac/medien.html)
[https](https://github.com/katticat/eopac/openning.html)
[https](https://github.com/katticat/eopac/styles.css)

[https](https://eopac.pages.dev/medien.html?liste=spiele)
[https](https://eopac.pages.dev/opening.html?tag=short)
[https](https://eopac.pages.dev/opening.html?tag=utm)
```
## Teil 1: Öffnungszeiten


Kurzanleitung zur Anzeige der Öffnungszeiten aus dem **eOPAC** über **GitHub / Cloudflare Pages**.

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

---

## Teil 2: Buch- und Medienlisten

Die Datei `medien.html` bindet die Medienlisten des **eOPAC der KÖB Obernau** ein. Die Auswahl erfolgt über den URL-Parameter `liste`; ohne Parameter wird die Liste `tonies` angezeigt.

**Feste Adresse:** https://eopac.pages.dev/medien.html

### Aufrufe und eOPAC-Aliase

| Medienliste | Alias (`liste=`) | Aufruf |
| --- | --- | --- |
| Tonies | `tonies` | [Tonies](https://eopac.pages.dev/medien.html?liste=tonies) |
| Bilderbücher | `bilderbucher` | [Bilderbücher](https://eopac.pages.dev/medien.html?liste=bilderbucher) |
| Spiele | `spiele` | [Spiele](https://eopac.pages.dev/medien.html?liste=spiele) |
| Erstleser | `erstleser` | [Erstleser](https://eopac.pages.dev/medien.html?liste=erstleser) |
| Belletristik | `belletristik` | [Belletristik](https://eopac.pages.dev/medien.html?liste=belletristik) |
| Zeitschriften | `zeitschriften-alle` | [Zeitschriften](https://eopac.pages.dev/medien.html?liste=zeitschriften-alle) |

Die Alias-Schreibweisen müssen genau zu den im eOPAC hinterlegten Medienlisten passen. **Achtung:** Der Alias für Bilderbücher lautet in der vorliegenden `medien.html` `bilderbucher` (ohne Umlaut).

### Dateien und Gestaltung

- `medien.html` – Navigation mit sechs Links, URL-Parameterauswertung und eOPAC-Einbindung.
- `style.css` – allgemeines Layout, Medienliste, Farben, Verfügbarkeitsampel und Navigation.
- Die sechs Navigationsbilder kommen aus dem Bildverzeichnis der Bücherei auf `bistum-wuerzburg.de`.
- Die spezielle Zeitschriften-Galerie hat in der vorliegenden `medien.html` zusätzlich einen eigenen `<style>`-Block.

**Wichtig:** Die hochgeladene Version von `medien.html` verweist für ihr allgemeines CSS noch auf `https://eopac.katti.workers.dev/style.css?v=4`, während die Öffnungszeiten-Anleitung `https://eopac.pages.dev/style.css` verwendet. Bei Änderungen prüfen, welche CSS-Adresse tatsächlich eingebunden und erreichbar ist, und diese nach Möglichkeit vereinheitlichen.

### Wie die Medienliste ausgewählt wird

```js
const params = new URLSearchParams(location.search);
const listenAlias = params.get("liste") || "tonies";

const options = {
  bibid: "BGX429094",
  formHash: "ecjajem",
  async: true,
  feature: "medialist",
  alias: listenAlias,
  format: "",
  cover: true,
  actions: true,
  styles: false,
  rak: true,
  heading: "h3"
};
```

`styles: false` erlaubt die eigene Gestaltung per CSS. Der eOPAC liefert die Medieninformationen, Cover-URLs und Kataloglinks; die Webseite berechnet keine eigenen Ausleihbestände.

### Zeitschriften: besondere Galerie

Bei `?liste=zeitschriften-alle` wird statt der normalen Medienliste eine **Covergalerie** ausgegeben:

1. Die vom eOPAC geladenen Ausgaben werden nach abgeleitetem Zeitschriftentitel gruppiert.
2. Pro erkannter Zeitschrift wird die neueste Ausgabe anhand von **Jahr und Heftnummer** gewählt.
3. Für das Cover werden die Ausgaben von neu nach alt geprüft. Ist das Bild der neuesten Ausgabe nicht nutzbar, wird ein Bild einer älteren Ausgabe versucht und entsprechend gekennzeichnet.
4. Gibt es kein ladbares Cover, erscheint eine Ersatzkachel mit dem Zeitschriftentitel.
5. Der Kataloglink führt unabhängig vom angezeigten Cover zum als neuestem erkannten Heft.

Das Raster nutzt **vier Spalten am Desktop**, drei auf kleineren Bildschirmen und zwei auf Smartphones. Die entsprechenden Klassen heißen unter anderem `.zeitschriften-raster`, `.zeitschrift-kachel` und `.zeitschrift-cover`.

**Grenzen:** Die Galerie kann nur Ausgaben berücksichtigen, die die eOPAC-Einbettung tatsächlich liefert. Unterschiedliche Titel-Schreibweisen oder unübliche Heftnummern können die Gruppierung oder Reihenfolge beeinflussen. Ein Cover-Link im eOPAC garantiert nicht, dass die Bilddatei im Browser lädt; bei fehlenden Covern die Bildadresse in den Browser-Entwicklerwerkzeugen prüfen.

### Neue Medienliste hinzufügen

1. Medienliste im eOPAC anlegen und den exakten Alias notieren.
2. In `medien.html` innerhalb von `<nav class="medien-navigation">` einen weiteren Link mit `href="medien.html?liste=ALIAS"` und `data-liste="ALIAS"` ergänzen.
3. Bildadresse und sichtbaren Namen eintragen; gegebenenfalls die Anzahl der Navigationsspalten im CSS anpassen.
4. Auf GitHub committen und nach dem Cloudflare-Pages-Deployment den Aufruf mit `?liste=ALIAS` testen.

### Einbindung auf der Bücherei-Webseite

Beispiel für die Tonies-Liste:

```html
<iframe
  src="https://eopac.pages.dev/medien.html?liste=tonies"
  title="Tonies der KÖB Obernau"
  style="width:100%;height:900px;border:0;"
  loading="lazy">
</iframe>
```

Die Höhe muss an den Inhalt angepasst werden; bei längeren Listen ist eine automatische Höhenanpassung oder ein direkter Link oft sinnvoller.

### Änderungen veröffentlichen und prüfen

1. `medien.html` oder `style.css` im GitHub-Repository ändern und committen.
2. Erfolgreiches Deployment unter **Cloudflare Pages → Deployments** kontrollieren.
3. **Immer `eopac.pages.dev` verwenden**, nicht die bei Deployments wechselnden Vorschauadressen.
4. Mit `?liste=tonies` und `?liste=zeitschriften-alle` testen; besonders Cover, Kataloglinks, Anzahl und Mobilansicht prüfen.
5. Bei veralteten Styles den Browsercache leeren oder die Versionsnummer an der CSS-URL erhöhen.

