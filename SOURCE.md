# Data Source: Kp Forecast Accuracy

**Research question:** How accurate are NOAA and GFZ Kp forecasts, and how does forecast accuracy change with the time remaining until the predicted interval?

We compare forecasts with reported values of the planetary Kp index (0–9, without units), which describes geomagnetic activity in three-hour UTC intervals. Kp does not directly show whether the Northern Lights were visible at a specific location.

## i. Data Sources

| Source | URL | Format | Account or key | License or terms | Planned collection frequency |
|---|---|---|---|---|---|
| NOAA SWPC Kp forecast | https://services.swpc.noaa.gov/products/noaa-planetary-k-index-forecast.json | JSON | No | NOAA data are generally public domain unless stated otherwise | Every 3 hours |
| GFZ Kp forecast (PAGER/SWIFT) | https://spaceweather.gfz.de/fileadmin/Kp-Forecast/CSV/kp_product_file_FORECAST_PAGER_SWIFT_LAST.json | JSON | No | Published with an "as-is" disclaimer; a specific reuse license was not confirmed | Every 3 hours |
| GFZ recorded Kp | https://kp.gfz.de/app/json/ | JSON | No | CC BY 4.0; credit GFZ | Every 3 hours |

The GFZ recorded Kp URL needs `start`, `end`, and `index=Kp` parameters. GFZ updates its forecast hourly, but the Kp values refer to three-hour intervals. Our first four samples were taken on four different days (November 17–20, 2026); collection every three hours is our plan for the semester.

## ii. What We Collect

Each sample contains the three source responses, their URLs and retrieval times, and a `collected_at_utc` timestamp. The four samples have the same structure.

- **NOAA forecast:** `kp` and `time_tag` from rows where `observed` is `predicted`.
- **GFZ forecast:** `median` Kp and its matching `Time (UTC)`.
- **GFZ recorded Kp:** matching `Kp`, `datetime`, and `status` array entries. All recorded Kp values in our first four samples have status `pre` (preliminary).

We save the complete responses in the raw JSON files. Only selected fields will be converted to `dataset.csv`. The collection timestamps are in UTC; the existing raw files also contain a local timezone label, which is not used in the analysis.

## iii. Dataset Schema (`dataset.csv`)

Each row will contain one forecast value or one reported Kp value. The CSV will have a header row, and all timestamps will use UTC (ISO 8601).

| Column | Meaning | Type / units |
|---|---|---|
| `collected_at_utc` | When the snapshot was collected | UTC datetime |
| `source` | `NOAA_FORECAST`, `GFZ_FORECAST`, or `GFZ_KP` | Text |
| `record_type` | `forecast` or `observation` | Text |
| `interval_start_utc` | Start of the three-hour interval | UTC datetime |
| `kp_value` | Forecast or reported Kp value | Number, 0–9 (no units) |
| `hours_ahead_at_collection` | Hours from collection to the forecast interval | Decimal hours; blank for observations |
| `value_status` | `predicted` for forecasts; GFZ `status` for observations | Text |

For forecasts, we keep only intervals that start at or after `collected_at_utc`. In each of our four samples, the first GFZ forecast interval is already in the past and will therefore be excluded from the CSV. `hours_ahead_at_collection` is measured from when we collected the data, not when the provider originally issued the forecast.

We keep separate forecast snapshots even when they predict the same interval. Reported GFZ values can change between collections, so the analysis will use the latest available value for each completed interval, preferring definitive values when available and identifying preliminary values as such. Forecasts and reported Kp values will be matched by `interval_start_utc`.

## iv. Why We Selected These Columns

The interval and Kp value let us compare predictions with reported activity. The source and collection time let us compare the two providers and different forecast horizons. The record type and status distinguish forecasts from preliminary or definitive Kp values.

Other fields, such as NOAA's `noaa_scale` and GFZ's ensemble members, quantiles and probabilities, are kept in the raw files but are not needed for the initial research question.

## v. Field Mapping

| Original field | Dataset column | Conversion |
|---|---|---|
| Snapshot `collected_at_utc` | `collected_at_utc` | Keep the UTC collection time |
| Key under `sources` | `source` | Use the source name |
| Source name | `record_type` | `forecast` for NOAA/GFZ forecasts; `observation` for GFZ recorded Kp |
| NOAA `time_tag` | `interval_start_utc` | Parse as UTC |
| NOAA `kp` | `kp_value` | Keep rows with `observed = predicted` and a nonnegative forecast horizon |
| NOAA `observed` | `value_status` | Set to `predicted` for selected rows |
| GFZ forecast `Time (UTC)` | `interval_start_utc` | Parse `DD-MM-YYYY HH:MM` as UTC |
| GFZ forecast `median` | `kp_value` | Match by the same row key as `Time (UTC)` |
| GFZ forecast source name | `value_status` | Set to `predicted` |
| GFZ recorded `datetime` | `interval_start_utc` | Read the timestamp from the corresponding array position |
| GFZ recorded `Kp` | `kp_value` | Read the value from the corresponding array position |
| GFZ recorded `status` | `value_status` | Keep the original status |
| Forecast interval minus collection time | `hours_ahead_at_collection` | Calculate in decimal hours for forecasts only |

## vi. Expected Size After One Semester

We plan one collection every three hours for about ten weeks: **8 collections per day × 70 days = 560 JSON files**. Files will be saved as `raw/YYYY-MM-DD/HHMM.json`, with UTC used for both the folder date and filename.

Our four initial JSON samples are each about **27–28 KB**. At this rate, the `raw/` folder would be about **15.5 MB** after ten weeks. Based on the selected fields and records in the four samples, `dataset.csv` is estimated at about **3 MB**. These are estimates and may change as the sources change. Both are below the course limit of 500 MB.
