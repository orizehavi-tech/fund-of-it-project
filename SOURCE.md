
# Project Data Source Proposals

## Proposal 1: Finding the Best Time to See the Northern Lights

First choice

The goal is to identify when and where conditions may be suitable for seeing the Northern Lights near Tromso and Reykjavik, and to examine how aurora forecasts change throughout the day. A favorable forecast does not necessarily mean that the Northern Lights will actually be visible.

NOAA provides updated aurora forecasts with timestamps, activity levels, and geographic coordinates. Cloud cover data could be added later to help assess viewing conditions.

NOAA live JSON data: https://services.swpc.noaa.gov/json/ovation_aurora_latest.json

NOAA text output: https://services.swpc.noaa.gov/text/ovation_latest_aurora_n.txt

Possible additional source for cloud cover: https://open-meteo.com/en/docs

NOAA updates its forecast approximately every five minutes. We plan to collect data every 15 minutes. Each sample should be around 1 to 3 KB if we save only the relevant locations and fields. The estimated total for ten weeks is 7 to 21 MB. The data is free and does not require an account, password, or API key.

Actual NOAA sample from November 6, 2026:

    Model_Run Time: 2026-11-06 13:10:00
    Observation Time: 2026-11-06 13:07:00+00:00
    Forecast Time: 2026-11-06 14:21:10.777085
    Hemispheric Power: 17.8

The current geographic forecast is replaced when a new forecast is published. Saving regular snapshots would let us examine changes in the forecast over time.

## Proposal 2: Can Tourists Rely on Rental Bikes in London?

Second choice

The goal is to find out whether tourists can reliably find available bikes and empty docking spaces near London's main attractions. We will compare availability at different times of day and on weekdays and weekends. We could also examine whether disruptions on the Underground are related to bike availability.

Transport for London provides current data on available bikes, empty docking spaces, station identifiers, and update times. A separate endpoint provides the current status of an Underground line.

TfL bike stations: https://api.tfl.gov.uk/BikePoint

TfL Piccadilly line status: https://api.tfl.gov.uk/Line/piccadilly/Status

We plan to collect data every 15 minutes. A sample covering 10 to 15 selected stations should be around 2 to 4 KB. The estimated total for ten weeks is 13 to 27 MB. Access is free, and the planned collection does not require a password or API key.

Actual TfL sample from November 6, 2026, showing only the relevant fields:

    {
      "id": "BikePoints_1",
      "commonName": "River Street , Clerkenwell",
      "NbBikes": "2",
      "NbEmptyDocks": "11",
      "modified": "2026-11-06T12:11:14.783Z"
    }

In the original response, the bike and docking space counts are stored within additionalProperties. They are shown here in a shorter format.

Bike and docking space availability changes throughout the day. The API provides the current situation rather than a complete history of previous availability.

## Preference Ranking

Our first choice is the Northern Lights project, followed by the London bike availability project. Both would involve collecting live data throughout the semester, with estimated storage requirements below the 500 MB limit.
