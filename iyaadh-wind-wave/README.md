# Maldives wind and wave history — August 2025

Hourly modelled weather for the Maldives Datathon 2026 study area: Alif Alif,
Alif Dhaalu, Kaafu, Vaavu and Greater Malé. This export contains only the wind
and wave reference dataset; it contains no GPS observations or FollowMe outputs.

## Files

- [Hourly Parquet](data/reference/weather/weather_hourly.parquet): 33,480 rows,
  one per query point and UTC hour, including local time, all 11 variables,
  returned model-grid coordinates, source flags and raw-response references.
- [Query points](data/reference/weather/weather_points.csv): 45 points with
  requested coordinates, inclusion reasons, OneMap polygon identifiers, study-area
  memberships, returned cells and source status.
- [Daily summary](data/reference/weather/daily_summary.csv): local-day wind,
  gust and wave summaries, observed-hour counts and review-threshold exceedances.
- [Provenance](data/reference/weather/provenance.json): exact API URLs and
  parameters, UTC retrieval timestamps, returned metadata and units, licences,
  SHA-256 hashes and row counts.
- [Validation](data/reference/weather/validation.json): per-point coverage,
  null counts, outliers and ranked days.
- [Raw responses and request receipts](data/reference/weather/raw/): original
  API response bytes, HTTP headers and request metadata, including diagnostics.
- [Collection documentation](docs/weather_data.md) and
  [versioned configuration](config/weather.json).

The source project's relative paths are preserved inside `iyaadh-wind-wave/`.
Resolve paths recorded in provenance and the Parquet raw-response columns from
this folder. Files are byte-identical to the validated source collection.

## Coverage

The UTC request window is **2025-08-01 00:00 through 2025-08-31 23:00**:
744 hours per point. The points comprise 35 regular 0.25° grid points, seven
OneMap-derived island-area endpoints and three corridor midpoints. The corridor
is Malé–Rasdhoo–Ukulhas–Mathiveri, with Thoddoo, Maafushi and Hulhumalé included.

Every requested variable has 33,480 non-null values and zero nulls. There are no
failed final requests, duplicate point-hours or diagnostic outliers. Returned
coordinates represent 41 atmospheric cells and 35 marine cells; query points
sharing cells are not independent samples.

Atmospheric variables are `wind_speed_10m`, `wind_gusts_10m`,
`wind_direction_10m`, `precipitation` and `surface_pressure`. Marine variables
are `wave_height`, `wave_direction`, `wave_period`, `wind_wave_height`,
`swell_wave_height` and `swell_wave_period`. Wind and gust units are km/h,
directions are degrees, precipitation is mm, pressure is hPa, wave heights are m
and periods are s. Literal returned units are recorded in provenance.

`time_local` is derived in code for **Indian/Maldives (UTC+5)**. The local window
therefore starts at 05:00 on 1 August and ends at 04:00 on 1 September. The daily
summary exposes the 19-hour first day and five-hour September spillover. Confirm
GPS timezone before joining; the first five hours of local August are outside
this requested UTC window.

## Sources and limitations

[Weather data by Open-Meteo.com](https://open-meteo.com/):

- [Historical Weather API](https://open-meteo.com/en/docs/historical-weather-api),
  requested model `ecmwf_ifs`, approximately 9 km.
- [Marine Weather API](https://open-meteo.com/en/docs/marine-weather-api),
  requested model `ncep_gfswave025`, approximately 25 km. Marine values are
  archived NOAA NCEP GFS Wave forecasts. Earlier ERA5-Ocean diagnostic responses
  lacked the requested component variables and do not contribute final values.

Modelled conditions are not an observed sea state. Coarse cells cannot resolve
reef, lagoon or harbour sheltering. Island endpoints and geodesic midpoints are
area proxies, not surveyed jetties or confirmed sailed routes. No interpolation,
gap filling or safety classification is performed by the collector.

Review thresholds (wind 30 km/h, gust 45 km/h and significant wave height 2 m)
are **provisional review thresholds, not safety limits**. Seventeen query points
fall inside the reviewed administrative study polygons; the other 28 remain as
surrounding sea context. Validation provides rankings for both scopes.

API data are provided under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Credit Open-Meteo, ECMWF and NOAA NCEP; diagnostic ERA5-Ocean responses additionally
credit ECMWF/Copernicus C3S. Open-Meteo's
[non-commercial free-tier terms](https://open-meteo.com/en/terms) apply to API use.
Point coordinates were derived from OneMap polygons under the
[OneMap Data Usage License](https://readme.onemap.mv/pdf/Data%20Usage%20License.pdf),
with Government of Maldives/OCHA COD-AB study geometry under CC BY-IGO. See
collection documentation and provenance for attribution and processing details.

## Rebuild context

This is a data export. The collector, tests and reviewed geography inputs remain
in the original `datathon-2026/` workspace; they are not included in this folder.
References to those source files in the collection documentation and provenance
describe that original workspace. Its exact cache-only rebuild command is:

```bash
.venv/bin/python scripts/collect_weather.py --config config/weather.json --offline
```

The source collection passed 20 offline tests. All 368,280 weather values and
returned coordinates were checked against raw responses, and a cache-only rerun
reproduced the data files, documentation, validation and provenance byte-for-byte.
