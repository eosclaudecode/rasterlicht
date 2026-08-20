# Rasterlicht

Generative Wellenfeld-Visualisierung: ein Interferenzfeld wird in ein Raster
quantisiert und dort als kreisförmige Pixel oder als Bilder gezeichnet — ein Bild
je Helligkeitsstufe.

Alles steckt in `index.html`. Kein Build, kein Bundler, keine Abhängigkeit außer
p5.js vom CDN.

```
rasterlicht/
├── index.html      ← die App
├── images.json     ← welche Bilder es gibt und welche Stufe sie belegen
└── img/            ← die Bilddateien
```

`images.json` und `img/` entstehen erst, wenn du das erste Mal veröffentlichst.
Ohne sie startet die App mit leerer Bibliothek — alles andere funktioniert.

---

## 1 · Auf GitHub Pages veröffentlichen

```bash
git init
git add index.html README.md
git commit -m "Rasterlicht"
git branch -M main
git remote add origin git@github.com:DEINNAME/rasterlicht.git
git push -u origin main
```

Im Repo: *Settings* → *Pages* → Source **Deploy from a branch**, Branch `main`,
Ordner `/ (root)` → *Save*. Nach etwa einer Minute liegt die Seite unter
`https://DEINNAME.github.io/rasterlicht/`.

---

## 2 · Wer darf was

**Alle Besucher** sehen dieselben Bilder aus dem Repository. Sie dürfen eigene
Bilder hinzufügen und damit spielen — das bleibt in ihrer Sitzung und berührt das
Repository nie.

**Du** kannst die Bibliothek ändern. Zwei Wege, beide erzeugen dasselbe Ergebnis:

### Weg A — ohne alles: ZIP

In der Bibliothek auf **↓ ZIP** klicken. Die Datei enthält `images.json` und den
kompletten `img/`-Ordner. Entpacken, Inhalt bei GitHub in das Repository ziehen,
fertig. Funktioniert immer, auch wenn ein Token fehlt oder abgelaufen ist.

### Weg B — mit Token: direkt aus der Seite heraus

Ein Klick auf **↑ Ins Repository veröffentlichen** committet die neuen Bilder und
das Manifest selbst. Dafür brauchst du einmalig ein GitHub-Token.

**Token erstellen**

1. GitHub → Profilbild → *Settings* → ganz unten *Developer settings*
2. *Personal access tokens* → **Fine-grained tokens** → *Generate new token*
3. **Repository access**: *Only select repositories* → dein `rasterlicht`
4. **Permissions** → *Repository permissions* → **Contents**: `Read and write`
   (mehr nicht — alles andere bleibt auf *No access*)
5. **Expiration**: 90 Tage oder *No expiration*. Läuft es ab, funktioniert nur
   noch Weg A, bis du ein neues erstellst.
6. *Generate token* → die Zeichenkette wird **nur einmal** angezeigt, direkt kopieren

**Token hinterlegen**

Auf der Seite im Bereich *Admin* einfügen und *Token merken* klicken. Benutzername
und Repository liest die Seite selbst aus der Adresse; überschreiben kannst du sie
in den Feldern darunter.

Das Token liegt danach ausschließlich in diesem einen Browser (`localStorage`).
Es steht nirgends im Quelltext und kommt nie ins Repository — andere Besucher
sehen bei sich einfach leere Felder und können nicht veröffentlichen. Auf einem
zweiten Gerät fügst du es erneut ein. Landet es je irgendwo, wo es nicht hingehört:
bei GitHub unter *Fine-grained tokens* mit einem Klick widerrufen. Weiter als in
dieses eine Repository kommt damit niemand.

Nach dem Veröffentlichen dauert es ~40 Sekunden, bis GitHub Pages die neue Fassung
ausliefert.

---

## 3 · Bilder

Beim Hochladen verkleinert der Browser jedes Bild auf 640 px lange Kante und
komprimiert es — WebP, oder PNG wenn Transparenz im Spiel ist. Ein Bild landet
damit typischerweise bei 30–80 KB; 30 Bilder wiegen zusammen etwa 1,5 MB und
werden vom Browser einzeln zwischengespeichert.

In `images.json` steht neben der Dateiliste auch die Zuordnung Stufe → Bild,
getrennt nach Stufenanzahl: eine Belegung für 10 Stufen und eine für 16 Stufen
können nebeneinander bestehen.

Weil die Bilder aus demselben Repository kommen wie die Seite, funktionieren
PNG- und Video-Export ohne Einschränkung. Bilder von fremden Servern würden das
Canvas sperren und den Export unmöglich machen — deshalb liegen sie hier.

---

## 4 · Bedienung

**Wellenfeld** — Vier Wellenfronten: *Radial* (Punktquellen, die sich
durchdringen), *Linear* (ebene Fronten mit Winkel und Fächerung), *Gemischt* und
*Zentrisch* (Ringe und Spiralarme). Das **Wellenprofil** bestimmt die Form der
Welle: Sinus weich, Rechteck mit harten Kanten, dazu Dreieck und Sägezahn.
**Spiegelachsen** faltet das Feld kaleidoskopisch in 2 bis 12 Sektoren.

**Bewegung** — Normalerweise wandert das Muster. Mit **Stehende Welle** bleibt es
an Ort und Stelle: die Knotenlinien stehen still, dazwischen schwingt die
Amplitude auf und ab — dieselbe Überlagerung zweier gegenläufiger Wellen, die eine
gezupfte Saite in Form hält. Die *Schwingungstiefe* bestimmt, wie weit das Feld
dabei zusammenfällt; bei 1 durchläuft es einen Moment völliger Ruhe.

**Raster** — Zehn Seitenverhältnisse vom Quadrat über 16:9 bis 9:16. Beim Wechsel
passt sich die Zeilenzahl automatisch an, damit die Zellen quadratisch bleiben und
das Bild randlos füllen; überschreiben kannst du sie danach jederzeit. Das
Wellenfeld selbst wird nie verzerrt — Kreise bleiben Kreise, das Format schneidet
nur einen anderen Ausschnitt heraus. Dazu Spalten, Zeilen und Helligkeitsstufen
(2 bis 24) frei wählbar; *Abstand* schafft Luft zwischen den Zellen, damit große
Bilder einander nicht überlagern.

**Bilder** — Die Slot-Anzahl folgt der Stufenzahl; Slot 1 ist die dunkelste Stufe.
Klick auf einen Slot öffnet die Bibliothek zur Auswahl.

Seed und Phase bestimmen das Bild vollständig: dieselben Werte ergeben immer
exakt dasselbe Ergebnis.

---

## 5 · Export

Angegeben wird jeweils die **längste Kante**; die zweite Seite ergibt sich aus dem
gewählten Seitenverhältnis, auf gerade Pixelzahlen gerundet, weil Videoencoder
ungerade Maße nicht mögen.

**Einzelbild** — PNG bis 6000 px lange Kante.

**Video** — Auflösung, Format, Framezahl, Bildrate und Phasen-Schritt pro Frame.
Vier Formate:

| Format | Wann |
|---|---|
| **MP4 (H.264)** | Der Normalfall. Öffnet in Premiere, Final Cut, DaVinci, QuickTime. |
| **MOV (H.264)** | Derselbe Datenstrom, nur mit `.mov` benannt — für Programme, die auf der Endung bestehen. |
| **WebM (VP9)** | Für Web und als Rückfalloption. |
| **PNG-Sequenz (ZIP)** | Nummerierte Einzelbilder, verlustfrei, in jedem Schnittprogramm als Sequenz importierbar. Funktioniert immer. |

Ein Hinweis, der in der Praxis zählt: Manche Browser melden MP4 als unterstützt,
schreiben dann aber VP9 in den MP4-Container — eine Datei, die zwar `.mp4` heißt,
die Schnittprogramme aber nicht öffnen. Die Seite nimmt deshalb beim Laden drei
Testframes auf und liest im Container nach, was wirklich darin steckt. Kann der
Browser kein echtes H.264, sind MP4 und MOV in der Auswahl ausgegraut statt
stillschweigend kaputte Dateien zu liefern.

Stand heute liefern Chrome, Edge und Safari echtes H.264; Firefox kann es nicht —
dort sind WebM und die PNG-Sequenz die Wege.

Das Video wird Frame für Frame gerendert und in Echtzeit aufgezeichnet: 120 Frames
bei 30 fps dauern rund vier Sekunden. Bei sehr hohen Auflösungen kann das Rendern
langsamer als die Bildrate werden — dann lieber die PNG-Sequenz nehmen, die ist
zeitlich exakt.
