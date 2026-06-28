# AEGIS

Ontology-driven emergency-response console. It fuses a **live NASA wildfire feed** into a typed object graph, then an agent reasons over that graph to recommend the next response decision — which a human approves and writes back, closing the loop.

**Live demo:** `https://hanli111.github.io/aegis/`

AEGIS is a working concept demo built for the Palantir Build Challenge — *"how would you build the future? Show, don't tell."* It's a single, self-contained web app: open it and it connects to NASA's live wildfire feed, assembles an operating picture on a real map, and runs a closed decision loop you can drive.

## The problem

A regional emergency-operations duty officer watches three systems that don't talk to each other. CAD dispatch knows where the engines are. The agency GIS knows where people live. A satellite feed knows where the fire is. When the wind turns, someone reconciles all three by hand — and by the time the spreadsheet is current, the fire has moved and the evacuation order is late.

The hunch: if those feeds were one operating picture, decisions would be faster and fewer people would be in the fire's path. AEGIS is what that picture could look like.

## What it does

- Pulls **active wildfires from NASA EONET** (VIIRS/MODIS satellite detections) and resolves each into a typed **Incident** object — real name, coordinates, acreage, detection date, and a link back to the source.
- Fuses those incidents with response units, population zones, and facilities into a single **ontology** of objects connected by typed links — *detected-by*, *threatens*, *assigned-to*, *nearest*.
- An **agent** reasons over that object graph and recommends the next action — which unit to dispatch, which zone to evacuate — and cites the exact objects behind each recommendation.
- You approve. The decision **writes back**: the unit moves, containment climbs, and the "residents at risk" metric drops. Ignore a fire and it spreads; inject a wind shift and the worst one escalates.

## Live vs. representative data

This distinction is the point — it's the integration boundary a real deployment works across, stated honestly:

- **Live** — wildfire incidents are real, fetched at runtime from NASA EONET (keyless, no setup).
- **Representative** — response units, facilities, and population zones stand in for an agency's internal CAD / GIS / census, the systems you'd actually connect on a deployment.

The app labels both clearly, in the opening brief and in the data-integration panel.

## Architecture

AEGIS demonstrates three ideas that data-driven operations platforms (Palantir's among them) are built around — implemented here from scratch, not with any vendor SDK:

1. **A semantic layer, not a map of dots.** Heterogeneous feeds become *typed objects with relationships and actions*, so the system reasons in the operator's vocabulary — incidents, units, zones — instead of rows and pixels.
2. **A grounded agent.** Every recommendation traces back to specific objects in the graph; nothing is opaque. Click any referenced object to inspect it and its links.
3. **A human-approved write-back loop.** The agent proposes, a person decides, the action mutates the ontology, and the outcome is measured live.

## Run it

One static file — no build step, no API keys.

- **Locally:** open `index.html` in any modern browser.
- **Host it:** drop `index.html` into any static host (GitHub Pages, Netlify, Cloudflare Pages). For Pages — commit it to a public repo, then **Settings → Pages → Deploy from a branch → `main` / root**.

## Tech

React 18 (loaded in-browser via esm.sh + Babel Standalone), Leaflet with a CARTO dark basemap, the NASA EONET API, and lucide icons. No backend — all logic and the live data fetch run client-side, which is why it deploys as a single file.

## Notes

This is an independent concept demo. It is not affiliated with or endorsed by Palantir or NASA, and it does not use Palantir Foundry or AIP — it reimplements the *concepts* to show the thinking behind them. EONET serves curated wildfire events; for pixel-level fire-radiative-power, NASA FIRMS is a drop-in upgrade behind a small proxy (it requires an API key and CORS handling, which a static page can't do on its own).
