# nparLD tutorial - Sleep data {#nparLDsleep}

Walkthrough of a two-factor analysis with the `nparLD` package, using Study 3 data.

## Set up




```r
# Load libraries ---------------------------------------------------------------

library(here)
library(nparLD)
library(dplyr)
library(tidyr)
library(knitr)
library(kableExtra)
library(purrr)
library(tibble)
library(stringr)

# Import the data --------------------------------------------------------------

# In-flight sleep data, aggregated across travel dates
sleep_inflight_aggregate_indiv <- readRDS(
  here("outputs/sleep_inflight_aggregate_indiv.rds"))

# Tournament sleep data per day
sleep_per_day_tournament <- readRDS(
  here("outputs/sleep_per_day_tournament.rds"))
```

## Wrangle the data

Using the **aggregated in-flight** data, we want to apply a median split approach to create two sub-samples based on:

- Players' in-flight sleep quantity (i.e., sleep duration)
- Players' in-flight sleep quality (i.e., sleep efficiency)


```r
# Calculate median for in-flight sleep duration
sleep_inflight_aggregate_indiv %>%
  summarise(
    sleep_duration_agg_median = median(sleep_duration_agg)
    ) -> sleep_duration_agg_median

# Calculate median for in-flight sleep efficiency
sleep_inflight_aggregate_indiv %>%
  summarise(
    sleep_efficiency_agg_median = median(sleep_efficiency_agg)
    ) -> sleep_efficiency_agg_median

# Categorise individuals based on median
# Group 1 - above / equal to the median
# Group 2 - below the median
sleep_inflight_aggregate_indiv %>%
  mutate(
    sleep_duration_agg_med = as.numeric(sleep_duration_agg_median),
    sleep_efficiency_agg_med = as.numeric(sleep_efficiency_agg_median),
    inflight_duration_group = case_when(
      sleep_duration_agg >= sleep_duration_agg_med ~ "Higher in-flight duration",
      TRUE                                         ~ "Lower in-flight duration"),
    inflight_efficiency_group = case_when(
      sleep_efficiency_agg >= sleep_efficiency_agg_med ~ "Higher in-flight efficiency",
      TRUE                                             ~ "Lower in-flight efficiency")) %>%
  select(
    ath_id, sleep_duration_agg, sleep_duration_agg_med, inflight_duration_group,
    sleep_efficiency_agg, sleep_efficiency_agg_med, inflight_efficiency_group
  ) -> median_split_groups
```

After applying the median split approach, the sub-samples look like this:


```r
median_split_groups %>%
  kable() %>%
  kable_styling() %>%
  scroll_box(width = "750px", height = "400px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:400px; overflow-x: scroll; width:750px; "><table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> ath_id </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_duration_agg </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_duration_agg_med </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> inflight_duration_group </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_efficiency_agg </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_efficiency_agg_med </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> inflight_efficiency_group </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:right;"> 599 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:right;"> 90.08 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:right;"> 761 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:right;"> 93.15 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 03 </td>
   <td style="text-align:right;"> 667 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:right;"> 83.17 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Lower in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 04 </td>
   <td style="text-align:right;"> 918 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Higher in-flight duration </td>
   <td style="text-align:right;"> 73.21 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Lower in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 05 </td>
   <td style="text-align:right;"> 1213 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Higher in-flight duration </td>
   <td style="text-align:right;"> 82.07 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Lower in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 06 </td>
   <td style="text-align:right;"> 740 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:right;"> 90.46 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 08 </td>
   <td style="text-align:right;"> 791 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:right;"> 81.29 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Lower in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 10 </td>
   <td style="text-align:right;"> 1223 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Higher in-flight duration </td>
   <td style="text-align:right;"> 87.17 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Lower in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 11 </td>
   <td style="text-align:right;"> 1002 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Higher in-flight duration </td>
   <td style="text-align:right;"> 92.95 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 12 </td>
   <td style="text-align:right;"> 871 </td>
   <td style="text-align:right;"> 831 </td>
   <td style="text-align:left;"> Higher in-flight duration </td>
   <td style="text-align:right;"> 93.06 </td>
   <td style="text-align:right;"> 88.625 </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
</tbody>
</table></div>

To use these groupings in the analysis, we need to merge the info above with data captured during the tournament period.


```r
# Simply the median_split_groups data frame to
# isolate only the variables to be merged
median_split_groups %>%
  select(ath_id, inflight_duration_group, inflight_efficiency_group
         ) -> median_split_groups_simple

sleep_per_day_tournament %>%
  # Exclude ath_id == 07; no in-flight sleep data recorded
  filter(ath_id != "07") %>%
  # {nparLD} requires "complete" cases, with
  # missing values in response variable specified as NA
  # BUT NO MISSING VALUES in the factors (e.g., day, group)
  complete(
    ath_id, nesting(effective_start_date)
    ) -> tournament_sleep
# More info on data structure required for {nparLD} here:
# https://community.rstudio.com/t/arguments-imply-differing-number-of-rows/11479

# Merge the group labels in with the tournament sleep data
tournament_sleep <- left_join(
  tournament_sleep, median_split_groups_simple)
```

For the purposes of this tutorial, we are going to simplify the data set further to focus on only those variables needed to complete one factorial analysis. Specifically, we will keep these variables:

- `ath_id`
- `effective_start_date`
- `sleep_duration_total`
- `inflight_duration_group`
- `inflight_efficiency_group`


```r
# Simplify the tournament_sleep data frame to keep
# only the variables needed for the factorial analysis
tournament_sleep %>%
  select(
    ath_id, effective_start_date, sleep_duration_total,
    inflight_duration_group, inflight_efficiency_group
  ) -> tournament_sleep_duration
```

The `tournament_sleep_duration` data set looks like this (first 20 observations shown):


```r
tournament_sleep_duration %>%
  head(n = 20) %>%
  kable() %>%
  kable_styling() %>%
  scroll_box(width = "700px", height = "400px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:400px; overflow-x: scroll; width:700px; "><table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> ath_id </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> effective_start_date </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> sleep_duration_total </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> inflight_duration_group </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> inflight_efficiency_group </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-10 </td>
   <td style="text-align:right;"> 587 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-11 </td>
   <td style="text-align:right;"> 554 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-12 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-13 </td>
   <td style="text-align:right;"> 552 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-14 </td>
   <td style="text-align:right;"> 562 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-15 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-16 </td>
   <td style="text-align:right;"> 505 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-17 </td>
   <td style="text-align:right;"> 491 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-18 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-19 </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-20 </td>
   <td style="text-align:right;"> 487 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 01 </td>
   <td style="text-align:left;"> 2016-03-21 </td>
   <td style="text-align:right;"> 400 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-10 </td>
   <td style="text-align:right;"> 548 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-11 </td>
   <td style="text-align:right;"> 541 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-12 </td>
   <td style="text-align:right;"> 504 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-13 </td>
   <td style="text-align:right;"> 480 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-14 </td>
   <td style="text-align:right;"> 493 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-15 </td>
   <td style="text-align:right;"> 547 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-16 </td>
   <td style="text-align:right;"> 621 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
  <tr>
   <td style="text-align:left;"> 02 </td>
   <td style="text-align:left;"> 2016-03-17 </td>
   <td style="text-align:right;"> 569 </td>
   <td style="text-align:left;"> Lower in-flight duration </td>
   <td style="text-align:left;"> Higher in-flight efficiency </td>
  </tr>
</tbody>
</table></div>

## Run the omnibus model

### 1) Grouped on in-flight sleep quantity

This is the code chunk that gives us our "ANOVA-type" statistics for assessing main and interaction effects.


```r
# Build the omnibus model
invisible(
  capture.output(
    omnibus_model <- 
      f1.ld.f1(
        # Specify the model components
        y = tournament_sleep_duration$sleep_duration_total,
        time = tournament_sleep_duration$effective_start_date,
        group = tournament_sleep_duration$inflight_duration_group,
        subject = tournament_sleep_duration$ath_id,
        # Specify names for the time and group factors
        time.name = "date",
        group.name = "group_inflight_duration",
        # Change arguments for auto display of certain outputs
        plot.RTE = FALSE,
        description = FALSE,
        order.warning = TRUE)
    )
  )
```


```r
# Print ANOVA-type output for the omnibus model
omnibus_model$ANOVA.test
```

```
##                              Statistic       df    p-value
## group_inflight_duration      0.2200005 1.000000 0.63903951
## date                         2.3253317 4.286523 0.04962238
## group_inflight_duration:date 1.2256602 4.286523 0.29659725
```

The results above can be interpreted as follows:

- There is no main effect for `group_inflight_duration` (p = 0.64). That is, the higher and lower in-flight sleep duration groups **are not different** in terms of the daily sleep duration obtained by players during the tournament period.
- There is a main effect for `date` (p < 0.05). That is, **there are differences (changes) over time** in the daily sleep duration that players obtained during the tournament period.
- There is no interaction effect for `group_inflight_duration` x `group` (p = 0.30). That is, the higher and lower in-flight sleep duration groups **are not different** in terms of how their daily sleep duration changes over time (i.e., no significant difference in their time profiles).

## Run multiple comparison tests

To run multiple comparison (pairwise) tests, we firstly create data subsets that focus on the pair of factor levels we want to compare.

The results from the omnibus model indicate a statistically significant main effect for `date`, so we will compare all paired levels within this factor.

There are no statistically significant effects for the `group_inflight_duration`, nor for the interaction between `group_inflight_duration` and `date`. Therefore, there is no need to conduct pairwise tests for these effects.

### Main effect for TIME

The omnibus model indicates a statistically significant main effect for `date`. This factor has 12 levels (i.e., 12 distinct dates), spanning each date from 2016-03-10 to 2016-03-21, inclusive.

To identify where differences lie within the main effect for `date`, we need to compare all pairs of, identify exactly where the differences (changes) over time lie. This means we need to create 66 subsets for the following pairwise comparisons:

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:390px; overflow-x: scroll; width:220px; "><table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> pair_num </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> pairs </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> 1 </td>
   <td style="text-align:left;"> March 10 vs. 11 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 2 </td>
   <td style="text-align:left;"> March 10 vs. 12 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 3 </td>
   <td style="text-align:left;"> March 10 vs. 13 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 4 </td>
   <td style="text-align:left;"> March 10 vs. 14 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 5 </td>
   <td style="text-align:left;"> March 10 vs. 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 6 </td>
   <td style="text-align:left;"> March 10 vs. 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 7 </td>
   <td style="text-align:left;"> March 10 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 8 </td>
   <td style="text-align:left;"> March 10 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 9 </td>
   <td style="text-align:left;"> March 10 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 10 </td>
   <td style="text-align:left;"> March 10 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 11 </td>
   <td style="text-align:left;"> March 10 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 12 </td>
   <td style="text-align:left;"> March 11 vs. 12 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 13 </td>
   <td style="text-align:left;"> March 11 vs. 13 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 14 </td>
   <td style="text-align:left;"> March 11 vs. 14 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 15 </td>
   <td style="text-align:left;"> March 11 vs. 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 16 </td>
   <td style="text-align:left;"> March 11 vs. 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 17 </td>
   <td style="text-align:left;"> March 11 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 18 </td>
   <td style="text-align:left;"> March 11 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 19 </td>
   <td style="text-align:left;"> March 11 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 20 </td>
   <td style="text-align:left;"> March 11 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 21 </td>
   <td style="text-align:left;"> March 11 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 22 </td>
   <td style="text-align:left;"> March 12 vs. 13 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 23 </td>
   <td style="text-align:left;"> March 12 vs. 14 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 24 </td>
   <td style="text-align:left;"> March 12 vs. 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 25 </td>
   <td style="text-align:left;"> March 12 vs. 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 26 </td>
   <td style="text-align:left;"> March 12 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 27 </td>
   <td style="text-align:left;"> March 12 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 28 </td>
   <td style="text-align:left;"> March 12 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 29 </td>
   <td style="text-align:left;"> March 12 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 30 </td>
   <td style="text-align:left;"> March 12 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 31 </td>
   <td style="text-align:left;"> March 13 vs. 14 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 32 </td>
   <td style="text-align:left;"> March 13 vs. 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 33 </td>
   <td style="text-align:left;"> March 13 vs. 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 34 </td>
   <td style="text-align:left;"> March 13 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 35 </td>
   <td style="text-align:left;"> March 13 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 36 </td>
   <td style="text-align:left;"> March 13 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 37 </td>
   <td style="text-align:left;"> March 13 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 38 </td>
   <td style="text-align:left;"> March 13 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 39 </td>
   <td style="text-align:left;"> March 14 vs. 15 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 40 </td>
   <td style="text-align:left;"> March 14 vs. 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 41 </td>
   <td style="text-align:left;"> March 14 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 42 </td>
   <td style="text-align:left;"> March 14 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 43 </td>
   <td style="text-align:left;"> March 14 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 44 </td>
   <td style="text-align:left;"> March 14 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 45 </td>
   <td style="text-align:left;"> March 14 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 46 </td>
   <td style="text-align:left;"> March 15 vs. 16 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 47 </td>
   <td style="text-align:left;"> March 15 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 48 </td>
   <td style="text-align:left;"> March 15 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 49 </td>
   <td style="text-align:left;"> March 15 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 50 </td>
   <td style="text-align:left;"> March 15 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 51 </td>
   <td style="text-align:left;"> March 15 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 52 </td>
   <td style="text-align:left;"> March 16 vs. 17 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 53 </td>
   <td style="text-align:left;"> March 16 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 54 </td>
   <td style="text-align:left;"> March 16 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 55 </td>
   <td style="text-align:left;"> March 16 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 56 </td>
   <td style="text-align:left;"> March 16 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 57 </td>
   <td style="text-align:left;"> March 17 vs. 18 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 58 </td>
   <td style="text-align:left;"> March 17 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 59 </td>
   <td style="text-align:left;"> March 17 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 60 </td>
   <td style="text-align:left;"> March 17 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 61 </td>
   <td style="text-align:left;"> March 18 vs. 19 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 62 </td>
   <td style="text-align:left;"> March 18 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 63 </td>
   <td style="text-align:left;"> March 18 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 64 </td>
   <td style="text-align:left;"> March 19 vs. 20 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 65 </td>
   <td style="text-align:left;"> March 19 vs. 21 </td>
  </tr>
  <tr>
   <td style="text-align:right;"> 66 </td>
   <td style="text-align:left;"> March 20 vs. 21 </td>
  </tr>
</tbody>
</table></div>

**Note:** For this part of the analysis, the Bonferroni correction is applied to adjust the alpha level for the 66 pairwise tests we would conduct. So an alpha of 0.05 divided by 66 (# of pairwise tests) = significant pairwise differences defined at p < 0.00076.

#### Creating subsets for pairwise comparisons

We want to create 66 data subsets for every pair of dates listed above. If we were to create subsets one at a time, we could apply the code chunk below 66 times.


```r
data %>%
  filter(
    as.character(
      effective_start_date) %in% c(item1, item2)
    ) -> data_subset

# Example with Study 3 data
tournament_sleep_duration %>%
  filter(
    as.character(
      effective_start_date) %in% c("2016-03-10", "2016-03-11")
    ) -> tournament_sleep_duration_subset_01
```

Creating such a large number of data subsets is repetitive, very manual, and prone to error. Another way is to "label" the rows that belong to the subsets we want to create. **Note:** The code chunk below is still quite tedious and manual to write up, but it does make things easier later down the line. I'm sure there's a better way to produce the code chunk below, but it does what I want it to do :)


```r
tournament_sleep_duration %>%
  mutate(
    # March 10 pairs
    subset_march_10_vs_11 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_12 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_13 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_14 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_15 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_16 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_17 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_18 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_19 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_20 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_10_vs_21 = case_when(
      effective_start_date == "2016-03-10" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 11 pairs
    subset_march_11_vs_12 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_13 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_14 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_15 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_16 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_17 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_18 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_19 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_20 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_11_vs_21 = case_when(
      effective_start_date == "2016-03-11" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 12 pairs
    subset_march_12_vs_13 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_14 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_15 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_16 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_17 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_18 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_19 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_20 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_12_vs_21 = case_when(
      effective_start_date == "2016-03-12" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 13 pairs
    subset_march_13_vs_14 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_15 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_16 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_17 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_18 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_19 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_20 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_13_vs_21 = case_when(
      effective_start_date == "2016-03-13" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 14 pairs
    subset_march_14_vs_15 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_14_vs_16 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_14_vs_17 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_14_vs_18 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_14_vs_19 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_14_vs_20 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_14_vs_21 = case_when(
      effective_start_date == "2016-03-14" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 15 pairs
    subset_march_15_vs_16 = case_when(
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_15_vs_17 = case_when(
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_15_vs_18 = case_when(
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_15_vs_19 = case_when(
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_15_vs_20 = case_when(
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_15_vs_21 = case_when(
      effective_start_date == "2016-03-15" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 16 pairs
    subset_march_16_vs_17 = case_when(
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_16_vs_18 = case_when(
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_16_vs_19 = case_when(
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_16_vs_20 = case_when(
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_16_vs_21 = case_when(
      effective_start_date == "2016-03-16" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 17 pairs
    subset_march_17_vs_18 = case_when(
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_17_vs_19 = case_when(
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_17_vs_20 = case_when(
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_17_vs_21 = case_when(
      effective_start_date == "2016-03-17" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 18 pairs
    subset_march_18_vs_19 = case_when(
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_18_vs_20 = case_when(
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_18_vs_21 = case_when(
      effective_start_date == "2016-03-18" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 19 pairs
    subset_march_19_vs_20 = case_when(
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    subset_march_19_vs_21 = case_when(
      effective_start_date == "2016-03-19" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_),
    # Remaining March 20 pairs
    subset_march_20_vs_21 = case_when(
      effective_start_date == "2016-03-20" ~ sleep_duration_total,
      effective_start_date == "2016-03-21" ~ sleep_duration_total,
      TRUE                                 ~ NA_real_)) %>%
  select(
    -sleep_duration_total, -starts_with("inflight")
    ) -> tournament_sleep_duration_wide

# Convert from wide to long format
tournament_sleep_duration_wide %>%
  gather(
    key = "subset_id",
    value = "sleep_duration_total",
    -c("ath_id", "effective_start_date"),
    na.rm = TRUE,
    factor_key = TRUE) %>%
  group_by(subset_id) %>%
  complete(
    ath_id, nesting(effective_start_date)) %>%
  ungroup(
    ) -> tournament_sleep_duration_subsets

# Need to merge the median split grouping vars back in
tournament_sleep_duration_subsets <- left_join(
  tournament_sleep_duration_subsets, median_split_groups_simple)
```

```
## Joining, by = "ath_id"
```

Executing the code chunk above labels all subsets while keeping them in the same data frame. This allows us to run one omnibus model for each subset in a looping (iterative) fashion. Iteratively modelling the 66 subsets creates 66 models, or in reality 66 pairwise tests.


```r
invisible(
  capture.output(
    pairwise_tests_time_models <- 
      tournament_sleep_duration_subsets %>%
      split(.$subset_id) %>%
      map(
        ~f1.ld.f1(
          # Specify the model components
          y = .$sleep_duration_total,
          time = .$effective_start_date,
          group = .$inflight_duration_group,
          subject = .$ath_id,
          # Specify names for the time and group factors
          time.name = "date",
          group.name = "group_inflight_duration",
          # Change arguments for auto display of certain outputs
          plot.RTE = FALSE,
          description = FALSE,
          order.warning = FALSE)
      )
  )
)
```

The `pairwise_tests_time_models` object stores the model outputs for all 66 models (in this case, pairwise tests) we have run to compare sleep duration between every pair of dates.

We want to focus our attention on the pairwise test results.


```r
# Extract ANOVA.test element and tidy up the resulting data frame
pairwise_tests_time_models %>%
  lapply("[", "ANOVA.test") %>%
  as.data.frame() %>%
  rownames_to_column("test_effect") %>%
  filter(test_effect == "date") %>%
  gather(
    key = "test_statistic",
    value = "result",
    -test_effect) %>%
  separate(
    col = test_statistic,
    into = c("subset_id", "stat_type"),
    sep = "[.]",
    extra = "merge") %>%
  rename(
    test_statistic = stat_type) %>%
  mutate(
    test_statistic = case_when(
      str_detect(test_statistic, "Statistic") ~ "ANOVA-type statistic",
      str_detect(test_statistic, "df") ~ "df",
      str_detect(test_statistic, "p") ~ "p")) %>%
  filter(
    test_statistic == "p") %>%
  mutate(
    result = round(result, 6),
    p_adjusted = round(0.05 / 66, 6),
    pairwise_diff = case_when(
      result < p_adjusted ~ "Yes",
      TRUE                ~ "No")
    ) -> pairwise_tests_time_results
```

There is one instance (for `subset_march_11_vs_14`) where the p value is NA - this is due to tied mean ranks when comparing between dates.


```r
pairwise_tests_time_results %>%
  arrange(desc(pairwise_diff)) %>%
  kable() %>%
  kable_styling() %>%
  scroll_box(width = "750px", height = "350px")
```

<div style="border: 1px solid #ddd; padding: 0px; overflow-y: scroll; height:350px; overflow-x: scroll; width:750px; "><table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> test_effect </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> subset_id </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> test_statistic </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> result </th>
   <th style="text-align:right;position: sticky; top:0; background-color: #FFFFFF;"> p_adjusted </th>
   <th style="text-align:left;position: sticky; top:0; background-color: #FFFFFF;"> pairwise_diff </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.000286 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.000702 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_16_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.000031 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_17_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.000000 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> Yes </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_11 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.113013 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_12 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.357643 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_13 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.640302 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_14 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.234075 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_15 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.644076 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_16 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.735317 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.400045 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.005546 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.621954 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_10_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.942575 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_12 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.787774 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_13 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.469763 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_14 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> NA </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_15 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.147293 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_16 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.120385 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.023604 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.032233 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.230043 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.414944 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_11_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.004290 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_13 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.816707 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_14 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.507127 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_15 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.317400 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_16 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.087546 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.048122 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.091431 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.100714 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_12_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.826860 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_14 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.730647 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_15 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.641540 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_16 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.665106 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.282121 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.021065 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.464261 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.890001 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_13_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.028625 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_15 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.241962 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_16 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.260858 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.162436 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.102348 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.216146 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.606341 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_14_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.016450 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_15_vs_16 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.876363 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_15_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.694875 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_15_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.034717 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_15_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.907405 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_15_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.826153 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_15_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.002311 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_16_vs_17 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.489525 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_16_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.003184 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_16_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.655975 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_16_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.756853 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_17_vs_18 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.002509 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_17_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.807183 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_17_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.361980 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_18_vs_19 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.058859 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_18_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.086439 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_18_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.656568 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_19_vs_20 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.600208 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_19_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.004111 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
  <tr>
   <td style="text-align:left;"> date </td>
   <td style="text-align:left;"> subset_march_20_vs_21 </td>
   <td style="text-align:left;"> p </td>
   <td style="text-align:right;"> 0.006386 </td>
   <td style="text-align:right;"> 0.000758 </td>
   <td style="text-align:left;"> No </td>
  </tr>
</tbody>
</table></div>

