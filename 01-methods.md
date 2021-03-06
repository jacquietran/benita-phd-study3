# Methods {#methods}

## Participants {#methods-participants}

In total, there were 16 players who participated in this study. However, 2 players recorded habitual sleep only (i.e., no tour sleep data available). In addition, 3 players recorded tour sleep only (i.e., no habitual sleep data available). Thus, 5 participants were excluded from the analysis, where `ath_id = c("09", "13", "14", "15", "16")`.

All results reported here include the 11 players who recorded both habitual and on-tour sleep.

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
- **Sleep efficiency:** Efficiency across all sleep bouts that started on the same date, for each person. [Click here for more details at back-calculating total time in bed,](#backcalc) in order to calculate appropriately weighted values for sleep efficiency at the day-level.
- **Wake duration:** Total wake duration from all sleep bouts that started on the same date, for each person.
- **Number of wake bouts:** Total number of wake bouts from all sleep bouts that started on the same date, for each person.
- **Average duration of wake bouts:** Mean of the average wake bout duration from all sleep bouts that started on the same date, for each person. *[JT note] THIS IS AN AVERAGE OF AN AVERAGE. Would be better to calculate this from total wake duration divided by number of wake bouts.*

#### Back-calculating total time in bed and sleep efficiency {#backcalc}

The source data is recorded at the per sleep bout level (i.e., one row = one bout).

For *most* of the sleep variables, it is straightforward to summarise to a per day level: e.g., total sleep duration in a day = the sum of sleep duration across all bouts in a day.

For *sleep efficiency*, this is not as clear cut. At the bout level, sleep efficiency is some percentage of total time in bed (not exceeding 100%). To summarise this at the day-level, we cannot take a total as we would for other sleep variables. For example: a person records two sleep bouts in a day, each with 80% sleep efficiency. If we were to take a total to represent the day-level sleep efficiency, we end up with 80 + 80 = 160%...which doesn't make sense in the context of this variable!

Another approach could be to calculate average (mean) sleep efficiency across a person's sleep bouts in a day, but this is not accurate either.

##### Example 1 {#backcalc_example1}

A person records two sleep bouts in a day - one brief nap (say, 30 min) and one longer night-time sleep (say, 9 hours). From the nap, the person records 60% sleep efficiency. From the night-time sleep, the person records 90% sleep efficiency.

If we average these sleep efficiency figures, then:

$efficiency_{daymean} = \frac {efficiency_{nap} + efficiency_{nightsleep}} {n_{bouts}}$

$efficiency_{daymean} = \frac {(60\% + 90\%)} {2}$

$efficiency_{daymean} = {75\%}$

However, the problem with this approach is that the efficiency of each bout is given equal weighting in calculating the day's average. This is despite the fact that the nap is much shorter than the night-time sleep. In this scenario, the mean of sleep efficiency across bouts is likely to **underestimate** the actual efficiency of sleep obtained across the day.

##### Example 2

A person records two sleep bouts in a day - one brief nap (say, 30 min) and one longer night-time sleep (say, 9 hours). From the nap, the person records 90% sleep efficiency. From the night-time sleep, the person records 60% sleep efficiency. If we apply the same averaging approach to this scenario as described for [Example 1](#backcalc_example1), the day-level sleep efficiency metric would work out to the same value = 75%. However, in this scenario, this calculation likely **overestimates** the actual efficiency of sleep obtained across the day, since the person obtained low sleep efficiency from the longest sleep bout in that day.

##### Returning to first principles

A common way to calculate sleep efficiency is:

${sleep\ efficiency} = \frac {sleep\ duration} {total\ time\ in\ bed}$

In the source data, we have sleep efficiency and sleep duration, but **not** total time in bed. Following first principles, we expect that we can "back-calculate" total time in bed by re-organising the algebraic equation, i.e.:

${total\ time\ in\ bed} = \frac {sleep\ duration} {sleep\ efficiency}$

The code below steps through this "back-calculation" procedure, using the habitual sleep data.




```r
# Load libraries ---------------------------------------------------------------

library(here)
library(dplyr)
library(knitr)
library(kableExtra)
```


```r
# Import the tidy data ---------------------------------------------------------

sleep_habitual <- readRDS(
  here("outputs/sleep_habitual.rds"))
```


```r
# Wrangle the data -------------------------------------------------------------

sleep_habitual %>%
  # Focus on key variables for this explanation
  select(
    ath_id, start_date, sleep_duration, sleep_latency, wake_duration,
    sleep_efficiency) %>%
  # Back-calculate total time in bed
  mutate(
    total_time_in_bed = round(
      (sleep_duration / sleep_efficiency * 100), 0)) %>%
  # Reorder columns
  select(
    ath_id, start_date, sleep_duration, sleep_efficiency, total_time_in_bed,
    sleep_latency, wake_duration
    ) -> back_calc
```

Table \@ref(tab:tabletotaltimeinbed) below shows the results of back-calculating an estimate of total time in bed.


```r
# Display table ----------------------------------------------------------------

back_calc %>%
  kable(
    caption = 'Total time in bed, estimated from habitual sleep variables.',
    booktabs = TRUE) %>%
  kable_styling() %>%
  scroll_box(width = "800px", height = "550px")
```

```
## Warning: namespace 'highr' is not available and has been replaced
## by .GlobalEnv when processing object '<unknown>'
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:550px; overflow-x: scroll; width:800px; "><table class="table" style="margin-left: auto; margin-right: auto;">
<caption>(\#tab:tabletotaltimeinbed)Total time in bed, estimated from habitual sleep variables.</caption>
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> ath_id </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> start_date </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_duration </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_efficiency </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> total_time_in_bed </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_latency </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> wake_duration </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-19 </td>
   <td style="text-align:right;"> 560 </td>
   <td style="text-align:right;"> 68.64 </td>
   <td style="text-align:right;"> 816 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 142 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-20 </td>
   <td style="text-align:right;"> 451 </td>
   <td style="text-align:right;"> 79.08 </td>
   <td style="text-align:right;"> 570 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 88 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-21 </td>
   <td style="text-align:right;"> 466 </td>
   <td style="text-align:right;"> 64.00 </td>
   <td style="text-align:right;"> 728 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 146 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-22 </td>
   <td style="text-align:right;"> 573 </td>
   <td style="text-align:right;"> 65.76 </td>
   <td style="text-align:right;"> 871 </td>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:right;"> 164 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-23 </td>
   <td style="text-align:right;"> 570 </td>
   <td style="text-align:right;"> 70.22 </td>
   <td style="text-align:right;"> 812 </td>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:right;"> 155 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-24 </td>
   <td style="text-align:right;"> 542 </td>
   <td style="text-align:right;"> 80.47 </td>
   <td style="text-align:right;"> 674 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 97 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:left;"> 2016-10-25 </td>
   <td style="text-align:right;"> 388 </td>
   <td style="text-align:right;"> 81.63 </td>
   <td style="text-align:right;"> 475 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 68 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 2016-10-18 </td>
   <td style="text-align:right;"> 471 </td>
   <td style="text-align:right;"> 76.35 </td>
   <td style="text-align:right;"> 617 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 103 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 2016-10-20 </td>
   <td style="text-align:right;"> 468 </td>
   <td style="text-align:right;"> 83.80 </td>
   <td style="text-align:right;"> 558 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 75 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 2016-10-20 </td>
   <td style="text-align:right;"> 484 </td>
   <td style="text-align:right;"> 89.90 </td>
   <td style="text-align:right;"> 538 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 48 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 2016-10-21 </td>
   <td style="text-align:right;"> 455 </td>
   <td style="text-align:right;"> 81.64 </td>
   <td style="text-align:right;"> 557 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 77 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 2016-10-22 </td>
   <td style="text-align:right;"> 477 </td>
   <td style="text-align:right;"> 82.64 </td>
   <td style="text-align:right;"> 577 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 82 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 07 </td>
   <td style="text-align:left;"> 2016-10-23 </td>
   <td style="text-align:right;"> 465 </td>
   <td style="text-align:right;"> 82.62 </td>
   <td style="text-align:right;"> 563 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 80 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 2016-10-24 </td>
   <td style="text-align:right;"> 422 </td>
   <td style="text-align:right;"> 78.01 </td>
   <td style="text-align:right;"> 541 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 92 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 2016-10-25 </td>
   <td style="text-align:right;"> 365 </td>
   <td style="text-align:right;"> 77.78 </td>
   <td style="text-align:right;"> 469 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 71 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 2016-10-26 </td>
   <td style="text-align:right;"> 397 </td>
   <td style="text-align:right;"> 77.13 </td>
   <td style="text-align:right;"> 515 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 80 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 2016-10-27 </td>
   <td style="text-align:right;"> 325 </td>
   <td style="text-align:right;"> 79.76 </td>
   <td style="text-align:right;"> 407 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 57 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:left;"> 2016-10-28 </td>
   <td style="text-align:right;"> 389 </td>
   <td style="text-align:right;"> 76.06 </td>
   <td style="text-align:right;"> 511 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 84 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-10-18 </td>
   <td style="text-align:right;"> 450 </td>
   <td style="text-align:right;"> 84.31 </td>
   <td style="text-align:right;"> 534 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 63 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-10-19 </td>
   <td style="text-align:right;"> 512 </td>
   <td style="text-align:right;"> 79.05 </td>
   <td style="text-align:right;"> 648 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 97 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-10-20 </td>
   <td style="text-align:right;"> 576 </td>
   <td style="text-align:right;"> 88.62 </td>
   <td style="text-align:right;"> 650 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 62 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-10-21 </td>
   <td style="text-align:right;"> 530 </td>
   <td style="text-align:right;"> 88.32 </td>
   <td style="text-align:right;"> 600 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 61 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-10-22 </td>
   <td style="text-align:right;"> 583 </td>
   <td style="text-align:right;"> 80.68 </td>
   <td style="text-align:right;"> 723 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 107 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-10-30 </td>
   <td style="text-align:right;"> 255 </td>
   <td style="text-align:right;"> 96.09 </td>
   <td style="text-align:right;"> 265 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 9 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-10-24 </td>
   <td style="text-align:right;"> 447 </td>
   <td style="text-align:right;"> 94.70 </td>
   <td style="text-align:right;"> 472 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 18 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-10-25 </td>
   <td style="text-align:right;"> 487 </td>
   <td style="text-align:right;"> 93.65 </td>
   <td style="text-align:right;"> 520 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 30 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-10-27 </td>
   <td style="text-align:right;"> 468 </td>
   <td style="text-align:right;"> 95.95 </td>
   <td style="text-align:right;"> 488 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 18 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-10-28 </td>
   <td style="text-align:right;"> 412 </td>
   <td style="text-align:right;"> 90.56 </td>
   <td style="text-align:right;"> 455 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 38 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-10-28 </td>
   <td style="text-align:right;"> 381 </td>
   <td style="text-align:right;"> 97.12 </td>
   <td style="text-align:right;"> 392 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 10 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 2016-11-28 </td>
   <td style="text-align:right;"> 478 </td>
   <td style="text-align:right;"> 90.98 </td>
   <td style="text-align:right;"> 525 </td>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:right;"> 34 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 2016-11-28 </td>
   <td style="text-align:right;"> 561 </td>
   <td style="text-align:right;"> 87.65 </td>
   <td style="text-align:right;"> 640 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 64 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 2016-11-29 </td>
   <td style="text-align:right;"> 641 </td>
   <td style="text-align:right;"> 89.41 </td>
   <td style="text-align:right;"> 717 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 67 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 2016-11-30 </td>
   <td style="text-align:right;"> 473 </td>
   <td style="text-align:right;"> 93.70 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:left;"> 2016-12-01 </td>
   <td style="text-align:right;"> 558 </td>
   <td style="text-align:right;"> 84.14 </td>
   <td style="text-align:right;"> 663 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 54 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 2016-10-22 </td>
   <td style="text-align:right;"> 478 </td>
   <td style="text-align:right;"> 85.57 </td>
   <td style="text-align:right;"> 559 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 57 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 2016-10-23 </td>
   <td style="text-align:right;"> 482 </td>
   <td style="text-align:right;"> 76.61 </td>
   <td style="text-align:right;"> 629 </td>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:right;"> 102 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 2016-10-24 </td>
   <td style="text-align:right;"> 426 </td>
   <td style="text-align:right;"> 79.16 </td>
   <td style="text-align:right;"> 538 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 88 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 2016-10-25 </td>
   <td style="text-align:right;"> 461 </td>
   <td style="text-align:right;"> 80.30 </td>
   <td style="text-align:right;"> 574 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 90 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:left;"> 2016-10-26 </td>
   <td style="text-align:right;"> 488 </td>
   <td style="text-align:right;"> 81.47 </td>
   <td style="text-align:right;"> 599 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 88 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 2016-11-27 </td>
   <td style="text-align:right;"> 496 </td>
   <td style="text-align:right;"> 77.97 </td>
   <td style="text-align:right;"> 636 </td>
   <td style="text-align:right;"> 87 </td>
   <td style="text-align:right;"> 36 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 2016-11-28 </td>
   <td style="text-align:right;"> 547 </td>
   <td style="text-align:right;"> 86.74 </td>
   <td style="text-align:right;"> 631 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 63 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 2016-11-29 </td>
   <td style="text-align:right;"> 502 </td>
   <td style="text-align:right;"> 93.27 </td>
   <td style="text-align:right;"> 538 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 31 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 2016-11-30 </td>
   <td style="text-align:right;"> 473 </td>
   <td style="text-align:right;"> 93.70 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:left;"> 2016-12-01 </td>
   <td style="text-align:right;"> 552 </td>
   <td style="text-align:right;"> 88.01 </td>
   <td style="text-align:right;"> 627 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 53 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 2016-10-31 </td>
   <td style="text-align:right;"> 557 </td>
   <td style="text-align:right;"> 92.28 </td>
   <td style="text-align:right;"> 604 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:right;"> 19 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 2016-11-01 </td>
   <td style="text-align:right;"> 511 </td>
   <td style="text-align:right;"> 88.42 </td>
   <td style="text-align:right;"> 578 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 30 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 2016-11-03 </td>
   <td style="text-align:right;"> 370 </td>
   <td style="text-align:right;"> 97.57 </td>
   <td style="text-align:right;"> 379 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 8 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 2016-11-03 </td>
   <td style="text-align:right;"> 490 </td>
   <td style="text-align:right;"> 92.04 </td>
   <td style="text-align:right;"> 532 </td>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:right;"> 16 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 2016-11-04 </td>
   <td style="text-align:right;"> 460 </td>
   <td style="text-align:right;"> 89.92 </td>
   <td style="text-align:right;"> 512 </td>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:right;"> 23 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:left;"> 2016-11-05 </td>
   <td style="text-align:right;"> 546 </td>
   <td style="text-align:right;"> 93.23 </td>
   <td style="text-align:right;"> 586 </td>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:right;"> 23 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 2016-10-17 </td>
   <td style="text-align:right;"> 486 </td>
   <td style="text-align:right;"> 87.25 </td>
   <td style="text-align:right;"> 557 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 48 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 2016-10-18 </td>
   <td style="text-align:right;"> 551 </td>
   <td style="text-align:right;"> 89.95 </td>
   <td style="text-align:right;"> 613 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 50 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 2016-10-19 </td>
   <td style="text-align:right;"> 569 </td>
   <td style="text-align:right;"> 88.07 </td>
   <td style="text-align:right;"> 646 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 67 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 2016-10-20 </td>
   <td style="text-align:right;"> 525 </td>
   <td style="text-align:right;"> 86.06 </td>
   <td style="text-align:right;"> 610 </td>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:right;"> 68 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:left;"> 2016-10-21 </td>
   <td style="text-align:right;"> 459 </td>
   <td style="text-align:right;"> 85.62 </td>
   <td style="text-align:right;"> 536 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 60 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 2016-10-18 </td>
   <td style="text-align:right;"> 413 </td>
   <td style="text-align:right;"> 89.37 </td>
   <td style="text-align:right;"> 462 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 43 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 2016-10-19 </td>
   <td style="text-align:right;"> 461 </td>
   <td style="text-align:right;"> 88.49 </td>
   <td style="text-align:right;"> 521 </td>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:right;"> 46 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 2016-10-20 </td>
   <td style="text-align:right;"> 461 </td>
   <td style="text-align:right;"> 82.52 </td>
   <td style="text-align:right;"> 559 </td>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:right;"> 74 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 2016-10-22 </td>
   <td style="text-align:right;"> 483 </td>
   <td style="text-align:right;"> 84.16 </td>
   <td style="text-align:right;"> 574 </td>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:right;"> 42 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:left;"> 2016-10-24 </td>
   <td style="text-align:right;"> 405 </td>
   <td style="text-align:right;"> 90.65 </td>
   <td style="text-align:right;"> 447 </td>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:right;"> 27 </td>
  </tr>
</tbody>
</table></div>
<br />

Applying the same approach to bout-level sleep data from the tour period, we can calculate an appropriate day-level metric for sleep efficiency through this process:

1. For each bout, back-calculate total time in bed using the formula above (sleep_duration / sleep_efficiency).
2. Calculate day-level totals for time in bed and sleep duration.
3. Calculate day-level sleep efficiency as:

$efficiency_{day-level} = \frac {total\ time\ in\ bed_{day}} {sleep\ duration_{day}}$

## Analysis

Most variables violated parametric assumptions, so non-parametric techniques were used. Statistical significance was set at p < 0.05.

To determine the impact of in-flight sleep on tournament measures, a median split was used to create sub-samples based on 1) in-flight sleep quantity (two groups: higher duration vs. lower duration) and 2) in-flight sleep quality (two groups: higher efficiency vs. lower efficiency). Two non-parametric factorial analyses (Noguchi, et al., 2012) were conducted with a two-way design to test for differences between groups and across tournament dates: 1) in-flight duration group x date, and 2) in-flight efficiency group x date. The non-parametric factorial approach is analogous to the parametric ANOVA, and its primary output is the F-type statistic. Where significant main or interaction effects were identified, pairwise comparisons were performed with Dunn-Bonferroni corrections applied to reduce the risk of Type I errors.

Descriptive statistics were calculated and plotted to show how the team's sleep, perceived jetlag, and self-reported well-being changed day-to-day over the course of the tournament period. Further context was added to the description and visualisation of tournament sleep by incorporating team means for habitual sleep, to illustrate where nightly sleep deviated from habitual values.

Spearman’s correlation coefficients (rho) were calculated to assess the bivariate associations between i) sleep measures and internal training and / or playing loads incurred on the subsequent day, ii) sleep measures and self-reported well-being recorded on the subsequent day, iii) sleep and perceived ratings of jetlag recorded on the subsequent day.

Statistical analyses were conducted using R version 3.6.1 (R Core Team, 2019) and the following packages: here (Müller, 2017), chron, (James & Hornik, 2020), dplyr (Wickham, et al., 2020), tidyr (Wickham & Henry, 2020), stringr (Wickham, 2019), purrr (Henry & Wickham, 2019), purrr (Henry & Wickham, 2019), tibble (Müller & Wickham, 2020), nparLD (Noguchi, et al., 2012), psych (Revelle, 2019), ggplot2 (Wickham, 2016), knitr (Xie, 2015), kableExtra (Zhu, 2019).

## All R packages


```r
# R project management ---------------------------------------------------------

library(here)

# Data processing --------------------------------------------------------------

library(chron)
library(dplyr)
library(tidyr)
library(stringr)
library(purrr)
library(tibble)

# Statistical analysis ---------------------------------------------------------

library(nparLD)
library(psych)

# Visualisations ---------------------------------------------------------------

library(ggplot2)
library(patchwork)

# Publishing tools -------------------------------------------------------------

library(knitr)
library(kableExtra)
```

- R Core Team, 2019, *R: A language and environment for statistical computing*. Vienna, Austria: R Foundation for Statistical Computing. URL: https://www.R-project.org/
- Müller, K., 2017, "here: A simpler way to find your files", R package version 0.1. URL: https://CRAN.R-project.org/package=here
- James, D. & Hornik, K., 2020, "chron: Chronological objects which can handle dates and times", R package 2.3-55. URL: https://CRAN.R-project.org/package=chron
- Wickham, H., François, R., Henry, L., & Müller, K., 2020, "dplyr: A grammar of data manipulation", R package version 0.8.5. URL: https://CRAN.R-project.org/package=dplyr
- Wickham, H. & Henry, L, 2020, "tidyr: Tidy messy data", R package version 1.1.0. URL: https://CRAN.R-project.org/package=tidyr
- Wickham, H., 2019, "stringr: Simple, consistent wrappers for common string operations", R package version 1.4.0. URL: https://CRAN.R-project.org/package=stringr
- Henry, L. & Wickham, H., 2019, "purrr: Functional programming tools", R package version 0.3.3. URL: https://CRAN.R-project.org/package=purrr
- Müller, K. & Wickham, H., 2020, "tibble: Simple data frames", R package version 3.0.1. URL: https://CRAN.R-project.org/package=tibble
- Noguchi, K., Gel, Y.R., Brunner, E., & Konietschke, F., 2012, "nparLD: An R software package for the nonparametric analysis of longitudinal data in factorial experiments", *Journal of Statistical Software, 50*(12), 1-23. URL: http://www.jstatsoft.org/v50/i12/
- Revelle, W., 2019, "psych: Procedures for psychological, psychometric, and personality research", R package 1.9.12. URL: https://CRAN.R-project.org/package=psych
- Wickham, H., 2016, *ggplot2: Elegant Graphics for Data Analysis*, New York, NY: Springer-Verlag. URL: https://ggplot2.tidyverse.org
- Pedersen, T.L., 2019, "patchwork: The composer of plots", R package version 1.0.0. URL: https://CRAN.R-project.org/package=patchwork
- Xie, Y., 2015, *Dynamic Documents with R and knitr* (2nd ed.). Boca Raton, FL: Chapman and Hall/CRC. URL: https://bookdown.org/yihui/rmarkdown/
- Zhu, H., 2019, "kableExtra: Construct complex table with 'kable' and pipe syntax", R package version 1.1.0. URL: https://CRAN.R-project.org/package=kableExtra
