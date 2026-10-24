# Maldives harbours and maritime terminals

**Review copy with unresolved source and location issues.** The supplied CSV is
preserved byte-for-byte. Its `VERIFIED` and `CORRECTED` values are the supplier's
audit labels and are not endorsements from this review. None of its 45
feature-specific OSM citations passed the current feature/type/location checks.

- [Supplied CSV](maritime_terminals_audited.csv): 46 records and 21 columns.
- [Row-level review](review_report.json): structural checks, missing-value counts,
  current OSM source comparisons and named-island distances.
- [Provenance and hashes](provenance.json).
- [Cached source responses and request receipts](review_sources/).

## Review findings — 24 October 2026

All records have parseable coordinates within the study bounding box. There are
no malformed CSV rows, exact duplicate records, duplicate normalized
terminal-name/island combinations or identical coordinate pairs. Geographic
range checks do not establish that a coordinate is the claimed harbour.

| Current OSM source check | Records |
| --- | ---: |
| Feature tags, names or locations mismatch | 39 |
| Cited current feature marked not visible/deleted | 6 |
| Generic OSM homepage without a feature ID | 1 |
| Feature type and nearby geometry consistent | 0 |

Examples:

- CSV row 15, Rasdhoo Harbour, cites
  [OSM way 456182900](https://www.openstreetmap.org/way/456182900).
  Its returned tags say `highway=path` and its geometry is far from Rasdhoo.
- CSV row 17, Feridhoo Harbour, is labelled `VERIFIED` in the supplied CSV, but
  [OSM way 456182902](https://www.openstreetmap.org/way/456182902) is also a path.
- CSV row 2, Hulhumalé Ferry Terminal in Malé, cites
  [OSM way 233767098](https://www.openstreetmap.org/way/233767098), currently tagged
  as a residential road.
- CSV row 10, Malé North Harbour, cites
  [OSM way 302137560](https://www.openstreetmap.org/way/302137560), whose actual
  name is `Hulu Male' Ferry Terminal`. A transport tag alone does not substantiate
  the claimed harbour identity and coordinate.

Five points are more than 1 km outside the named island's existing OneMap
footprint:

| CSV row | Terminal | Distance outside footprint |
| --- | --- | ---: |
| 28 | Hangnaameedhoo Harbour | 14.983 km |
| 29 | Kunburudhoo Harbour | 6.006 km |
| 32 | Omadhoo Harbour | 3.523 km |
| 41 | Maafushi Recreation Jetty | 3.735 km |
| 44 | Fulidhoo Harbour | 12.292 km |

Row 41 is already marked `INVALID` in the supplied audit and remains in this
copy for traceability. Its supplied note measures a different distance; this
review reports distance to the supplied OneMap footprint, not to an island centre
or a legal territorial boundary.

Operators are `UNKNOWN` in 39 records. Opening hours are `UNKNOWN` in 43.
One record has unknown services and one has an unknown Dhivehi name. The report
counts placeholders separately from empty strings.

## Coverage and use

Input atoll labels are AA (8 records), ADh (10), K (10), V (5) and `Male City`
(13). `Male City` needs a project-code crosswalk. The Velana Airport/Hulhule
record is assigned `Male City` in this input, while OneMap assigns Hulhule to
Kaafu; agree on administrative versus Greater Malé grouping before joining.

Use this copy to resolve the flagged records. It is not a validated set of
operational landing points. Keep the original `INVALID` record excluded from
routing unless independently corrected. No replacement IDs, guessed coordinates,
operator assignments or service claims were added. The supplied retrieval date
is 2026-10-24 and no August 2025 operating history is established.

## Review method and source evidence

The review queried exact cited OSM node/way IDs and their geometry nodes through
the public API. Serial, bounded requests included delays; missing-ID batches were
split as needed. `review_sources/requests.json` records exact URLs, UTC timestamps,
HTTP outcomes and raw-body hashes. Response bodies and headers are unchanged.
`elements.json` is a derived index of those responses.

OSM distance checks use minimum great-circle distances to returned vertices.
The 1 km threshold is diagnostic, not an accuracy guarantee, and vertex distance
is not full polygon distance. Named-island distances use the existing public
OneMap snapshot in EPSG:32643. The input geometry hash, atoll matching rules and
explicit name aliases are in the report. The geography snapshot remains in the
original datathon workspace and is not included here. No FollowMe GPS or pipeline
outputs are included. These checks describe current source objects and cannot
establish historical service availability. A mismatched or deleted OSM source
does not prove that the real-world harbour itself does not exist.

## Attribution

The supplied file labels all 46 rows ODbL 1.0. That label does not establish that
the CSV's claimed values were actually derived from the cited OSM objects; the
source mismatches must be resolved.

OSM source-check evidence: © OpenStreetMap contributors,
[ODbL 1.0 and attribution](https://www.openstreetmap.org/copyright).
OneMap reference geometry: Geomatics Department, Ministry of Infrastructure,
Housing and Urban Development,
[OneMap Data Usage License](https://readme.onemap.mv/pdf/Data%20Usage%20License.pdf).
The CSV's original source and attribution fields are retained.
