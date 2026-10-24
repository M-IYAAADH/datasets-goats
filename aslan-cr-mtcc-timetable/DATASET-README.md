# CTN Ferry Timetables — Central Region (routes 301–310)

Source: **CTN-Ferry-Schedule-Effective-15.05.2022-K.And-Aa.-Adh.V.pdf**
Effective date: 15 May 2022. Contact: 7998821.

## What this file is

`ctn_timetable_source.csv` is a structured transcription of all 10 routes in the above PDF. It uses the same column schema as `MTCC_GTFS/timetable_source.csv` so it can be fed directly into `build_gtfs.py` or the repo router.

## Column schema

| Column | Description |
|---|---|
| `route` | Route number (301–310) |
| `trip_id` | Unique trip identifier, e.g. `308_OUT` / `308_BACK` or `303_SUNWED_OUT` for multi-pattern routes |
| `block_id` | Vessel block — trips sharing a block_id run on the same boat; use this for disruption what-if |
| `days` | Space-separated day names on which this trip runs, e.g. `Sat Sun Mon Tue Wed Thu` |
| `seq` | Stop sequence number within the trip (1-based) |
| `stop` | Stop name (English, as printed in the PDF) |
| `arrive` | Scheduled arrival time `HH:MM`, blank for the first stop of a trip |
| `depart` | Scheduled departure time `HH:MM`, blank for the last stop of a trip |

## Routes

All routes run **Saturday through Thursday**. No service on Fridays on any route.

### K-Atoll (Malé central) — main focus

| Route | Endpoints | Pattern | Stops |
|---|---|---|---|
| 308 | K.Dhiffushi ↔ Male' | Daily (Sat–Thu) | Dhiffushi, Thulusdhoo, Huraa, Himmafushi, Male' |
| 309 | K.Guraidhoo ↔ Male' | Daily (Sat–Thu) | Guraidhoo, Maafushi, Gulhi, Male' |
| 307 | K.Kaashidhoo → Male' | Alternating — outbound Sat/Mon/Wed, return Sun/Tue/Thu | Kaashidhoo, Gaafaru, Male' |
| 306 | V.Rakeedhoo → Male' | Alternating — outbound Sat/Mon/Wed, return Sun/Tue/Thu | Rakeedhoo, Keyodhoo, Felidhoo, Thinadhoo, Fulidhoo, K.Maafushi, Male' |

Routes 307 and 306 run outbound and return on different day groups, so no same-day round trip is possible on either.

### Alif Alif / Alif Dhaal — regional connections

| Route | Endpoints | Pattern | Notes |
|---|---|---|---|
| 301 | AA.Himandhoo ↔ AA.Rasdhoo | Daily (Sat–Thu) | Also in `MTCC_GTFS/timetable_source.csv` — cross-check if merging |
| 303 | AA.Thoddoo ↔ Male' | Three day patterns (see below) | Also in `MTCC_GTFS/timetable_source.csv` |
| 305 | ADH.Mahibadhoo ↔ Male' | Alternating — Male'→Mahibadhoo Sat/Mon/Wed, return Sun/Tue/Thu | ~4 h crossing |
| 304 | ADH.Fenfushi ↔ ADH.Mahibadhoo | Daily (Sat–Thu) | Internal Alif Dhaal |
| 302 | ADH.Mandhoo ↔ ADH.Mahibadhoo | Daily (Sat–Thu) | Mahibadhoo is both an intermediate stop (seq 3) and the terminus (seq 6) |
| 310 | AA.Feridhoo → ADH.Mahibadhoo | Daily (Sat–Thu) | Cross-atoll connector via Maalhos, Himandhoo |

### Route 303 day patterns

| trip_id | Days | Journey |
|---|---|---|
| 303_SATTUE_OUT / _BACK | Sat, Tue | Thoddoo ↔ Rasdhoo only (does not reach Male') |
| 303_SUNWED_OUT | Sun, Wed | Thoddoo → Rasdhoo → Ukulhas → Rasdhoo → Male' |
| 303_MONTHU_OUT | Mon, Thu | Male' → Rasdhoo → Ukulhas → Rasdhoo → Thoddoo |

The boat visits Ukulhas as a detour (Rasdhoo → Ukulhas → Rasdhoo) in both the Sun/Wed and Mon/Thu patterns. Rasdhoo therefore appears twice in those trip sequences — this is correct and faithful to the PDF.

## Known issues and anomalies

**Route 309 PDF typo.** The final row of the return trip prints "Male'" in the English departure-stop column, but the Dhivehi script alongside it reads Maafushi. Given the sequence (the boat has just departed Gulhi and is heading to Guraidhoo), the correct stop is Maafushi. The CSV uses Maafushi.

**Routes 301 and 303 overlap with MTCC_GTFS.** The `MTCC_GTFS/timetable_source.csv` already contains 301 and 303, transcribed from the same 2022 PDF, with a 2026 extension for route 303 (Hulhumalé leg added). Do not merge the two files blindly — the 2026 extended version in MTCC_GTFS supersedes the 303 data here.

**Route 302 intermediate terminus.** Mahibadhoo is the atoll capital and acts as a hub mid-route: the outbound trip stops there (seq 3), continues to Omadhoo and back, and terminates at Mahibadhoo again (seq 6). The router must not treat the two Mahibadhoo entries as the same stop for transfer purposes within the same trip.

## Relation to the repo

Routes 308 and 309 are the highest-priority additions for the Island Connectivity Index (DT-18): they are the daily K-atoll lifelines to Male' and have the most census-relevant population (Guraidhoo, Maafushi, Gulhi, Dhiffushi, Thulusdhoo, Huraa, Himmafushi — all inhabited K-atoll islands).

Routes 306 and 307 have alternating-day outbound/return patterns that make same-day return trips to Male' impossible, which is a significant connectivity finding worth highlighting in the pitch.

---

# FollowMe GPS — Central Region subset (August 2025)

**Files:**
- `FollowMe_central_region_august_2025.csv` — project root
- `filtered data sets/FollowMe_central_region_august_2025.parquet` — subfolder

**Script:** `filter_central_region.py` (project root)

## What this file is

A geographic subset of the raw FollowMe GPS data (`FollowMe/FollowMe_august_2025/1.csv … 31.csv`), keeping only pings that fall within the four atolls covered by the CTN ferry schedule above. All 31 days of August 2025 are concatenated into a single file.

## Column schema and data types

The columns are identical to the raw daily CSVs. The Parquet file stores them with the types listed below; the CSV treats timestamps as plain strings.

| Column | CSV type | Parquet type | Description |
|---|---|---|---|
| `poll_id` | int64 | int64 | Unique database row ID |
| `imei` | int64 | int64 | Raw device IMEI — **treat as personal data, never commit** |
| `timestamp` | string | datetime64[us] | Server-side timestamp — prefer `device_date`; ~1 000 rows/day carry a 2026 server timestamp due to a known clock-sync issue |
| `device_date` | string | datetime64[us] | Device-reported time — use this for all temporal analysis |
| `latitude` | float64 | float64 | Decimal degrees N |
| `longitude` | float64 | float64 | Decimal degrees E |
| `speed` | float64 | float64 | Speed in **knots** |
| `heading` | float64 | float64 | Bearing 0–360 ° (NaN when stationary or invalid) |
| `network` | int64 | int64 | Mobile network: 1 = Dhiraagu, 2 = Ooredoo, 0 = unknown |
| `cellid` | string | string | Mobile cell tower ID |
| `gsm_level` | int64 | int64 | GSM signal level (0 = no signal) |

`timestamp` and `device_date` are parsed to `datetime64[us]` in the Parquet file so time-based operations work without an explicit `parse_dates` step.

## Geographic filter

A ping is kept if it falls inside any of the four atoll bounding boxes:

| Atoll | Islands in schedule | Lat range | Lon range |
|---|---|---|---|
| K – Kaafu (Malé atoll) | Malé, Kaashidhoo, Gaafaru, Dhiffushi, Thulusdhoo, Huraa, Himmafushi, K.Maafushi, Gulhi, Guraidhoo | 3.70–5.10 °N | 72.80–73.80 °E |
| AA – Alif Alif (N. Ari) | Himandhoo, Maalhos, Feridhoo, Mathiveri, Bodufolhudhoo, Ukulhas, Rasdhoo, Thoddoo | 3.85–4.55 °N | 72.65–73.05 °E |
| Adh – Alif Dhal (S. Ari) | Mandhoo, Ku'nburudhoo, Mahibadhoo, Hangnaameedhoo, Omadhoo, Fenfushi, Maamigili, Dhidhdhoo, Dhigurah, Dha'ngethi | 3.45–3.95 °N | 72.65–73.05 °E |
| V – Vaavu | Rakeedhoo, Keyodhoo, Felidhoo, Thinadhoo, Fulidhoo | 3.30–3.70 °N | 73.30–73.70 °E |

## Counts and size

| | Rows |
|---|---|
| Raw (all 31 days) | 17,462,749 |
| Central region kept | 9,973,837 (57.1 %) |

## Why Parquet instead of CSV

The filtered CSV is ~1.1 GB and too large to open in Excel or load quickly in a script. Parquet was chosen for three reasons:

1. **Size.** Parquet uses columnar compression (Snappy codec). The same 9.97 M rows compress to ~270 MB — 75 % smaller than the CSV with no data loss.
2. **Speed.** Columnar storage lets pandas skip columns it does not need on load. Reading only `device_date`, `latitude`, `longitude`, and `speed` is significantly faster than parsing a full CSV row-by-row.
3. **Types are preserved.** The CSV stores all values as text; every load requires re-parsing timestamps and floats. The Parquet file stores `timestamp` and `device_date` as native `datetime64[us]` and numeric columns as their proper types, so scripts work with correct types from the first line.

To load in a script:

```python
import pandas as pd

df = pd.read_parquet("filtered data sets/FollowMe_central_region_august_2025.parquet")

# Load only the columns you need (faster):
df = pd.read_parquet("filtered data sets/FollowMe_central_region_august_2025.parquet",
                     columns=["device_date", "latitude", "longitude", "speed", "imei"])
```

Uncompressed CSV is kept alongside the Parquet file as a fallback for tools that cannot read Parquet (e.g. QGIS, Excel with Power Query).

## Data warnings

- **Raw IMEIs present.** This file inherits the raw `imei` column from the source CSVs. Never commit it, never share it outside the team. If you need a shareable version, hash IMEIs first (see `FollowMe_pipeline/boat_filter.py` for the salted SHA-256 method).
- **No deduplication or coordinate cleaning applied.** The pipeline in `FollowMe_pipeline/boat_filter.py` removes ~213 k duplicate pings and 384 bad-coordinate rows. If you need clean pings, run that pipeline first and use the `out/pings/day_XX.csv.gz` outputs; this file is the raw geographic slice.

the for the filtered followme data is still too large to be uploaded to GitHub so i am attaching a link to a google drive folder where the datasets are stored

https://drive.google.com/drive/folders/1IP02eELXfw6RqihYZjxYutt7uGTypp4R?usp=drive_link
