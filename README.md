# Map Player

An interactive world map player built with [MapLibre GL JS](https://maplibre.org/).  
Shows country-level data over time, driven entirely by a JSON config file — no build step, no framework.

---

## Live demo

```
https://www.eriksberg.no/Maps/World
https://www.eriksberg.no/Maps/World?config=https://raw.githubusercontent.com/YOUR_ORG/YOUR_REPO/main/mapconfig.json
https://www.eriksberg.no/Maps/World?config=https://raw.githubusercontent.com/eriksoeb/worldmap/main/test.json
https://www.eriksberg.no/Maps/World?config=https://raw.githubusercontent.com/eriksoeb/worldmap/main/allyears.json
```

Replace the config URL with your own (see [Hosting your config](#hosting-your-config)).

---

## Features

- Year-by-year player with dropdown and play button
- Color and opacity per country per year, driven by config
- Three configurable country types (e.g. Country program / Digital project / Partner project)
- Auto-generated legend from config
- Flag tooltips (local images with public fallback)
- Geopolitically accurate borders:
  - Ukraine shown with Crimea (custom GeoJSON)
  - Morocco shown including Western Sahara
  - Somalia shown merged with Somaliland
- Passes config URL as a query parameter — one player, many configs

---

## Quick start

No install needed. Serve the folder with any static server:

```bash
py -m http.server 8000
```

Then open:

```
http://localhost:8000
```

This loads the default `mapconfig.json` from the same folder.  
To use a different config:

```
http://localhost:8000?config=http://localhost:8000/testconfig.json
```

---

## Config format

The map is controlled by a single JSON file. Full example:

```json
{
  "title": "World Map",

  "map": {
    "borderWidth": 0.5,
    "borderColor": "#aaaaaa"
  },

  "interval": {
    "from": 1992,
    "to": 2025
  },

  "types": {
    "1": { "role": "Country program", "color": "#27ae60" },
    "2": { "role": "Digital project",  "color": "#2980b9" },
    "3": { "role": "Partner project",  "color": "#e67e22" }
  },

  "countries": {
    "UA": {
      "name": "Ukraine",
      "source": "ukraine.json",
      "years": {
        "2016": { "type": 1, "opacity": 44 },
        "2020": { "type": 1, "opacity": 98 }
      }
    },
    "GH": { "name": "Ghana",
      "years": {
        "2016": { "type": 1, "opacity": 44 },
        "2025": { "type": 1, "opacity": 40 }
      }
    }
  }
}
```

### Fields

| Field | Description |
|---|---|
| `title` | Map title shown above the player |
| `map.borderWidth` | Country border line width |
| `map.borderColor` | Country border color (hex) |
| `interval.from` / `interval.to` | Year range for the player dropdown |
| `types` | Map of type ID → role label and color |
| `countries` | Map of ISO 3166-1 alpha-2 code → country entry |

### Country entry

| Field | Required | Description |
|---|---|---|
| `name` | Yes | Display name (also used for GeoJSON lookup if ISO2 fails) |
| `source` | No | `"ukraine.json"` for custom local GeoJSON; `"custom"` for hardcoded polygon (Morocco). Omit for standard world GeoJSON lookup. |
| `years` | Yes | Map of year (string) → `{ type, opacity }` |

### Year entry

| Field | Description |
|---|---|
| `type` | Type ID matching a key in `types` — determines color |
| `opacity` | Fill opacity 0–100. Use lower values for historical/lighter presence. |

A country is only shown for years that have an explicit entry. The player interpolates nothing — each year is exactly what the config says.

---

## Hosting your config

Because the player fetches the config via `fetch()`, the config must be served with CORS headers. The easiest options:

| Host | How | CORS |
|---|---|---|
| GitHub (raw) | `https://raw.githubusercontent.com/user/repo/main/config.json` | Allowed |
| GitHub Pages | Serve via `user.github.io/repo/config.json` | Allowed |
| jsDelivr CDN | `https://cdn.jsdelivr.net/gh/user/repo/config.json` | Allowed |
| Local server | `http://localhost:8000/config.json` | Same-origin, always works |

Pass the config URL as a query parameter:

```
mapallcountries.html?config=https://raw.githubusercontent.com/user/repo/main/config.json
```

---

## Dependencies

All loaded from CDN — no npm, no bundler.

| Library | Purpose |
|---|---|
| [MapLibre GL JS](https://maplibre.org/) | Vector map rendering |
| [Turf.js](https://turfjs.org/) | Polygon union (Somalia + Somaliland) |
| [geo-countries](https://github.com/datasets/geo-countries) | World country GeoJSON |
| [flagcdn.com](https://flagcdn.com) | Flag image fallback |
| [MapTiler](https://www.maptiler.com/) | Base map tiles (requires API key) |

### MapTiler API key

The map uses MapTiler for the base tile style. The API key is embedded in `mapallcountries.html`. 
For your own deployment, replace it with your own free key from [maptiler.com](https://www.maptiler.com/).

---

## Files

| File | Description |
|---|---|
| `mapallcountries.html` | Main map player |
| `mapconfig.json` | Default config (Norwegian development cooperation) |
| `testconfig.json` | Minimal example config for testing |
| `ukraine.json` | Custom GeoJSON for Ukraine (UN borders, Crimea as Ukraine) |

---

## License

MIT — see [LICENSE.md](LICENSE.md)
