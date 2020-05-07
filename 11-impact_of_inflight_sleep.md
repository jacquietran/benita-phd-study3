# Impact of in-flight sleep {#impactofinflightsleep}

RQ2: "What is the impact of in-flight sleep on the subsequent tournament sleep, self-reported well-being, and perceptions of jetlag upon arrival at the competition destination?"



## Assumption tests

**TO DO:** Conduct assumption tests. Given that the assumption testing for RQ3 (correlation analysis) showed that *"many variables across the load, sleep, jetlag, and well-being measures do not approximate normal"*, we proceed for now on the basis that assumption testing for RQ2 will show similar results.

For RQ2: we want to compare two groups (poor in-flight sleep vs. good in-flight sleep), accounting for repeated measures of the dependent variable (i.e., daily measures). The appropriate test is a non-parametric test for factorial longitudinal data, as described here by Noguchi et al., 2012: https://www.jstatsoft.org/index.php/jss/article/view/v050i12/v50i12.pdf

## Analysis steps

With aggregate in-flight sleep data, apply median split to create groups based on their in-flight sleep quantity ("high" / "low") and their in-flight sleep quality ("high" / "low").

Convert tournament period data to long format. At minimum, this data set needs these variables:
- Response / dependent variable (e.g., sleep duration)
- Day of the tournament (this is our time variable)
- In-flight sleep quantity group (this is one of our grouping variables)
- In-flight sleep quality group (this is one of our grouping variables)

Using `nparLD` package

- Build omnibus model. Inspect plot and model summary.
- Conduct pairwise comparisons with Bonferroni adjustment of alpha level.

## Considerations

If we had sufficient sample size, the best approach would be to apply the F2-LD-F1 design as described by [Noguchi, et al., 2012](https://www.jstatsoft.org/index.php/jss/article/view/v050i12/v50i12.pdf). This is analogous to a three-way ANOVA. With such a design, the omnibus model would be specified as:

response_variable ~ day * inflight_sleep_quantity_group * inflight_sleep_quality_group

And the results of the omnibus model would allow us to check for:

- Main effects
     - Is there a main effect for "day"? That is, are there changes in the response variable over time (over the tournament period)?
     - Is there a main effect for "in-flight sleep quantity group"? That is, do players differ in the response variable based on whether they are in the "high" vs. "low" group for in-flight sleep quantity?
     - Is there a main effect for "in-flight sleep quality group"? That is, do players differ in the response variable based on whether they are in the "high" vs. "low" group for in-flight sleep quality?
- Interaction effects
     - Is there an interaction between "day" and "in-flight sleep quantity group"? That is, do the time profiles of the response variable differ between the two groups that are based on in-flight sleep quantity?
     - Is there an interaction between "day" and "in-flight sleep quality group"? That is, do the time profiles of the response variable differ between the two groups that are based on in-flight sleep quality?
     - Is there an interaction between "in-flight sleep quantity" and "in-flight sleep quality"? Think of a quadrant: 1) high-quantity & high-quality in-flight sleep; 2) high-quantity & low-quality in-flight sleep; 3) low-quantity & high-quality in-flight sleep; and 4) low-quantity & low-quality in-flight sleep.
     - Is there an interaction betwen "day", "in-flight sleep quantity", and "in-flight sleep quality"? That is, do the time profiles differ of the response variable between groups based on both their in-flight sleep quantity AND quality? The quadrant idea applies here too.
     
While this is the ideal approach, our sample size is too small to be "cut" this many ways. We may need to make mention of this when discussing analysis methods / limitations.
