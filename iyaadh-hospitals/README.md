# Maldives hospitals and health facilities

**Review copy with unresolved source and location issues.** The supplied CSV is
preserved byte-for-byte. Its `VERIFIED` and `CORRECTED` values are the supplier's
audit labels and are not endorsements from this review.

- [Supplied CSV](health_facilities_audited.csv): 47 records and 21 columns,
  including hospitals, health centres and clinics.
- [Row-level review](review_report.json): structural checks, missing-value counts,
  duplicate checks, current OSM source comparisons and named-island distances.
- [Provenance and hashes](provenance.json).
- [Cached source responses and request receipts](review_sources/).

## Review findings — 24 October 2026

All records have parseable coordinates within the study bounding box. There are
no malformed CSV rows, exact duplicate records or duplicate normalized
facility-name/island combinations. Shared coordinates occur at CSV rows 7/10
and 17/21. These are island-proxy collisions, not evidence of duplicate clinics.

| Current OSM source check | Records |
| --- | ---: |
| Feature type and nearby geometry consistent | 3 |
| Feature tags, names or locations mismatch | 35 |
| Cited feature not returned by the current API | 1 |
| Generic source URL without a specific OSM feature | 8 |

The three limited consistent checks are IGMH, Dharumavantha Hospital and ADK
Hospital. This does not verify their listed services, ownership, opening hours,
regulatory classifications or August 2025 availability.

Examples of unresolved source problems:

- CSV row 3, Bodufolhudhoo Health Centre, cites
  [OSM node 2167184200](https://www.openstreetmap.org/node/2167184200).
  The returned node is at approximately 0.658°N, 42.718°E, far from the supplied
  Maldives coordinate.
- CSV row 39, Hulhumalé Hospital, cites
  [OSM way 233767115](https://www.openstreetmap.org/way/233767115).
  Its current tags describe a building with `addr:country=GR` and a Greek address.
- CSV row 40, Tree Top Hospital, cites
  [OSM node 2334944290](https://www.openstreetmap.org/node/2334944290).
  It is tagged as the town of Hulhumalé, not a hospital.

Thirty-nine records are already labelled `LOCATED-BY-ISLAND-ONLY`. Four points
are more than 1 km outside the named island's existing OneMap footprint:

| CSV row | Facility | Distance outside footprint |
| --- | --- | ---: |
| 15 | Hangnaameedhoo Health Centre | 14.918 km |
| 16 | Kunburudhoo Health Centre | 6.136 km |
| 19 | Omadhoo Health Centre | 3.380 km |
| 32 | Fulidhoo Health Centre | 12.414 km |

`opening_hours_if_listed` is `UNKNOWN` for all 47 records. Dhivehi names are
unknown for seven. Empty-string counts alone would hide these missing values;
the review report counts both blanks and semantic placeholders.

## Coverage and use

Atoll labels in the input are AA (9 records), ADh (11), K (9), V (5) and
`Male City` (13). Reconcile `Male City` with project code `MLE` before joining.
Island spellings also require an explicit crosswalk. No input spelling, source
ID, coordinate, service claim or audit label was silently changed.

Use this copy to resolve the flagged records. It is not a validated set of
hospital-building locations or emergency-service availability. The supplied
retrieval date is 2026-10-24. No evidence establishes the listed facilities or
services as operating throughout August 2025.

## Review method and source evidence

The review queried exact cited OpenStreetMap node/way IDs through the public API,
then fetched the referenced way nodes. Small serial requests were delayed and
bounded; batches containing missing IDs were split to identify them. Raw API
bodies, headers, URLs, UTC timestamps and hashes are in `review_sources/`.
`elements.json` there is a derived index of those responses, not another source.

Distances to OSM geometry are minimum great-circle distances to returned
vertices. The 1 km threshold is diagnostic, not an accuracy guarantee. Way
vertex distance is not full polygon distance. Named-island checks use the existing
public OneMap polygon snapshot in EPSG:32643. That source file's hash, atoll
matching rules and the explicit Himmafushi→Hinmafushi and Villimale→Vilingili
aliases are recorded in the report. The geography snapshot remains in the
original datathon workspace and is not included here. No FollowMe GPS or pipeline
outputs are included.

The generic [Ministry of Health HEMS registry](https://hems.health.gov.mv/establishments)
and [Maaharaa portal](https://maaharaa.health.gov.mv/) are not record-level evidence
for the individual clinic claims in this file. Current OSM checks cannot prove
historical existence or service availability. Missing/deleted/mismatched source
objects do not prove that the named real-world facility itself does not exist.

## Attribution and unresolved reuse terms

The input assigns ODbL 1.0 to 39 records and `terms not stated` to eight.
Do not treat this mixed-source file as uniformly licensed. This review does not
resolve the eight missing reuse terms or independently establish a right to
redistribute those source records.

OSM source-check evidence: © OpenStreetMap contributors,
[ODbL 1.0 and attribution](https://www.openstreetmap.org/copyright).
OneMap reference geometry: Geomatics Department, Ministry of Infrastructure,
Housing and Urban Development,
[OneMap Data Usage License](https://readme.onemap.mv/pdf/Data%20Usage%20License.pdf).
The CSV's original attribution and source fields are retained.
