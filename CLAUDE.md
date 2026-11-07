# CLAUDE.md · Checklist Tool

Dit is de **Checklist Tool** van Savills Valuations: één HTML-bestand waarmee een taxateur een
digitaal dossier (een map op de netwerkschijf, `T:\Archief\…`) naloopt tegen de interne checklist.
Het maakt een A4-pdf met vinkjes voor in het dossier. Eigenaar en gebruiker: Ramon Coppens (RSC),
uitvoerend taxateur. De huidige versie staat in `const VERSIE` en bovenaan `const VERSIES` in `index.html`.

## Belangrijkste afspraken

1. **Alles staat in `index.html`.** Er is geen bronmap en geen build: je wijzigt dit ene bestand.
2. **Eén zelfstandig bestand.** Vanilla JS, CSS en SVG, alles inline. Geen libraries, geen CDN, geen
   netwerk (`fetch`), geen modules. Het bestand moet lokaal (`file://`) werken in Microsoft Edge op
   Windows, ook offline.
3. **Versie bij elke oplevering** (zie Versiebeheer). Noem in je antwoord altijd het versienummer en
   wat er ten opzichte van de vorige versie is veranderd.
4. **Nederlands.** Teksten in de tool en op de pdf, en ook namen van functies en variabelen. Kort en in
   gebruikerstaal, zoals de bestaande teksten.
5. **Privacy.** De repo op GitHub is openbaar. Echte klant- en dossiernamen horen niet in de code, de
   demo's of commits. `voorbeelden/*.html` (echte inventarisaties) staat in `.gitignore`: nooit committen.
6. **De gebruiker beslist.** De tool stelt voor en vinkt alleen zekere punten zelf af; alles is te
   herzien. Parafen worden nooit automatisch gezet. Liever een punt open laten dan verkeerd afvinken.
7. **Blijf in `C:\Users\ramon.coppens\Documents\ClaudeProjects`.** Tijdelijke bestanden (testharnas,
   schermafdrukken, Edge-profiel) in `.werk\` in deze map; die staat in `.gitignore`.

## Mappen

```
index.html            de tool (HTML, CSS en JS in één bestand)
CLAUDE.md             dit bestand
.gitignore            .werk/ en voorbeelden/*.html
voorbeelden/
  Voorbeeld map Enkel Object\     het sjabloon: zo hoort een dossiermap er nu uit te zien
  Voorbeeld map Portefeuille\     idem voor een portefeuille (objectmap heet hier "Dossiernummer")
  voorbeelden1.html, voorbeelden2.html   echte inventarisaties van twee opdrachtgevers (vertrouwelijk)
.werk/                werkmap van Claude, niet in git
  draai.sh            herkenning draaien op de echte inventarisaties → .werk/uit.json
  shots.sh            schermafdrukken op 375 en 1280 px → .werk/shots/
```

## Opbouw van index.html

Van boven naar beneden:

1. **Blok TEAM** (`<script type="text/plain" id="team">`): het echte team, zodat collega's het in
   Kladblok kunnen aanpassen. Zie Team.
2. **`<style>`**: alle opmaak, ook het A4 en de zes pdf-ontwerpen. Kleuren en maten als variabelen in
   `:root`. Smalle schermen onder `@media (max-width:1100px)`, `760px` en `420px`.
3. **HTML**: de kop met de vier stappen en de vier schermen (`#s-dossier`, `#s-controle`,
   `#s-overzicht`, `#s-a4`), plus `#venster`, `#melding` en de verborgen bestandskiezers.
4. **`<script>`** met vooraan de vaste waarden `MODUS` (`"bestand"`), `MERK` (`"logo"`), `VERSIE`,
   `VERSIES`, `MERKNAAM` en `LOGO_D`. Daarna blokken met een kop `/* ===== … ===== */`:
   - **Instellingen**: `INSTELLINGEN`, het team inlezen (`leesTeam`), `SECTIES` en `PUNTEN`
     (de checklist en de herkenning).
   - **Lezers**: zip, xlsx, docx, pptx, Outlook .msg (CFB) en .eml, zonder libraries.
   - **Voorbeelddossiers** (`DEMO`): twee fictieve dossiers in de mapindeling van het sjabloon.
   - **Kern**: state `S`, inlezen, dossier herkennen, scoren, automatisch afvinken, tellingen,
     aandachtspunten en de map-check (`SJABLOON`, `mapCheck`).
   - **Voorbeeld** van een bestand: pdf, foto's, mail, Excel, Word, PowerPoint, zip.
   - **Nagemaakte voorbeelden** voor de demodossiers (`demoPapier`, op mapnaam, niet op exact pad).
   - **Schermen** en alle events (`koppel`).
   - **Rapport**: het A4, ontwerpen, paginering, afdrukken.
   - **Start**.

De constante `MODUS === "artifact"` (demo opent meteen in Controle, geen afdrukknop) en
`MERK === "neutraal"` (geen logo) zitten nog in de code, maar er wordt nu alleen de bestandsversie gebruikt.

## Testen

Er is geen testsuite. Zo controleer je een wijziging:

```bash
sh .werk/draai.sh     # herkenning op alle 37 echte dossiers uit voorbeelden/*.html → .werk/uit.json
sh .werk/shots.sh     # schermafdrukken van alle schermen op 375 en 1280 px → .werk/shots/
```

- Beide scripts gebruiken de geïnstalleerde Edge headless. Headless Edge kan niet smaller dan ±500 px:
  de 375-schermafdruk gaat daarom via een iframe van 375 px breed.
- Bestaat `.werk` niet meer, maak de scripts dan opnieuw: de harnas laadt `index.html`, voegt een
  `<script>` toe dat de inventarisaties via `leesInventarisHtml` inleest, per dossier `laadUitInventaris`
  draait en per punt status en gekozen bestanden in een `<pre>` zet; `--dump-dom` haalt dat eruit.
- Na een wijziging in de herkenning: kijk of het aantal automatisch afgevinkte punten per punt niet
  onverwacht verandert, en kijk bij nieuwe automatische vinkjes of het gekozen bestand echt klopt.
- Loop daarna in Edge beide demo's door ("Voorbeeld enkel object" en "Voorbeeld portefeuille").

## Versiebeheer

Bij elke HTML die je oplevert:

1. Gewone wijziging: het laatste cijfer omhoog (1.3 → 1.4).
2. Pas in `index.html` aan: de commentaarregel helemaal bovenaan (`<!-- Checklist Tool V1.4 · dd-mm-jjjj -->`),
   `<title>`, `const VERSIE`, en zet een nieuw blok vooraan in `const VERSIES`
   (`{"versie": "1.4", "datum": "…", "punten": ["Eén regel per wijziging, kort en in gebruikerstaal."]}`).
3. De versie komt dan vanzelf in de kop van de tool, het (i)-venster en onderaan elke pdf-pagina.
4. Zet in je antwoord "V1.4" met daaronder dezelfde wijzigingen als in `VERSIES`.

Werk op een nieuwe branch; commit en push alleen als Ramon erom vraagt.

## Team

- In het blok **TEAM** bovenaan `index.html`: één regel per persoon, `Initialen | Rol | Naam`.
  Rollen: `taxateur` (1e of 2e taxateur, paraaf dossiercheck), `uitvoerend` (Checklist ingevuld door),
  `secretariaat` (paraaf factuur). Meer rollen met een komma. Regels die met `#` beginnen tellen niet.
- Zegt Ramon dat hij dat blok in zijn eigen bestand heeft gewijzigd, neem de regels dan over in
  `index.html`; anders gaan ze bij de volgende versie verloren.
- Initialen zijn de sleutel (hoofdletters maken niet uit; bij dubbele telt de eerste regel). Afwijkend:
  RSC (Ramon Coppens), JeL (Jesse Luimes) en JuL (Julia Leemans).
- Taxateurs staan in de volgorde waarin je klikt (1e, 2e). De paraaf bij de dossiercheck stelt de
  invuller voor als die taxateur is, anders de 1e taxateur.
- Wie er niet in staat, kan in de tool in het open veld naast de knoppen worden getypt.

## Hoe de tool werkt

**Schermen**: Dossier → Controle → Overzicht → PDF.
- *Dossier*: eerst enkel object of portefeuille, dan de map kiezen (`showDirectoryPicker`, anders
  `webkitdirectory`), slepen, een inventarisatie laden of paden plakken. Daarna de dossiergegevens.
- *Controle*: per punt de gevonden bestanden met een voorbeeld. Enter = akkoord, N = n.v.t., O =
  ontbreekt, pijlen = vorig/volgend punt of ander bestand.
- *Overzicht*: alle punten op een rij, en "Viel op in de map": tijdelijke bestanden, te lange paden
  en de map-check tegen het sjabloon.
- *PDF*: ontwerp kiezen, opties, dan Afdrukken en "Opslaan als PDF".

**Inlezen** (zodat grote mappen op `T:\` snel blijven):
1. `leesMap()` leest alleen namen, met voortgangsbalk en Stoppen (`STOP.aan`).
2. `vulDetails()` haalt datum en grootte op, alleen van bestanden die bij een punt gevonden zijn.
3. `haalFile()` opent een bestand pas als het voorbeeld getoond wordt.

**Inventarisatie** (`leesInventarisHtml`): een .html van het inventarisatiescript. Het script maakt nu
alleen de "Mapstructuur" (boom van `details.boom`, per bestand een `<li>`, slotje `stip s-slot` =
alleen-lezen, geen datum per bestand); dat leest `leesInventarisBoom`. Een oude tabel "Alle bestanden"
werkt ook nog. Gaat de inventarisatie over een opdrachtgever (`T:\Archief\<jaar>\<opdrachtgever>`),
dan kies je eerst het dossier (`dossiersInInventaris`, `kiesUitInventaris`, `laadUitInventaris`).

**State** staat in `S`: `bron`, `bestanden` (`id` = relatief pad met `\`), `objecten` en
`objectenAlle` (portefeuille), `dossier` (gegevens voor de pdf), `punten[id]` (`status`
ok/nvt/mist/deels, `gekozen` per object, `toelichting`, `paraaf`, `auto`), `kand` (kandidaten per punt
en object), `type` en `keuze`. Voortgang wordt per dossier in `localStorage` bewaard
(`checklisttool:<soort>:<map>`), net als het gekozen ontwerp. Altijd in try/catch.

**Herkenning**: de punten staan in `PUNTEN`, het scoren in de kern.
- Namen worden genormaliseerd (`normTekst`): kleine letters, zonder accenten, leestekens als spatie,
  mapnummers als `03.` of `3.1` eraf.
- `zoek` per punt: `map` (+`mapW`, standaard 2), `moetMap`, `mapSterk` (+3), `naam` (+4), `zwak`
  (+1,5), `niet` (−6), `nietMap` (−2), `ext`. Bestanden in een map oud/old/archief/vervallen: −4,5.
- Vanaf `TOON = 2,5` ziet de gebruiker het bestand, vanaf `KIES = 4` staat het klaar (`voorgekozen`).
  Met `voorkeurAlleenLezen` (berekening) staat een bestand op alleen-lezen voor de rest.
  `isZeker()` bepaalt of `autoAfvinken()` het punt zelf afvinkt. Nooit automatisch: `nooitAuto`
  (ondertekend rapport, onderbouwing markthuur) en parafen.
- Bestanden met "sjabloon" in de naam tellen nooit als zeker. "Onderbouwing referenties sjabloon.docx"
  staat standaard in elke map en telt helemaal niet mee.
- Andere velden: `scope` (`"dossier"` of `"object"`), `paraaf`, `auto` (padlengte, alleenlezen,
  bestandsnaam), `nvtAls(d)` met `voorwaarde` (type taxatie, high risk), `welke`, `tabbladen`, `fotos`.

**Dossiermappen volgens het sjabloon** (`voorbeelden\Voorbeeld map …`, en `SJABLOON` in de code):

Enkel object: `T:\Archief\<jaar>\<opdrachtgever>\<dossiernummer>`
```
01. Opdracht
02. Correspondentie
03. Object Informatie\3.1 Inspectie Formulier & Foto's · 3.2 Kadaster · 3.3 Bestemming ·
    3.4 Plattegronden - Meetstaten · 3.5 Huurgegevens · 3.6 Eigenaarslasten en Onderhoud ·
    3.7 ESG en Milieu · 3.8 Marktverhaal & Referenties · 3.9 Diversen
04. Berekening\4.1 Werkmap (\Oud) · 4.2 Concept · 4.3 Definitief
05. Rapport\5.1 Werkmap (\Oud) · 5.2 Concept · 5.3 Definitief
06. Checklist\Akkoord mails
```
Portefeuille: `T:\Archief\<jaar>\<opdrachtgever>\<projectnaam>`
```
01. Algemeen Secretariaat · 02. Correspondentie · 03. Algemene Informatie
04. Berekeningen\4.1 Werkmap · 4.2 Concept · 4.3 Definitief
05. Rapporten\5.1 Werkmap · 5.2 Concept · 5.3 Definitief
06. Checklist\Akkoord mails
<dossiernummer per object>\1. Inspectie Formulier & Foto's … 9. Diversen
```
- Een objectmap begint met 5 cijfers, eventueel met versie en adres: `12539`, `12539-01`,
  `12539 Radarweg 256`, `09768 - Albert Cuypstraat 210-H` (`objMapInfo`). Berekeningen en rapporten
  van een portefeuille staan algemeen en horen bij een object via het nummer in de naam (`12539-01 …`).
- Bestandsnamen beginnen vaak met dossiernummer en versie (`12110-02 …`). Een pad mag hoogstens 230
  tekens zijn.
- **In de praktijk** bestaan ook oudere indelingen naast elkaar (`05. Berekening`, `06. Rapport`,
  `09.0 Berekening`, `01.2 Werkmap Berekeningen`, `02.0 Correspondentie Object`, akkoordmails in
  `02. Correspondentie\Akkoord`, `01. Opdracht\Akkoord taxateurs`). De herkenning moet daar ook mee
  werken; de map-check meldt dan alleen dat de indeling afwijkt. De definitieve rekenmodel-Excel staat
  in de praktijk meestal op alleen-lezen in `4.1 Werkmap`.

**Beperkingen**: bij een map uit de browser kunnen `alleenlezen` en `padlengte` niet volledig (de
browser ziet geen alleen-lezen en kent het volledige `T:\`-pad niet); met een inventarisatie wel.
Een lege map ziet de tool niet (alleen mappen met bestanden). `showDirectoryPicker` werkt niet in een
iframe. Microsoft Print to PDF maakt van tekst vormen: adviseer "Opslaan als PDF".

## PDF

- Het blad is in millimeters (`.a4`, `.a4in`, `.a4voet`) met `@page { size: A4; margin: 0 }`.
  `pagineer()` verdeelt de rijen over bladen met `overloopt()`. Enkel object past op één blad; een
  portefeuille krijgt een bijlage per object (matrix met legenda).
- Zes ontwerpen in `SJABLONEN` (klassiek, formulier, modern, geel, navy, compact), elk met een plaatje
  in `sjabloonPlaatje()` en CSS onder `.a4.sj-<id>`. Controleer bij een nieuw ontwerp met beide demo's
  dat niets overloopt.
- Bestandsnaam via `pdfNaam()`: `CL_<dossiernummer>_Checklist_<dd-mm-jjjj>`. De pdf hoort in
  `06. Checklist` van het dossier (`checklistMap()`).
- Vinkjes zijn SVG: aanwezig donker navy, n.v.t. lichtgrijs, deels oranje, ontbreekt rood. Onderaan
  alleen "Checklist Tool V… · pad" en het paginanummer.

## Stijl

- Savills-huisstijl met een rustige, Apple-achtige uitstraling: navy `#25273A`, geel `#FFDF00`, crème
  `#EEE8E3`, veel wit, afgeronde vlakken, systeemlettertype. Gebruik de variabelen in `:root`.
- Knoppen zeggen wat er gebeurt ("Maak PDF", "Opnieuw beginnen"). Op de pdf: "aanwezig", "n.v.t.",
  "ontbreekt".
- Altijd bruikbaar op 375 px breed.

## Code-afspraken

- HTML met template strings en `innerHTML`; altijd `esc()` om tekst uit bestanden of invoer.
- Events via delegatie in `koppel()` met `data-`attributen (`data-actie`, `data-stap`, `data-keuze`,
  `data-sjabloon`, `data-invdossier`, …). Geen losse onclick's.
- Teken niet het hele scherm opnieuw terwijl iemand typt: werk bij in plaats van te vervangen, zoals
  `verversPersonen()`.
- Korte functies met Nederlandse namen, een regel uitleg boven een blok, geen nieuwe afhankelijkheden.
- Bij scripts die `index.html` aanpassen: let op backslashes (paden in de JS staan als `\\`).
  De Edit-tool is het veiligst.

## Werken met Ramon

- Hij test het bestand lokaal in Edge en geeft feedback in rijtjes. Pak alle punten op en zeg kort
  wat je hebt gedaan.
- Antwoord in het Nederlands, kort. Lever een nieuwe HTML altijd met versienummer en wijzigingen.
- Twijfel je over de herkenning of de mapindeling: kijk in `voorbeelden\` (sjabloon en echte
  inventarisaties) en in de Excel-checklists in `voorbeelden\…\06. Checklist\`.
