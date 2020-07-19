# Tournament sleep {#tournamentsleep}









## Per player

Example interpretations from the plots below:

> When examining sleep obtained during the tournament, athlete 02 recorded the highest sleep duration per night (mean = 9.06 h), while athlete 10 recorded the lowest sleep duration per night (mean = 7.29 h).

> During the tournament, athlete 12 typically had the earliest bed time (mean = 8:35 PM) while athlete 06 typically had the latest bed time (mean = 11:56 PM).



<img src="04-tournament_sleep_files/figure-html/patch_plots_indiv_summary-1.png" width="1152" />

## Team summary

### Plots: Daily sleep

Example interpretations of the plots below:

> On average, nightly sleep duration during the tournament period was greater than habitual sleep duration (habitual mean = 7.98 h), except for on March 18 (mean = 7.21 h) and March 21 (mean = 6.62 h).

> On average, nightly bed time during the tournament period was earlier than the team's habitual bed time (habitual mean = 11:12 PM), except for on March 19 (mean = 11:16 PM), 21 (mean = 11:25 PM), and 22 (mean = 1:10 AM).






```
## Warning: Use of `tournament_bed_wake_daily_means$bed_time_pseudo24h` is
## discouraged. Use `bed_time_pseudo24h` instead.

## Warning: Use of `tournament_bed_wake_daily_means$bed_time_pseudo24h` is
## discouraged. Use `bed_time_pseudo24h` instead.
```

```
## Warning: Use of `tournament_bed_wake_daily_means$wake_up_time_pseudo24h` is
## discouraged. Use `wake_up_time_pseudo24h` instead.

## Warning: Use of `tournament_bed_wake_daily_means$wake_up_time_pseudo24h` is
## discouraged. Use `wake_up_time_pseudo24h` instead.
```

```
## Warning: Use of `tournament_daily_means_sleep_duration$value` is discouraged.
## Use `value` instead.

## Warning: Use of `tournament_daily_means_sleep_duration$value` is discouraged.
## Use `value` instead.
```

```
## Warning: Use of `tournament_daily_means_sleep_latency$value` is discouraged. Use
## `value` instead.

## Warning: Use of `tournament_daily_means_sleep_latency$value` is discouraged. Use
## `value` instead.
```

```
## Warning: Use of `tournament_daily_means_sleep_efficiency$value` is discouraged.
## Use `value` instead.

## Warning: Use of `tournament_daily_means_sleep_efficiency$value` is discouraged.
## Use `value` instead.
```

```
## Warning: Use of `tournament_daily_means_wake_duration$value` is discouraged. Use
## `value` instead.

## Warning: Use of `tournament_daily_means_wake_duration$value` is discouraged. Use
## `value` instead.
```

```
## Warning: Use of `tournament_daily_means_wake_bouts_num$value` is discouraged.
## Use `value` instead.

## Warning: Use of `tournament_daily_means_wake_bouts_num$value` is discouraged.
## Use `value` instead.
```

```
## Warning: Use of `tournament_daily_means_wake_bouts_avg_duration$value` is
## discouraged. Use `value` instead.

## Warning: Use of `tournament_daily_means_wake_bouts_avg_duration$value` is
## discouraged. Use `value` instead.
```

<img src="04-tournament_sleep_files/figure-html/patch_plots_sleep_daily-1.png" width="1152" />

### Tables: Per day across tournament period

Table \@ref(tab:tabletournamentabsoluteteam) below presents team-level summary statistics for tournament sleep, expressed as absolute values.

**Example interpretation:**

> Over the course of the tournament period, athletes recorded a mean sleep duration of 8.33 h of per night (SD = 1.54 h).





```
## Warning: namespace 'highr' is not available and has been replaced
## by .GlobalEnv when processing object '<unknown>'
```

<div style="border: 1px solid #ddd; padding: 5px; overflow-x: scroll; width:800px; "><table class="table" style="margin-left: auto; margin-right: auto;">
<caption>(\#tab:tabletournamentabsoluteteam)Tournament sleep summary for the whole team (absolute values).</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> statistic </th>
   <th style="text-align:left;"> bed_time_HHMM </th>
   <th style="text-align:left;"> wake_up_time_HHMM </th>
   <th style="text-align:right;"> sleep_duration </th>
   <th style="text-align:right;"> sleep_duration_h </th>
   <th style="text-align:right;"> sleep_latency </th>
   <th style="text-align:right;"> sleep_efficiency </th>
   <th style="text-align:right;"> wake_duration </th>
   <th style="text-align:right;"> wake_bouts_num </th>
   <th style="text-align:right;"> wake_bouts_avg_duration </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Mean </td>
   <td style="text-align:left;"> 22:19 </td>
   <td style="text-align:left;"> 06:49 </td>
   <td style="text-align:right;"> 496 </td>
   <td style="text-align:right;"> 8.26 </td>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:right;"> 82.92 </td>
   <td style="text-align:right;"> 72 </td>
   <td style="text-align:right;"> 28 </td>
   <td style="text-align:right;"> 2.45 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> SD </td>
   <td style="text-align:left;"> 01:52 </td>
   <td style="text-align:left;"> 01:37 </td>
   <td style="text-align:right;"> 84 </td>
   <td style="text-align:right;"> 1.40 </td>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:right;"> 7.72 </td>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:right;"> 1.10 </td>
  </tr>
</tbody>
</table></div>

<br />

Table \@ref(tab:tabletournamentrelativeteam) below presents team-level summary statistics for tournament sleep, expressed in values that are relative to each person's habitual sleep characteristics (i.e., relative value = habitual minus tournament).

**Example interpretations:**

> Over the course of the tournament period, athletes obtained 21 more minutes of sleep (SD = 106 min), as compared to their habitual sleep characteristics.

> Typical bed time during the tournament period was 53 minutes earlier than habitual (SD = 41 min) and typical wake-up time during the tournament period was 46 minutes earlier than habitual (SD = 44 min).



<div style="border: 1px solid #ddd; padding: 5px; overflow-x: scroll; width:800px; "><table class="table" style="margin-left: auto; margin-right: auto;">
<caption>(\#tab:tabletournamentrelativeteam)Tournament sleep summary for the whole team (relative to habitual).</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> statistic </th>
   <th style="text-align:right;"> bed_time_min_diff </th>
   <th style="text-align:right;"> wake_up_time_min_diff </th>
   <th style="text-align:right;"> sleep_duration_diff </th>
   <th style="text-align:right;"> sleep_duration_h_diff </th>
   <th style="text-align:right;"> sleep_latency_diff </th>
   <th style="text-align:right;"> sleep_efficiency_diff </th>
   <th style="text-align:right;"> wake_duration_diff </th>
   <th style="text-align:right;"> wake_bouts_num_diff </th>
   <th style="text-align:right;"> wake_bouts_avg_duration_diff </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Mean </td>
   <td style="text-align:right;"> 53 </td>
   <td style="text-align:right;"> 46 </td>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:right;"> 0.27 </td>
   <td style="text-align:right;"> -3 </td>
   <td style="text-align:right;"> -2.12 </td>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:right;"> 0 </td>
   <td style="text-align:right;"> 0.28 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> SD </td>
   <td style="text-align:right;"> -41 </td>
   <td style="text-align:right;"> 44 </td>
   <td style="text-align:right;"> 101 </td>
   <td style="text-align:right;"> 1.68 </td>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:right;"> 9.13 </td>
   <td style="text-align:right;"> 43 </td>
   <td style="text-align:right;"> 15 </td>
   <td style="text-align:right;"> 1.27 </td>
  </tr>
</tbody>
</table></div>
