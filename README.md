# World Geolocations — GeoJSON

**GeoJSON boundary polygons for every country and territory in the world, organized by continent.**

I built this dataset because I couldn't find a single public repository that had complete, consistent GeoJSON coverage of *all* the world's countries **and** territories — most datasets skip dependencies, overseas territories, and disputed or minor territories, or bury them inside one massive file. So I assembled it myself.

This dataset is used in production: it powers the on-device country/territory detection engine in [Detour](https://joindetour.com), an offline-first geospatial app that performs GeoJSON point-in-polygon matching against these boundaries.

## Repository structure

```
africa/
antarctica/
asia/
europe/
north-america/
oceania/
south-america/
```

Each continent folder contains one `.geojson` file per country or territory. Every file is a standard [GeoJSON](https://geojson.org/) `FeatureCollection` (RFC 7946), so it works out of the box with Leaflet, Mapbox, MapKit, Turf.js, GeoPandas, PostGIS, QGIS, and anything else that speaks GeoJSON.

## Quick start

**Preview any file instantly:** drag it into [geojson.io](https://geojson.io).

**JavaScript (Turf.js) — point-in-polygon check:**

```js
import { booleanPointInPolygon, point } from "@turf/turf";
import france from "./europe/france.geojson" assert { type: "json" };

const eiffelTower = point([2.2945, 48.8584]);
const feature = france.features[0];

console.log(booleanPointInPolygon(eiffelTower, feature)); // true
```

**Python (GeoPandas):**

```python
import geopandas as gpd

gdf = gpd.read_file("south-america/brazil.geojson")
print(gdf.total_bounds)  # bounding box of Brazil
```

**Swift (MapKit / CoreLocation):** decode the file with `MKGeoJSONDecoder`, then test coordinates against the resulting `MKPolygon` renderers — this is essentially how Detour's detection engine works.

## 🙏 Contributions wanted: states, provinces & subdivisions

The biggest thing this dataset is missing — and the main thing I'd love help with — is **first-level administrative subdivisions**: states, provinces, regions, departments, prefectures, etc. for each country.

If you want to contribute, here's the proposed convention:

```
north-america/
  united-states.geojson          ← country boundary (exists)
  united-states/                 ← subdivisions (wanted!)
    florida.geojson
    texas.geojson
    ...
```

Guidelines for contributions:

- One `.geojson` file per subdivision, valid RFC 7946, `FeatureCollection` at the root.
- Lowercase, hyphenated filenames, no spaces (`new-south-wales.geojson`).
- Include at minimum a `name` property; ISO 3166-2 codes (e.g. `US-FL`) in an `iso_3166_2` property are strongly encouraged.
- Note your boundary data source in the pull request so provenance stays traceable.

Corrections to existing country/territory boundaries, added metadata properties (ISO codes, names in local languages), and file-size optimizations are welcome too. Open an issue first if you want to discuss an approach before doing the work.

## Notes & caveats

- **Disputed territories:** boundaries for disputed areas reflect one common representation and are provided for practical/technical use, not as a political statement. If a boundary is wrong or contentious, open an issue.
- **Precision:** polygons are simplified to a level suitable for country/territory-scale detection and rendering, not for legal or survey use.
- **Naming:** territory naming follows common English usage; local-language names are a welcome contribution (see above).

## License

Released under the [MIT License](LICENSE). If your contribution derives from another dataset, make sure its license is compatible and credit it in your PR.

---

*Maintained by [Luis E. Azmouz](https://luisazmouz.com) · [github.com/luisazmouz](https://github.com/luisazmouz)*
