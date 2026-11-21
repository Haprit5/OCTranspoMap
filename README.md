# Ottawa Rapid Transit Map

An interactive map of Ottawa's rapid transit — light rail, the Stage 2
extensions under construction, and the rapid bus corridors — built with the
Google Maps JavaScript API. Single static page, no build step, no dependencies.

## What's on the map

Three kinds of thing, each independently toggleable from the sidebar — the
open rail lines one card apiece, Stage 2 and rapid bus one card each. Only the
first is the subject; the other two are context, and say so in how they're
drawn.

### Open rail (`NETWORK`)

| Line | Span | Stations | Length | Colour |
| --- | --- | --- | --- | --- |
| Line 1 — Confederation | Tunney's Pasture ↔ Blair | 13 | 12.3 km | `#DA291C` |
| Line 2 — Trillium | Bayview ↔ Limebank | 11 | 19.1 km | `#76BC42` |
| Line 4 — Airport Link | South Keys ↔ Airport | 3 | 4.1 km | `#01A1DD` |

25 unique stations — Bayview and South Keys each appear on two lines and are
drawn once. Lengths are measured along the drawn alignment, not summed between
stations.

### Stage 2 — under construction (`FUTURE`)

| Project | Span | Stations | Length | Colour |
| --- | --- | --- | --- | --- |
| Line 1 — West Extension | Tunney's Pasture → Algonquin | 7 | 8.9 km | `#DA291C` |
| Line 3 — Moodie branch | Lincoln Fields → Moodie | 4 | 6.0 km | `#947200` |
| Line 1 — East Extension | Blair → Trim | 5 | 12.4 km | `#DA291C` |

Off by default — the open network is the subject. Drawn dashed with hollow,
half-lit dots, and its names rank below every open station, so a station that
isn't running yet never displaces one that is. West of Tunney's Pasture, Lines
1 and 3 share a trunk to Lincoln Fields and split there; the trunk is drawn
once, with Line 1, rather than as two overlapping dashed lines.

### Rapid bus (`TRANSITWAY`, `TRANSIT_STATIONS`)

Eight corridors, 121 km, plus 35 transit stations. Eight are shared with open
rail (Hurdman, Tunney's Pasture, South Keys, Greenboro, Walkley, Limebank,
Lincoln Fields, Bayshore) and tagged `rail` in the station list; five more —
Trim, Place-d'Orléans, Jeanne-d'Arc, Montréal and Moodie — are Stage 2 stations
under construction that already serve buses today, and are tagged `Stage 2`.

Those five have to be named **character for character** as their `FUTURE`
counterparts. The label overlay de-duplicates by name, so "Place d'Orléans"
here against "Place-d'Orléans" there would print the same station twice, a few
metres apart. With the names matching, whichever layer places first claims the
label: Stage 2 outranks rapid bus, so with that layer on the future name wins,
and with it off the bus name appears at zoom 15.

Montréal is the exception in the set. OC Transpo publishes no station for it —
only a stop pair on Montréal Rd at R.R. 174 (stop codes 1920 and 2461), which
happens to sit where the Stage 2 station is being built. Nine routes call
there, so it earns a dot, but it is a stop pair rather than a designated
station and the code says so.

| Corridor | Span | Length | Routes |
| --- | --- | --- | --- |
| West Transitway | Tunney's Pasture ↔ Kanata | 22.8 km | 20 |
| Southeast Transitway | Tunney's Pasture ↔ Limebank | 24.0 km | 17 |
| Southeast spur | South Keys ↔ Hurdman | 8.1 km | 16 |
| 61 spur | Transitway → Terry Fox | 1.1 km | 7 |
| 63 | Moodie → Teron → Innovation | 11.6 km | 1 |
| 34 | Chapel Hill → Blair | 15.2 km | 1 |
| 39 | Millennium → Blair | 21.5 km | 1 |
| 75 | Baseline → Barrhaven → Minto | 16.7 km | 1 |

On by default, unlike Stage 2: the Transitway is running today and is part of
how the city actually moves. Deliberately understated all the same — a neutral
grey at a third the weight of the rail, at `zIndex: 0` so every route draws
over it. Its station names don't appear until zoom 15, late enough that they
never crowd a rail name.

The card expands into its stations like a rail card does, but each row carries
the routes calling there instead of a street address. For a bus interchange
that *is* the useful fact, and it's the one thing the map can't show without
being clicked. Rows are searchable by route as well as by name, so typing `63`
finds every station the 63 calls at — the reverse of what the row itself shows.

Corridors thicken on hover like the rail lines do, but by +1.8 px rather than
+2.5. At 1.6 px a train line's growth reads as a different layer appearing
rather than as the one under the cursor responding; roughly doubling says
"clickable" without letting a context layer outweigh a route.

Clicking a corridor names the routes using *that* stretch — it differs section
by section, from one route on the 63 section to 22 on the west Transitway, so
the answer belongs on the section you clicked rather than in a global list.

**Every route number is a link** to that route's schedule and map on
octranspo.com, in all three places numbers appear — the corridor popup, the
station popup, and the sidebar list. All three render through `routeChips()`
rather than each growing its own copy of the anchor. The schedule URL takes a
date, and it's stamped at click time rather than baked in: a hard-coded
`YYYYMMDD` starts asking for a past day's timetable the morning after it's
written, and this page has no build step to refresh it.

They're styled as chips, not as links. Underlining a grid of two-digit numbers
turns it into noise, so the affordance is carried by the hover state. The chip
exists twice in the stylesheet — `.iw .route` for the info window, which Google
paints on a white card in both themes and so uses fixed colours, and
`.bus-routes .route` for the sidebar, which uses the theme tokens.

### Where the colours come from

Sampled from OC Transpo's own artwork, not eyeballed. Line 1 is the fill of
their line badge (`images/files/route_types/line1_empty.png`), Line 2 the fill
of their R2 rail route icon, Line 4 their site-wide blue.

Their badge artwork also carries darker takes on the same two hues — `#65A233`
green and `#057EB6` blue. Both are equally "official"; the lighter variants
above read closer to the printed network map. Worth knowing before someone
"corrects" these to the badge values.

The rapid bus grey is the exception: it isn't an OC Transpo colour at all, and
it's the one route colour that changes with the theme. It lives in `PALETTES`
as `transitway` rather than on the data, because a fixed grey legible over the
light basemap disappears into the dark one. The CSS token `--busline` mirrors
it for the legend, the sidebar swatch, and the address panel's bar; the two
have to be kept in step by hand, and each carries a comment saying so.

### Station coordinates

The `stop` nodes of the OpenStreetMap route relations — the point on the track
where the train actually berths. That's what keeps every dot on its line: each
sits within 3.7 m of the drawn path, where the previous Wikipedia-infobox
coordinates were up to 190 m off it. They mark the stopping point, not a
station entrance or building centroid.

Bayview and South Keys have a stop node on each of their two lines, 10 m and
0 m apart respectively; the first line to reach the station supplies the shared
coordinate, matching how the renderer de-duplicates transfer stations.

Each station also carries an `addr` string. These are **street locations and
cross-streets, not civic addresses** — most O-Train stations don't have a
street number, and the ones that do aren't consistently published. They come
from the stations' Wikipedia articles (infobox `Address` where present,
otherwise the article's own description of the location); Leitrim and
Parliament are the only two with a formal address field, and Lees and Hurdman
are the thinnest, giving only the road the station sits on. Treat them as
orientation, not as postal addresses. The directions links don't depend on
them — those use coordinates.

Rapid bus station dots sit **on the line**, at the point of the corridor
closest to OC Transpo's published coordinate for that station. All 35 are
within 0.5 m of a drawn corridor.

That is the same rule the rail uses, for the same reason. Rail dots are OSM
`stop` nodes — where the train berths — not station centroids, which is what
keeps every dot on its track. A bus station's published coordinate is the
centroid of a platform cluster set back from the road, so it sits off the
corridor by up to 126 m (Riverview) and reads as a dot floating beside the
line. Projecting it onto the corridor puts it where the bus actually stops.

The cost is that the stored coordinate is displaced by that same amount, and it
is also what the directions links and the address-search distances use. At a
worst case of 126 m — Riverview and Hurdman, both stations whose bus loop
genuinely sits that far off the road the corridor follows — that is within a
block, and the alternative was a visibly wrong dot.

The published coordinates come from the station entries (`location_type=1`) in
OC Transpo's GTFS feed, the same source as `STATION_ROUTES`. They were
previously derived from OSM as the median of each station's platform nodes, and
the trap there is worth keeping written down. A station bay is named
`<Station> <bay>` — `Teron 1A`, `Innovation B`, `Terry Fox 3C` — and the bays of
one station share its stop code in `ref`. An ordinary on-street stop is named
`<Street> / <Cross street>`. Taking the median of everything whose name merely
*starts with* the station name sweeps in every stop on a road of the same name
and drags the coordinate down that road: it put **Teron 858 m north** on Teron
Drive and **Innovation 332 m east** on Innovation Drive.

Eight stations are `shared` with rail and so carry two dots. Hurdman's sit
about 120 m apart, which is right rather than a bug: its bus loop genuinely is
that far from the LRT platform. The label de-duplicates, so the name still
prints once.

## Setup

1. Create an API key in the [Google Cloud console](https://console.cloud.google.com/google/maps-apis/credentials)
   with the **Maps JavaScript API** enabled. For address search, also enable
   the **Places API (New)**; the map itself works without it, and the search
   box still filters stations.
2. Copy the key into a local config file:

   ```bash
   cp config.example.js config.js
   # then edit config.js and paste your key
   ```

   `config.js` is gitignored, so the key stays out of version control.

3. Serve the folder over HTTP:

   ```bash
   python3 -m http.server 8000
   ```

   Then open <http://localhost:8000>.

### When the map doesn't load

`gm_authFailure` fires for every kind of auth rejection without saying which,
so the on-page message names the origin Google actually rejected. Almost always
that origin simply isn't on the key's allowlist — add it under **Credentials →
your key → Website restrictions** as `http://host:port/*`.

Two cases that catch people out:

- **Opening `index.html` directly.** A `file://` URL can't be matched against a
  referrer allowlist at all. Serve over HTTP instead.
- **Testing from a phone.** `http://192.168.x.x:8000` is a different origin from
  `http://localhost:8000` and needs its own allowlist entry. The address is a
  DHCP lease, so it can change; `ipconfig getifaddr en0` reports the current one.

## Features

- Route polylines follow the real track alignment — curves, the swing around
  Dow's Lake, the loop into the airport — with tunnelled sections drawn dashed.
  Routes thicken on hover, so they read as a click target.
- Station dots and tunnel dashes both drop away below zoom 12, so the
  zoomed-out view is clean lines. 12 is the lowest that still works: the
  closest pair of stations on the network is 18 px apart there against 11 px
  dots, and by 11.5 they touch.
- Station markers — hollow dots for regular stops, larger filled dots for
  transfer stations (Bayview, South Keys). Rapid bus stations invert the same
  circle, a solid navy centre inside a white ring, so they read as a different
  kind of stop without needing a different shape. Names are drawn permanently
  beside each dot, so they read without a mouse. Clicking opens the street
  location and connections plus links to transit directions, walking
  directions, and the station on Google Maps; with an address pinned, those
  directions start from it instead of your current location.
- Sidebar with per-layer visibility toggles, expandable station lists, and
  search. Rail, Stage 2 and rapid bus all expand; the rapid bus list gives each
  station the routes calling there, as links to their schedules. A hamburger at
  the map's top-left folds it away, an **×** in the sidebar's own corner brings
  it back — only one is on screen at a time. The choice persists in
  `localStorage`.
- A fixed compass, stacked over the legend. The map can't be rotated or
  tilted, so north is always up and the compass never needs to move; it stands
  down inside Street View along with everything else that doesn't apply there.
- Line details popup — click a route on the map (or the ⓘ on a sidebar card)
  to isolate that line, frame it in view, and open a card with station count,
  length, average stop spacing, termini, underground section, interchanges, and
  the full stop list in order. With an address pinned, it also names the nearest
  stop on that line.

  **Isolating means every other layer**, not just the other rail lines: the bus
  corridors and Stage 2 both go down too. Between them they carry more ink than
  the network does — eight corridors and three dashed projects — so leaving
  them up isn't isolating anything.

  Closing restores what was showing beforehand rather than blanket-enabling
  everything, so a layer you had deliberately switched off stays off. The
  snapshot covers all five toggles, and the sidebar switches follow.
- Address search — type a street address and pick a suggestion to drop a pin,
  see the closest station on each line **and on the rapid bus network** with
  distance and an estimated walk time, and get a dashed connector drawn to
  each. Distances are straight-line, not walking routes; the walk estimate
  applies a 1.3× detour factor at 4.8 km/h, so treat it as a ballpark. Falls
  back to the Geocoding API (if enabled) when Places is not available.
- Light / Dark theme toggle, a Map / Satellite base map toggle, and an EN / FR
  language switch, with route and marker contrast adjusted for aerial imagery.
  The language switch reloads, which is the only way to bring Google's own
  basemap labels along — see [Bilingual](#bilingual).
- Street View via the Pegman control. The routes don't appear in a panorama —
  map overlays never do — and the legend and map controls stand down while it's
  open, since none of them apply there.
- Accessibility marks — every open O-Train station is wheelchair accessible,
  so each carries a quiet grey ♿ in the station list and a blue callout in its
  info window, plus a link to OC Transpo's alerts page for elevator outages.
- Responsive layout. Under 760px the map takes 58dvh and the sidebar flows
  below it, scrolled by the page rather than its own nested scroller; fold the
  sidebar away and the map takes the whole viewport. The legend is desktop-only.

## Bilingual

Ottawa is officially bilingual and OC Transpo publishes everything twice, so
the map does too. An **EN / FR** control sits under the theme switch; the
choice persists in `localStorage` under `otm-lang`.

**Switching reloads the page**, and that is a deliberate constraint rather than
a shortcut. The Maps JS API fixes its label language from the loader's
`language` parameter when the script loads and exposes no setter afterwards —
so a live switch would leave "Rivière des Outaouais" reading "Ottawa River"
under a French sidebar. Reloading costs a pinned address and the current
pan/zoom; it buys street, river and place names in the right language.

The upside is that there is no re-render pass anywhere in this file. Every
render happens after load, so `t()` is read once per string and nothing needs
re-rendering when the language changes — because the language never changes
without a reload.

### What's translated

| | |
| --- | --- |
| UI chrome | header, search, footer, legend, all three segmented controls, both popups, the line panel, the address panel, the auth-failure messages |
| Line names | `nameFr` on `NETWORK` and `FUTURE` |
| Station names | `nameFr` — only three differ: Parliament/Parlement, Dow's Lake/Lac Dow, Airport/Aéroport |
| Station prose | `addrFr` (27) and `connFr` (21), plus `alertFr` (2) |
| Accessibility | `ACCESS.labelFr`, `detailFr`, and a `/fr/alertes/` link |
| Basemap | Google's own labels, via the loader's `language` |
| Outbound links | OC Transpo schedules use their `/fr/` path and `sched-lang`; Google Maps links carry `hl` |
| Numbers | `1,2 km` with a comma and a non-breaking space, via `toLocaleString` |

### Two rules worth keeping

**Station `name` is identity, not display text.** It keys `stationIndex`,
`STATION_ROUTES`, the label de-duplicator and every `data-station` attribute.
So `name` stays English everywhere and `nameFr` is read only through
`stationName()`. Translating `name` in place would silently break the sidebar
row → marker lookup, the shared-station de-duplication, and the address
panel's row dispatch.

**French falls back to English per key**, not per language. A missing
translation shows English prose rather than a bare identifier like
`straightLine`.

Two things are derived rather than stored twice, because a hand-kept second
copy is what drifts in one language and not the other: the legend's line rows
come from `NETWORK`, and a line's span in the sidebar comes from its terminus
station names — which is why Line 4 reads "South Keys ↔ Aéroport" in French
without a `spanFr` field existing.

Search matches **both** languages at once, so "Airport" and "Aéroport" each
find the station whichever language is showing.

## Rapid bus in the address search

The nearest way out of a neighbourhood is often a bus station, so the address
panel measures the rapid bus network alongside the three rail lines rather than
answering the narrower "how close is the train?".

The whole layer answers as **one pseudo-line**, `TRANSITWAY_LINE`. Its
corridors have no individual identity — one stretch of Transitway carries 22
routes — so "which corridor" isn't a question a rider asks; "how far to a rapid
bus station" is. It's shaped like a `NETWORK` entry so `nearestPerLine` and
`drawConnector` don't need to know the difference, minus the fixed `color`:
this one is a palette entry, so `lineColour()` and `lineVisible()` cover the
two places where a pseudo-line can't answer the same way as a rail line.

Three consequences worth knowing:

- **A shared station can appear twice**, once per row — a rail row and a rapid
  bus row both naming Hurdman. That's the honest answer, not a duplicate: it
  says both networks serve it. Dropping the eight `shared` stations from the
  bus calculation instead would report some farther station as the nearest
  rapid bus stop, which is simply false.
- **The rows dispatch on their own line, not on the station name.** A shared
  station exists in both `stationIndex` and `transitLabels`; the rapid bus row
  has to open the bus popup even though a rail entry of the same name is
  sitting right there.
- **The bus popup can be opened without its marker.** Rapid bus stations leave
  the map when the layer is toggled off or the zoom drops below
  `DETAIL_MIN_ZOOM`, but the panel row survives both, so `openTransitStation`
  falls back to positioning the bubble by coordinate rather than anchoring it
  to a marker that isn't there.

## Station name labels

Names thin out by importance as you zoom out, so the map stays readable at
every scale. Importance is derived from data already present — `transfer`,
being first or last on a line, then whether the station has a `conn` — so a new
station tiers itself.

Two questions, answered separately, because they are different questions: a
**threshold** (`STOP_LABEL_ZOOM`) decides when a name becomes eligible, and a
**rank** (`labelPriority`) decides who wins when two eligible names overlap.

| Zoom | Newly eligible | Count |
| --- | --- | --- |
| 10+ | termini — but not Bayview or South Keys | 4 |
| 12+ | interchanges, and anything with a `conn` | 19 cumulative |
| 12+ | Stage 2 stations, when that layer is on | 16 |
| 13+ | everything else on the open network | 25 cumulative |
| 15+ | rapid bus stations | 35 |

Not everything eligible gets drawn — `draw()` places names in rank order and
skips any that would overlap one already placed, so at 12 a good third of the
19 lose their collision and stay silent.

Termini arrive two steps ahead of everything else, below even
`DETAIL_MIN_ZOOM`, so the far view is four names on three lines and nothing
else — deliberately without dots, the way a regional rail map just runs the
line out to its name. Bayview and South Keys are technically line ends but sit
in the middle of the map, so naming them out there would read as arbitrary;
they wait their turn with the other interchanges.

Terminus needs its own rank as well as its own threshold. Without one it ties
with every other stop at the same threshold and the tiebreak falls to
alphabetical order — which is how "Airport" once lost its slot to a neighbour.

Below 12 the dots go too, so no name is ever left pointing at nothing.

Rank puts the open network first (terminus, interchange, `conn`, then the
rest), Stage 2 fourth, and rapid bus last: a bus stop should never displace a
train station, and a station that isn't open yet should never displace one that
is. Rapid bus also gets the latest threshold of anything on the map, 15, so
its 35 names never crowd rail at the zooms where rail is the point.

All of them — open, Stage 2, rapid bus — share **one** overlay and therefore
one collision pass. Two overlays would happily print a future name straight
through a live one. A stop served by several layers has an entry per layer;
whichever is placed first claims the name and the others stay silent, so a
shared station reads once no matter how many layers it belongs to.

**Framing a line names every one of its stops**, whatever the zoom. Line 2 is
19 km and frames at zoom 12, below where its minor stops would normally be
named, and reading the stops is the point of framing it. Stops of a framed line
also get first claim in the collision pass, so a name from another line can't
crowd out one you're looking at.

All labels live in one `OverlayView` rather than one per station — a single
projection lookup per redraw. Widths are measured once on creation; reading
`offsetWidth` during `draw()` would force a reflow on every pan frame.

Collisions depend only on relative pixel offsets, which depend only on zoom, so
panning never makes a name flicker.

## Touch and iOS

- Hover is feature-detected, not assumed. On a touch device the hover styling
  and the dot's hover growth are never wired up at all — touch fires
  `mouseover` on tap and never `mouseout`, which would leave a dot stuck
  enlarged. Names are drawn permanently, so nothing depends on hover.
- `:hover` highlight styles are cancelled under `@media (hover: none)`, since
  iOS keeps them applied to whatever you tapped last.
- Dismissal and autocomplete selection listen for `pointerdown`, not `click` or
  `mousedown` — Safari is unreliable about bubbling taps on non-interactive
  elements to document-level click listeners.
- `gestureHandling: "greedy"`, so panning the map takes one finger instead of
  two.
- Viewport heights use `dvh` with a `vh` fallback, so Safari's collapsing
  address bar doesn't crop the map; `viewport-fit=cover` plus a
  `safe-area-inset-bottom` pad keeps the sidebar clear of the home indicator.
- The search field is 16px on phones. Anything smaller makes iOS zoom the whole
  page when the field takes focus.
- `backdrop-filter` is paired with `-webkit-backdrop-filter` throughout.

## Accessibility data

Rail only. Nothing here has been verified for the rapid bus stations, so they
carry no accessibility claim at all rather than inheriting the rail one.

Per [OC Transpo](https://www.octranspo.com/en/our-services/accessibility/station-accessibility-features/),
all O-Train stations have level boarding (the platform sits flush with the
train floor), two elevators between each pair of levels so one outage doesn't
strand anyone, tactile wayfinding tiles, and accessible fare gates. That is a
network-wide property, not per-station data, so it lives in the `ACCESS`
constant rather than being repeated on all 25 stations. A station can override
it with `access: false`.

Escalators are **not** modelled. OC Transpo says 9 stations have them but
publishes no list I could verify per station, and a wrong escalator claim is
worse than none for someone planning a trip. Elevator *status* changes daily
and isn't baked in either — that's what the alerts link is for.

## Track geometry

Everything drawn on this map comes from
[OpenStreetMap](https://www.openstreetmap.org/copyright), but from four
different queries, and the differences between them are worth knowing before
anyone regenerates one.

### Open rail

Each line carries an encoded polyline in `path`, plus `tunnelPaths` for the
sections drawn dashed. These are the real alignments, taken from the O-Train
route relations (relations 10036401, 16190573, 16176187), stitched in member
order and simplified to a 3 m tolerance — 993 points down to 177, which is
visually identical at any zoom this map uses. Tunnel sections are the ways OSM
tags `tunnel=*`, so the dashes match the actual bores rather than a guess at
which stations are underground.

Sanity checks worth repeating if you regenerate them: the measured lengths
(12.3 / 19.1 / 4.1 km) match the published route lengths, and every station
sits within 4 m of its line's path. Take the stations and the alignment from
the same source — mixing coordinates from elsewhere is exactly what put dots
beside their lines before.

### Stage 2

`railway=construction`, with the branch topology checked against OC Transpo's
own Stage 2 west map.

The East Extension is the trap: it's mapped as `railway=light_rail`, not
`railway=construction`. **Filtering on construction alone finds a 40 m stub and
misses the other 12 km.**

### Rapid bus corridors

Three kinds of source, which is why the shapes differ in character:

- **Whole bus routes** (57, 74), stitched from their route relation's member
  ways *in stored order*. Order matters: tracing a shortest path between the
  endpoints instead cuts any loop the route makes to serve a station.
- **Transitway proper** — segments tagged `highway=service` with
  `name=Transitway` and `operator=OC Transpo`, reduced to one spine per
  component.

#### One spine per component

The Transitway's two directions are mapped as separate service ways, so the
reduction to a single spine is what stops the same carriageway being drawn
twice. It doesn't always succeed, and the failure is quiet: you get a second
corridor running parallel to a real one, tens of metres away.

A 2.0 km entry from Lincoln Fields to Iris was one of these and has been
deleted. Two things gave it away — it carried **no routes at all**, and every
one of its 16 points sat within 55 m of another corridor, most within 20 m. Both
are cheap to check on any corridor that looks like it ends nowhere in
particular.

#### Corridors that end nowhere

Not every dangling end is an artifact, and the two failure modes need different
fixes. Run the endpoint audit — distance from each corridor end to its nearest
station — and then work out *why* each one dangles.

The 57 corridor was the other kind: real service, wrong extent. Stitched whole,
it ran 4.75 km past its last station out to a residential loop in Bells Corners
(Black Forest Lane, Harwick Crescent, Northside Road), ending at ordinary
on-street stops with no station within 1.2 km.

Trimming it back to Bayshore was not enough — the remainder still began 266 m
short of the station, on Holly Acres Road, and still read as a line starting
nowhere. **It has since been deleted outright.** That was the right call in the
end for a reason the trim made obvious: from Lincoln Fields north it ran within
0–44 m of the Transitway corridors, so once the Bells Corners tail was gone
almost nothing was left that another line wasn't already drawing.

The lesson generalises. A whole-route corridor is worth drawing only where it
diverges from the busway; where it rejoins, it is duplicate ink. Check the
overlap before adding one.

Two ends still dangle by more than 300 m, both deliberately:

| Corridor | End | Distance | Why |
| --- | --- | --- | --- |
| West Transitway | Hazeldean Rd, Kanata | 1885 m | where the Transitway data ends; trimming it would drop real Kanata busway |
| 75 | Minto Rec Centre | 1745 m | a genuine terminus that simply isn't a station |
| 61 spur | on the Transitway | 618 m | by design — it branches off a corridor, not a station |
| Southeast spur | on the Transitway near South Keys | 409 m | on the busway itself, just short of the station |
- **Named sections of a single route**, sliced out of one direction's relation
  and trimmed at a station. Five of these: the 61's 1.1 km spur into Terry Fox,
  the 63's 11.6 km from Moodie through Teron to Innovation, the 34's 15.2 km
  from Chapel Hill to Blair, the 39's 21.5 km from Millennium to Blair, and the
  75's 16.7 km from Baseline through Barrhaven to the Minto Rec Centre.

Note the tagging: the Transitway is **not** `highway=busway`. Querying busway
in an Ottawa bounding box returns Gatineau's Rapibus instead — a different
agency across the river — which is a convincing-looking wrong answer if you
don't check where the coordinates land.

#### Slicing a section out of a route

Take it from **one direction's relation**. Stitching both gives a doubled path,
since the two directions run on different ways wherever the road is divided.

Stitching a relation's ways in member order yields several disconnected runs,
not one — the 63's Tunney's Pasture → Innovation relation breaks into five,
because the route shares ways with others and OSM splits them. The section you
want is generally one whole run; find it by which run reaches the stations you
care about rather than by assuming it's the longest.

**Sometimes it isn't one run, and the runs aren't in order.** The 34's
Renaud → Blair relation stitches into three, and taken in stored order there's
an apparent 795 m gap that makes the route look unusable. The runs actually
chain 0 → 2 → 1, joining at 2 m and 0 m. The 75's is five runs, of which
2 → 3 → 4 chain at 25 m and 6 m. Before concluding geometry is missing, check
whether a later run continues an earlier one.

**Not every stop has role `platform`.** A terminus is often
`platform_entry_only` or `platform_exit_only` — the 75's Minto Rec Centre is
both, one per direction. Filter on `role == "platform"` and the end of the line
silently disappears from the stop list, which reads as "the route doesn't go
there" rather than as a bug in the filter.

Long straight hops between consecutive points are usually fine, not breaks. The
34 has hops up to 406 m where it runs on Regional Road 174, which is mapped
with few nodes across its gentle curves. Check whether the two points share a
way before treating a hop as a gap.

Then trim each end to a station, by truncating at the run's closest approach to
that station's coordinate. The 61 spur additionally starts at the last point
still within 30 m of the corridor it branches off, so it joins the existing
Transitway rather than starting in mid-air.

Sliced sections start at the same 3 m tolerance as the rail, then go through
the shared cleanup pass below, which is what sets their final vertex counts.

One trap in the encoding itself: a polyline may contain literal backslashes
(the 39's has two). They must be written `\\` in the JS string, which is why
every `path` in this file is escaped rather than pasted raw. Paste one
unescaped and the path silently decodes to something else.

#### Verifying a new section

Three checks catch nearly everything.

1. Confirm the relation you sliced actually reaches the stations you expect —
   see [Routes that aren't what they look like](#routes-that-arent-what-they-look-like).
2. Confirm the corridor ends within ~30 m of each station it terminates at.
3. Confirm every station it passes sits on the drawn line.

For the third, measure **point-to-segment distance, not distance to the nearest
vertex.** After simplification a long straight keeps only its endpoints, so a
station halfway along it can be hundreds of metres from the nearest vertex while
sitting right on the line. Longfields measures 390 m by vertex and 1.8 m by
segment; the segment figure is the one that matches what renders. Every station
on the map is currently within 10 m of its line by that measure.

A corridor that carries no routes at all is worth a second look. The one that
used to run from Lincoln Fields to Iris was exactly that — see
[One spine per component](#one-spine-per-component).

Finally, sieve the route list against the GTFS feed and drop any route with no
trips in it. OSM lags service changes by months: it still listed 155, 251, 284
and 167, all discontinued, and each was rendering as a chip linking to the
schedule for a route that no longer runs.

#### Shared alignments and smoothing

Corridors that run on the same road were the main source of visual noise: eight
corridors produced four overlapping pairs, up to 7.5 km each, drawn 0–40 m
apart. Near-parallel is worse than coincident — it reads as two or three lines
where there is one road.

The fix is not to trim (that would cost each route the span it is named for)
but to make the shared stretch *literally the same geometry*:

1. **Smooth each corridor alone** — drop out-and-back excursions shorter than
   350 m that return to within 45 m of where they left, then simplify at 8 m.
2. **Then snap, in priority order** — most routes first, so the trunks win.
   Any vertex within 60 m of an already-finalised corridor is replaced by its
   projection onto that corridor.

Order matters, and getting it wrong is subtle. Snapping *before* simplifying
looks right and isn't: the later simplification moves each corridor's vertices
independently and pulls the shared stretch back apart. Doing it after leaves
the overlapping runs sharing actual vertices.

Result: shared stretches now draw a mean 2.6–5.9 m apart against a 1.6 px line,
so they render as one. Total vertices fell 626 → 425 and no corridor drifted
more than 45 m from its true alignment.

#### Route lists

Each corridor's `routes` list is computed by intersecting its ways against
every OC Transpo bus route relation, **keeping only routes that cover 90%+ of
it**. The cutoff matters: without it, a route borrowing one way of a 99-way
corridor is listed as running the whole thing. Both new sections show a clean
cliff right at that line — the 61 spur drops 94% → 33%, the 63 section
100% → 28% — so nothing sits awkwardly near the threshold.

### `STATION_ROUTES` comes from GTFS, not OSM

This is the one dataset on the page that does **not** come from OpenStreetMap,
and the reason is that OSM got it wrong at 27 of the 31 stations then on the map.

Source: OC Transpo's own [GTFS feed](https://mobilitydatabase.org/). A route
calls at a station iff some trip in the published schedule stops at a platform
whose `parent_station` is that station. The feed also supplies official station
groupings — 61 of them, `location_type=1` — which removes the guesswork about
which platforms belong to which station.

Rebuild:

1. `routes.txt` → `route_id` → `route_short_name`, `route_type`
2. `trips.txt` → `trip_id` → `route_id`
3. stream `stop_times.txt` (~200 MB, ~3.4 M rows) → `stop_id` → set of routes
4. `stops.txt` → roll each `stop_id` up to its `parent_station`

**Filter to regular bus service**, which is what a station listing means:
`route_type == 3` only, so the O-Train lines don't appear on bus stations;
minus the 400s (Lansdowne event shuttles), the 600s (school specials, ~72 of
them) and 940. Leave the 600s in and Innovation reads as 7 routes, matching
nothing OC Transpo publishes. With the filter it reads 4, which is exactly what
their site shows.

What OSM had wrong, for scale:

| Station | OSM said | GTFS says |
| --- | --- | --- |
| Innovation | 2 | 4 |
| Limebank | 1 | 9 |
| Chapel Hill | 1 | 5 |
| Riverview | 2 | 7 |
| Baseline | 12 | 19 |
| Bayshore | 21 | 15 |

It also still listed **155, 251 and 284** — routes with zero trips in the
current schedule, i.e. discontinued. And it had the Connexion routes (251, 261,
262, 263, 265, 283) calling at Bayshore when they run via the Moodie/Iris
corridor and don't stop there at all.

The feed is a dated snapshot — `feed_info.txt` carries the validity window, and
the one used here runs 2026-05-29 to 2026-08-29. This table ages with it, so
regenerate when routes change rather than editing entries by hand.

#### Routes that aren't what they look like

Check what a route number actually connects before drawing it — a plausible
pairing of station names is not evidence that a route runs between them.

Worked example: **route 24 does not serve Chapel Hill.** In OSM the 24 is four
Blair ↔ St-Laurent variants running via Ogilvie Road and Beacon Hill. Its
closest approach to Chapel Hill station is 5.7 km, and it never touches
Montréal Road. The only bus at Chapel Hill is the **34** (Renaud ↔ Blair) —
which is what's drawn, and it does pass Montréal, within 68 m of the Stage 2
station on its way along Regional Road 174 into Blair.

Cheapest test: stitch every variant of the route, then measure each one's
closest approach to the two stations you expect it to link. If nothing comes
within a few hundred metres, the premise is wrong, not the data.

**OpenStreetMap data is ODbL-licensed and requires attribution** — that's the
credit in the sidebar footer. Keep it if you publish this.

## Theming

Light is the default; the choice is stored in `localStorage` under `otm-theme`
and applied at script parse time, before the Maps API loads.

The default theme lives on bare `:root` and dark is the `.dark` override —
deliberately, since the stylesheet paints before any script runs, so whichever
set sits on `:root` is what appears first. Swapping the default means swapping
which block is the override, not just changing the stored value; otherwise the
page flashes the wrong theme on every load. The sidebar toggle follows the same
rule: the class is `sidebar-hidden`, so the default open state needs no class.

Three surfaces have to change together, and each is reached differently:

- **The DOM** (sidebar, popups, controls) goes through the CSS token block —
  `:root` for light, `:root.dark` for the overrides. Every colour that differs
  between themes is a variable; the only fixed colours left in the stylesheet
  are inside `.iw`, since Google renders the info window on a white card in
  both themes, and `#fff` on saturated line colours.
- **The basemap** is generated by `buildBaseStyle(palette)` rather than written
  out twice, so a rule added there applies to both themes. Same for
  `buildImageryOverrides(palette)`.
- **Markers** are vector symbols drawn by the Maps API, so no CSS reaches them
  — `setTheme` recolours them explicitly via `refreshStationIcons()` and
  `refreshTransitwayColour()`. The latter also has to catch the rapid bus
  address connector, which is the one dashed connector whose colour isn't a
  fixed line colour.

`PALETTES` holds both colour sets, including the satellite scrim. Note the
imagery entries invert per theme: dark mode washes the photo dark and draws
light roads and white labels over it, light mode washes it white and goes dark
on both. Using one set for both would make the labels invisible in one of them.

## Basemap styling

The map deliberately has no `mapId`. A map ID sends styling to the Cloud
console and makes the local `styles` array a no-op, and local control is what
lets the basemap recede: flat ground, dim label text, `labels.icon` off
entirely (that's what removes highway shields and POI pictograms), Google's own
transit layer off so it doesn't compete with ours, and administrative place
captions off because they printed over the station names.

The trade-off is that Advanced Markers require a map ID, so stations are drawn
as vector symbols on `google.maps.Marker` instead. That API is deprecated but
still supported; if it's ever removed, the way back is a Cloud-console map
style plus a map ID. Dropping the map ID also means raster tiles, which have no
fractional zoom — `DEFAULT_VIEW.zoom` is an integer for that reason.

The style is composed at runtime from `buildBaseStyle(palette)` plus modifiers:
`SPARSE_LABELS` when zoomed out, `buildImageryOverrides(palette)` over
satellite.

That second one matters more than it looks. Map styles apply to the satellite
map type as well, so everything tuned for a flat basemap inverts its own intent
over a photo: dark-theme road fills become black lines across the imagery, and
light-theme white roads vanish into a white wash. The imagery overrides flip
the palette accordingly, and switch off the park and water fills so they don't
paint over the photograph. Dark-mode imagery roads are near-neutral greys with
`geometry.stroke` off — there's no width styler in the Maps API, and losing the
casing is the one thing that makes a road render thinner rather than just
dimmer.

The imagery itself can't be styled — no `featureType` addresses the photo — so
it's muted with a world-sized translucent rectangle at `zIndex: 1`, above the
tiles and below the routes (`zIndex: 2`) and tunnel dashes (`3`). The photo
dims; the network doesn't. A CSS `filter` on the map element would have dimmed
the routes along with the imagery and gained no contrast at all. Each palette's
`scrim` and `scrimOpacity` set the colour and strength. Note the scrim sits
above the basemap, so the imagery road colours are pre-wash — what renders is
about 58% of what you type.

Label density has no direct control in the Maps API. The stand-in is
zoom-tiered styling: below `LABEL_ZOOM` (14) local street names are suppressed
and only arterials and highways are named; at 14 and above every street is
labelled. Fewer names on screen at once, nothing permanently nameless.
`LABEL_ZOOM` and `DETAIL_MIN_ZOOM` at the top of the script are the knobs.

## Editing the network

All the data lives in constants at the top of the `<script>` block in
`index.html`. The map, sidebar, legend, labels, and search all render from
them.

| Constant | Holds |
| --- | --- |
| `NETWORK` | the three open rail lines and their stations |
| `FUTURE` | Stage 2 projects, drawn dashed and off by default |
| `TRANSITWAY` | rapid bus corridors, one entry per drawn spine |
| `TRANSIT_STATIONS` | major bus interchanges |
| `STATION_ROUTES` | which routes call at each of those, keyed by name — drives the sidebar rows and both popups |

### `NETWORK`

Per-line fields: `id`, `name`, `nameFr`, `color`, `span`, `path` (encoded
polyline), optional `tunnelPaths` and `tunnel` (station indices, used only for
the popup's "underground" label).

Per-station fields: `name`, `lat`, `lng`, optional `addr` (street location),
`conn` (connections blurb), and `transfer: true`. Station search matches `name`
and `addr` together, so adding a street name makes a station findable by it.

French counterparts are optional and suffixed `Fr` — `nameFr`, `addrFr`,
`connFr`, `alertFr` — and fall back to the English field when absent. Add
`nameFr` only where OC Transpo actually publishes a different name; three
stations do. **Never translate `name` itself** — see
[Bilingual](#bilingual) for why it is an identity key.

This is the only array whose stations join `stationIndex`, and `stationIndex`
is what the sidebar lists, the search filters, and the line-details popup
counts. Adding a station here is enough; it tiers its own label from `transfer`,
its position on the line, and whether it has a `conn`.

### `FUTURE`

Per-project: `id`, `name`, `note` (the span, shown under the name), `color`,
`path`, and `stations` of `{ name, lat, lng }`. One sidebar card covers all of
Stage 2 rather than one per project — it's a single "show me what's coming"
decision, and three more toggles would crowd out the lines that actually run.

Deliberately outside `NETWORK`: nothing here should reach the sidebar station
lists, the search index, or the nearest-station maths. Rows are searchable by
the stations they will serve, so looking up "Moodie" surfaces the project
rather than coming back empty.

### `TRANSITWAY` and `TRANSIT_STATIONS`

A corridor is `{ path, routes }` — no name, no id, because a stretch of
Transitway has no identity of its own. A station is `{ name, lat, lng }` plus
`shared: true` where rail serves it too.

The card expands into a station list sorted north to south, each row showing
the routes calling there. Like Stage 2 these stay out of `stationIndex`, but
**they are measured by the address search** through `TRANSITWAY_LINE` — see
[Rapid bus in the address search](#rapid-bus-in-the-address-search) above.

A station added here shows up as a nearest-station candidate and as a sidebar
row with no further wiring. One added without a matching `STATION_ROUTES` key
reads "no route data" rather than showing an empty box — but if you're adding a
station, regenerate that table too; the row is most of the point.

Deleting a station means removing it from **both** `TRANSIT_STATIONS` and
`STATION_ROUTES`. Nothing else references one by name.
