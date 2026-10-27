# Briefing voor Codex: app "Museumtentoonstellingen"

## Wat ik wil bouwen
Een persoonlijke app (mobielvriendelijke web-app, later eventueel als app op de telefoon) voor mij (Sven) en mijn vriendin. We wonen in Amersfoort, hebben een Museumkaart en gaan meestal met de auto in het weekend naar een museum. De app laat in één oogopslag zien welke tentoonstellingen in Nederlandse musea de moeite waard zijn, en wij kunnen er zelf cijfers aan geven.

## Waar de data vandaan komt
- Mijn assistent werkt elke vrijdag rond 9:00 een JSON-bestand bij in mijn GitHub-repository.
- Bestand: `data.json` in de repo `studioluttmer/museum-data` (adres volgt). De app haalt de "raw"-versie op, bijvoorbeeld `https://raw.githubusercontent.com/studioluttmer/museum-data/main/data.json`.
- De app leest dit bestand alleen. Hij past het niet zelf aan.

## Opbouw van data.json
```json
{
  "laatst_bijgewerkt": "2026-10-30T09:00:00+01:00",
  "herkomst": { "plaats": "Amersfoort", "vervoer": "auto" },
  "musea": [
    {
      "id": "voorlinden",
      "naam": "Museum Voorlinden",
      "plaats": "Wassenaar",
      "provincie": "Zuid-Holland",
      "adres": "Buurtweg 90, 2244 AG Wassenaar",
      "categorie": "hedendaagse kunst",
      "website": "https://www.voorlinden.nl",
      "favoriet": true,
      "amersfoort": false,
      "museumkaart": "ja",              // "ja" | "nee" | "onbekend"
      "openingstijden": "di-zo 11:00-17:00",
      "reistijd_auto_min": 55,
      "afstand_km": 70,
      "parkeren": "Eigen parkeerterrein"
    }
  ],
  "tentoonstellingen": [
    {
      "id": "voorlinden-2026-voorbeeld",
      "museum_id": "voorlinden",
      "titel": "...",
      "kunstenaars": ["..."],
      "startdatum": "2026-10-01",
      "einddatum": "2027-01-31",
      "status": "nieuw",                // "nieuw" | "lopend" | "binnenkort" | "afgelopen"
      "beschrijving": "...",
      "verhaal_kunstenaar": "Kort verhaal over wat de kunstenaar bijzonder maakt.",
      "tags": ["hedendaags", "installatie"],
      "fotos": [ { "url": "https://...", "bijschrift": "Titel werk, jaar" } ],
      "voorbeeldwerken": [ { "titel": "...", "jaar": "..." } ],
      "museumkaart_toeslag": { "toeslag": true, "bedrag_eur": 5, "toelichting": "..." },
      "reserveren": {
        "nodig": "verplicht",           // "verplicht" | "aangeraden" | "niet nodig" | "onbekend"
        "drukte": "druk",
        "ticket_url": "https://...",
        "gecontroleerd_op": "2026-10-30T09:00:00+01:00",
        "eerste_vrije_tijdslot": [ { "datum": "2026-10-31", "tijd": "11:30" } ]
      },
      "recensies": [
        { "medium": "NRC", "recensent": "...", "sterren": 4, "max_sterren": 5,
          "samenvatting": "...", "datum": "2026-10-10", "url": "https://..." }
      ],
      "gemiddelde_sterren": 4.0,
      "bron_url": "https://...",
      "onze_cijfers": { "sven": 8, "vriendin": 7, "bezocht_op": "2026-11-07", "notitie": "..." }
    }
  ]
}
```
Velden kunnen ontbreken als de informatie niet bekend is; de app moet daar netjes mee omgaan (niets tonen in plaats van een lege plek).

## Wat de app moet kunnen
1. Overzicht van tentoonstellingen als kaarten: foto's groot bovenaan, daaronder het verhaal over de kunstenaar, dan de praktische info (museum, plaats, looptijd, reistijd met de auto, Museumkaart of toeslag, reserveren, sterren uit recensies).
2. Standaard sorteren: favorieten en Amersfoort eerst, daarna wat het best past bij onze eigen cijfers.
3. Filters: plaats/provincie, museum, categorie/tags, status (nieuw, lopend, binnenkort), alleen met Museumkaart, reistijd (binnen 30 min, 1 uur, 1,5 uur), minimale sterren, en zoeken op titel of kunstenaar.
4. Detailpagina per tentoonstelling met alle info, recensies met links, tijdsloten en een knop naar de ticketpagina en naar de route (Google Maps vanaf Amersfoort).
5. Herinnering/markering voor tentoonstellingen die binnen vier weken sluiten.
6. Archief: afgelopen tentoonstellingen met onze cijfers.
7. Cijfers geven: Sven en mijn vriendin kunnen elk apart een cijfer (1-10), bezoekdatum en notitie geven per tentoonstelling.
   - Sla de cijfers lokaal op in de app (of in een eigen kleine database als we inloggen toevoegen).
   - Stuur elk nieuw of gewijzigd cijfer ook als POST naar het webadres van mijn assistent (volgt nog), met JSON zoals:
     `{ "tentoonstelling_id": "...", "persoon": "sven" | "vriendin", "cijfer": 8, "bezocht_op": "2026-11-07", "notitie": "..." }`
   - Mijn assistent verwerkt die cijfers in data.json (veld `onze_cijfers`), zodat ze bij de volgende update terugkomen en hij onze smaak leert.
8. Nederlandstalig, rustig en visueel ontwerp, goed bruikbaar op de telefoon.

## Technische wensen
- Simpel te hosten (bijvoorbeeld als statische web-app via GitHub Pages of Vercel).
- Data alleen ophalen bij openen of verversen; geen eigen scraping.
- Houd het id-veld van tentoonstellingen aan als vaste sleutel om cijfers te koppelen.
