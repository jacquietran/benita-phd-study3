# Methods {#methods}

## Key definitions

- **In-flight sleep:** The sleep obtained during the international travel from Melbourne to Chennai on 8 and 9 March.
- **Distinct sleep bouts:** From the actigraphy data, each bout of sleep is treated as a distinct observation. Example: A person has a nap and a night-time sleep occurring on the same date. When the data is represented at the *per sleep bout* level, the nap and night-time sleep would be captured as two distinct sleep bouts (i.e., one row of sleep data for each bout).
- **Sleep per day:** From the actigraphy data, each day of sleep is treated as a distinct observation. Example: A person who has a nap and a night-time sleep occurring on the same date. When the data is represented at the *per day* level, the nap and night-time sleep data is merged to produce a summary of the day's sleep (i.e., one row of sleep data for each day).

## Measures and variables

- Sleep via actigraphy
- Perceived jet-lag
- Self-reported well-being
- Internal load (Session-RPE)

## Derived variables and formats

### Habitual sleep

Habitual sleep values are obtained from per-person per-day sleep monitoring data, with variables definitions as follows:

- **Bed time:** Mean bed time over *n* number of days monitored, expressed in HH:MM:SS format.
- **Wake-up time:** Mean wake-up time over *n* number of days monitored, expressed in HH:MM:SS format.
- **Sleep duration (min):** Mean sleep duration over *n* number of days monitored, expressed in minutes.
- **Sleep duration (h):** Mean sleep duration over *n* number of days monitored, expressed in decimal hours.
- **Sleep latency:** Mean sleep latency over *n* number of days monitored, expressed in minutes.
- **Sleep efficiency:** Mean sleep efficiency over *n* number of days monitored, expressed as a percentage.
- **Wake duration:** Mean wake duration over *n* number of days monitored, expressed in minutes.
- **Wake bouts num:** Mean number of wake bouts over *n* number of days monitored.
- **Wake bouts average duration:** Mean duration of wake bouts over *n* number of days monitored, expressed in decimal minutes.

### Sleep per day

The per day data has been calculated from the bout-level data as follows:

- **Sleep duration:** Total sleep duration from all sleep bouts that started on the same date, for each person.
- **Sleep latency:** Total sleep latency from all sleep bouts that started on the same date, for each person.
- **Sleep efficiency:** Mean sleep efficiency from all sleep bouts that started on the same date, for each person. *[JT note] THIS IS CALCULATED FROM THE RAW SLEEP EFFICIENCY VALUES. May be better to calculate from total sleep time / total time-in-bed.*
- **Wake duration:** Total wake duration from all sleep bouts that started on the same date, for each person.
- **Number of wake bouts:** Total number of wake bouts from all sleep bouts that started on the same date, for each person.
- **Average duration of wake bouts:** Mean of the average wake bout duration from all sleep bouts that started on the same date, for each person. *[JT note] THIS IS AN AVERAGE OF AN AVERAGE. Would be better to calculate this from total wake duration divided by number of wake bouts.*

## Analysis
