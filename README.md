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
