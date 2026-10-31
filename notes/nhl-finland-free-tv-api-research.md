# NHL Free-to-Air TV Schedule in Finland — API & Data Source Research

**Date of research:** 2026-10-18
**Question:** Is there a usable API/data source for a recurring (not just "today") service that answers "Is tonight's NHL game airing free on MTV Sub, STAR Channel, or Viaplay TV in Finland?"

## Bottom line

- **No single official, documented, public API covers Finnish broadcast listings for all three channels (MTV Sub, STAR Channel, Viaplay TV).**
- The NHL's own public schedule API is a solid, verified primary source for the **game schedule** (fixtures, times, venues) but does **not** carry Finnish broadcaster/channel assignments.
- Each Finnish channel would have to be handled separately. Only one first-party *observable* (undocumented) data endpoint was located (Viaplay's internal content API); MTV's and STAR Channel's own EPG/API surfaces could not be located or reached in this session.
- A third-party Finnish EPG aggregator (telkku.com) is the most promising lead for a one-stop cross-channel source, but its terms of use and any API/feed were **not verified** — this is a priority gap, not a confirmed finding.
- Research in this session was cut short before several of these gaps could be closed (see **Gaps** below); treat unresolved items as open follow-ups, not as "no API exists."

## Findings by source

### 1. NHL official schedule API — verified working
- Live fetch confirmed: `https://api-web.nhle.com/v1/schedule/2026-10-18` returns valid JSON for the surrounding game week (teams, venue, `startTimeUTC`, `gameState`, etc.).
- Each game includes a `tvBroadcasts` array, e.g. for CBJ @ MIN (2026-10-18): entries with `"countryCode":"US","network":"Wild+"` and `"countryCode":"CA","network":"SN+"/"TVAS"`. **Only US/Canadian broadcaster codes were present in every game inspected — no Finnish/European entries.**
- **Conclusion:** reliable for *which games exist and when (UTC)*, not for *which Finnish channel airs them*. This endpoint is the modern, unofficial-but-widely-used successor to the old `statsapi.web.nhl.com`; no published ToS/rate-limit documentation was located for it in this session, so production reliance would need separate verification.

### 2. MTV Sub (MTV Oy)
- MTV Sub's existence as an MTV Oy channel is confirmed via MTV Oy's own corporate description (search-index snippet of `mtvyritys.fi`): "MTV Oy on Suomen johtava kaupallinen tv- ja suoratoistopalveluyhtiö: MTV Katsomo, MTV3, MTV Sub, MTV Ava, MTV Uutiset…" — https://www.mtvyritys.fi/
- Direct fetches for a program-guide/EPG page or API all failed: `mtv.fi/ohjelmaopas` (404), `mtv.fi/ohjelmat/mtv-sub` (404), `mtv.fi/sarja/mtv-sub` (404), `mtvuutiset.fi/ohjelmaopas` (404). mtv.fi currently functions primarily as the MTV Katsomo streaming catalog, not a linear TV guide at the URLs tried.
- **No MTV-operated public API or EPG endpoint was located.** The correct URL for MTV's linear TV guide (if one exists) was not found before the session ended — open follow-up.

### 3. STAR Channel (Finland)
- Channel's own domain confirmed via search index: `starchannel.fi`, with a referenced programs page `www.starchannel.fi/ohjelmat` ("Tuoreimmat tiedot STAR Channel -ohjelmista").
- **Both `https://www.starchannel.fi/` and `https://www.starchannel.fi/ohjelmat` returned HTTP 503 on every direct fetch attempt** in this session — could not confirm page structure, any underlying JSON/XML feed, or whether this channel currently carries any NHL games at all.
- **Unverified:** STAR Channel's operator/owner and whether it carries NHL for the 2026-27 season — no primary-source confirmation obtained.

### 4. Viaplay / "Viaplay TV"
- `https://viaplay.fi` is Viaplay's Finland site. Fetching `https://viaplay.fi/watching` returned its embedded Next.js client config (`window.viaplay.settings`), which exposes first-party backend hosts the official web client itself calls:
  - `content.viaplay.fi` — content/editorial API (catalog; plausibly includes linear-channel/EPG data)
  - `play.viaplay.fi` — stream API
  - `login.viaplay.fi` — device/login API
  These are **undocumented, internal endpoints** used by Viaplay's own frontend, not a published developer API.
- A direct probe of `https://content.viaplay.fi/fi/` returned **HTTP 400**, indicating the endpoint exists but needs additional path segments/parameters not determined in this session.
- A search-index snippet (not directly fetched) of `viaplay.fi/urheilu` shows Viaplay's own sports page renders a live, human-readable schedule (e.g., "Live 04.55–09.00 Leeds – Man United", "Valioliiga Viaplay Premier Sunday"), confirming the site publishes a browsable live-sport schedule — but NHL listings and any distinction between the paid streaming app and the "Viaplay TV" linear channel brand were not confirmed.
- **Unverified:** whether Viaplay still holds Nordic/Finnish NHL broadcast rights for 2026-27, whether any NHL content on "Viaplay TV" is actually free-to-air vs. subscription-gated, and the terms of use covering programmatic access to `content.viaplay.fi`.

### 5. Third-party aggregators (leads, not confirmed)
- `telkku.com` (a well-known Finnish all-channel TV guide) redirects to `iltalehti.fi/telkku`, indicating it's operated under Alma Media/Iltalehti. Its page structure, any JSON/API feed, and its terms of use regarding automated reuse were **not confirmed** in this session — this is the single most promising lead for a one-stop source spanning all three channels, but it remains unverified and most EPG-aggregator sites restrict scraping/reuse in their ToS, so this must be checked before relying on it.
- `https://www.suomikiekko.com/nhl/` — confirmed live; returns a plain-text table of historical NHL game results, but no Finnish broadcast-channel assignment data was visible.
- `https://www.nhlsuomi.com/` — described in search results as a Finnish NHL news/"teksti-tv"-style page; a direct fetch of the homepage returned only footer/legal boilerplate (content is likely JavaScript-rendered), so its usefulness as a broadcast-schedule source is unconfirmed.

## Game schedule vs. broadcast schedule (clarification)

- **Game schedule** (fixture list: teams, date/time, venue) is reliably available, free, from the NHL's own API (`api-web.nhle.com`) — verified working, no Finland-specific gap.
- **Broadcast schedule** (which Finnish channel, if any, airs a given game, and whether it's free) is a *separate* dataset that the NHL API does not provide. It must be sourced per-broadcaster (MTV, STAR Channel, Viaplay) or from a Finnish EPG aggregator — no official, cross-broadcaster API for this was found in this session.

## "Free" definition used

Treated as **free-to-air / included in a standard antenna or basic pay-TV tier**, without a separate paid sports or streaming subscription. MTV Sub and STAR Channel are commonly distributed in Finland as free-to-air channels. "Viaplay TV" denotes Viaplay's linear-channel brand, distinct from its paid streaming app — but whether its content airs free-to-air (vs. requiring a Viaplay subscription or pay-TV package) was **not confirmed** from a primary source in this session and should be verified before being asserted in any product copy.

## Recommendation (brief)

No ready-made, documented, public API satisfies the full requirement (NHL fixtures + free-TV status across all three Finnish channels). A maintainable recurring service would need to combine:
1. The NHL's public schedule API for fixtures (reliable, verified).
2. Either (a) a confirmed, ToS-compliant EPG aggregator (e.g., telkku.com, pending ToS/API review) or (b) direct, per-broadcaster parsing of MTV's, STAR Channel's, and Viaplay's own program-guide pages — none of which currently expose a documented public API, so each would need bespoke, maintenance-prone scraping of undocumented/first-party endpoints.
3. A periodic, manual/editorial check of the current season's Finnish NHL broadcast-rights holder(s), since no API surfaces "rights holder" as structured data and rights can change season to season.

**Priority follow-ups before implementation:** (1) confirm telkku.com/Iltalehti's terms of use and whether a feed/API exists; (2) locate MTV's actual EPG/program-guide URL; (3) retry STAR Channel's site (503 may be transient or bot-blocking) or find its operator's press/EPG data; (4) confirm the current (2026-27 season) Finnish NHL broadcast-rights holder(s) from a primary broadcaster source.

## Gaps / uncertainties (explicit)

- STAR Channel site was unreachable (503) throughout this session — unconfirmed whether it carries NHL at all, or who operates it.
- MTV's TV-guide/EPG URL was not located (all guessed paths 404'd).
- Viaplay's `content.viaplay.fi` API requires parameters that were not determined (400 on bare path); its terms of use were not reviewed.
- telkku.com/Iltalehti's page structure, any API/feed, and terms of use were not reviewed.
- The current (2026-27 season) Finnish NHL broadcast-rights holder(s) were not confirmed from a primary source.
- This research session was curtailed before these gaps could be closed; this note reflects only what was directly verified, with gaps explicitly flagged rather than assumed.

## Citations

- NHL schedule API (live fetch, 2026-10-18): https://api-web.nhle.com/v1/schedule/2026-10-18
- MTV Oy corporate description (search-index snippet): https://www.mtvyritys.fi/
- MTV guessed EPG/program URLs (all 404, for the record): https://www.mtv.fi/ohjelmaopas , https://www.mtv.fi/ohjelmat/mtv-sub , https://www.mtv.fi/sarja/mtv-sub , https://www.mtvuutiset.fi/ohjelmaopas
- STAR Channel site (unreachable, HTTP 503 at fetch time): https://www.starchannel.fi/ and https://www.starchannel.fi/ohjelmat
- Viaplay Finland "watching" page (embedded client config revealing content/play/login API hosts): https://viaplay.fi/watching
- Viaplay Finland sports page (search-index snippet only, not directly fetched): https://viaplay.fi/urheilu
- Viaplay content API probe (HTTP 400): https://content.viaplay.fi/fi/
- telkku.com (redirects to, structure/ToS not confirmed): https://www.iltalehti.fi/telkku
- suomikiekko.com NHL results page (confirmed live, scores only, no FI broadcast data seen): https://www.suomikiekko.com/nhl/
- nhlsuomi.com homepage (JS-rendered, content not captured by raw fetch): https://www.nhlsuomi.com/
