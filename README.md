# NHL ilmaiseksi tänään?

Yksi staattinen sivu (`index.html`), joka kertoo näytetäänkö tänään NHL-ottelu ilmaiskanavalla Suomessa:
MTV Sub, STAR Channel tai Viaplay TV. Suorat lähetykset ja tallenteet erotellaan.

- Avaa `index.html` selaimessa tai julkaise GitHub Pagesilla (Settings → Pages → branch `main`, root).
- `?date=YYYY-MM-DD` näyttää muun päivän. "Päivä" = klo 06:00–06:00, jotta yön ottelut lasketaan illalle.

## Datalähde

Telkku/Iltalehti-ohjelmaoppaan julkinen JSON-rajapinta (CORS sallittu, ei avainta):
`https://il-telkku-api.prod.il.fi/v1/channel-groups/d63736b5-7f72-45d4-8f9e-6505f1bac855/offering?startTime=…&endTime=…`

Kanavat: MTV Sub `560`, Star Channel `557`, Viaplay TV Finland `13786`.

Rajapinta on dokumentoimaton. Jos sivu näyttää virheen, tarkista ajantasainen osoite ja kanava-ID:t
[iptv-org/epg](https://github.com/iptv-org/epg/tree/master/sites/iltalehti.fi) -projektista.
Kanavien NHL-oikeudet voivat muuttua kausittain (2026–27: MTV Sub sunnuntait, STAR Monday Night Hockey).
