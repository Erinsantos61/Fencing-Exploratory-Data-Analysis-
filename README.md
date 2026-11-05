
# NCAA Fencing: Do Ivy League Fencers Perform Differently?

An exploratory data analysis in R of the 2024 NCAA Fencing National Championship. I'm a club foil fencer at a liberal arts college, and I chose this dataset because it combines sports performance with data analysis, and because it includes an Ivy vs. non-Ivy variable that let me test whether academic prestige goes along with stronger fencing results.

**Tools:** R, R Markdown (`tidyverse`, `effectsize`)

**Slides:** [Fencing Data Analysis Presentation](Fencing%20Data%20Analysis%20Presentation.pdf)

---

## Project Question

Do Ivy League fencers perform differently from non-Ivy fencers in terms of **victories**, **touch differential**, and **overall ranking**?

## Dataset

- **Source:** [2024 NCAA Fencing National Championship](https://www.kaggle.com/datasets/mexwell/2024-ncaa-fencing-national-championship) on Kaggle (published by mexwell)
- **File in this repo:** `NCAAfencing.csv`
- **Size:** 144 fencers (rows) and 13 columns, with no missing values
- **Coverage:** 28 schools; 6 events (men's and women's foil, epee, and sabre) with 24 fencers each; every fencer fenced 23 bouts
- **Groups:** 54 Ivy fencers and 90 non-Ivy fencers

| Column | Meaning |
|---|---|
| `Place` | Final placement within the fencer's event (1 to 24) |
| `Tied` | Whether the placement was tied |
| `Name`, `School` | Fencer and school |
| `Victories`, `Bouts`, `Pct` | Bouts won, bouts fenced, and win percentage |
| `TS`, `TR` | Touches scored and touches received |
| `Ind` | Touch differential (indicator score), equal to `TS` − `TR` |
| `Gender`, `Weapon` | Event the fencer competed in |
| `Ivy` | `Ivy` or `Non-Ivy` |

## Methods

1. **Explore:** `glimpse()` and `summary()` to check structure and confirm there were no missing values.
2. **Describe:** mean, standard deviation, and group sizes by Ivy status.
3. **Visualize:** boxplot with violin overlay of victories by Ivy status.
4. **Compare means:** Welch two-sample t-test on victories, with Cohen's d for effect size.
5. **Regression:** `Victories ~ Ind + Ivy` to see whether Ivy status predicts wins once touch differential is accounted for, plus an interaction model (`Ind * Ivy`) tested with ANOVA.
6. **Rank comparison:** converted `Place` to a percentile and compared the groups with a t-test.
7. **Correlations:** correlation structure within each Ivy group.

**What didn't work:** violin plots split by gender and Ivy were cluttered because the subgroups are small, and MANOVA results were statistically sound but hard to interpret without visuals. I shifted to clear two-variable plots.

## Key Findings

| Metric | Ivy | Non-Ivy | Test |
|---|---|---|---|
| Mean victories | 12.54 | 10.88 | Welch t-test p ≈ 0.011, Cohen's d ≈ 0.45 |
| Median victories | 13 | 11 | |
| Mean touch differential (`Ind`) | 6.1 | -3.7 | p ≈ 0.012 |
| Mean placement percentile | 57.2 | 42.6 | p ≈ 0.004 |

1. **Ivy fencers won more bouts on average**, about 1.7 more victories out of 23, a small-to-medium effect.
2. **Touch differential is the strongest predictor of victories.** In the regression, each extra point of `Ind` goes with about 0.16 more victories (p < 0.001), and the model explains about 92% of the variation in victories.
3. **Ivy status adds almost nothing once `Ind` is controlled for** (coefficient ≈ 0.10, p ≈ 0.59). The interaction test was also not significant (p ≈ 0.86), so both groups follow the same trend line.
4. **Ivy fencers tended to place higher** in their events, on average.

The scatterplot of victories against `Ind` shows the Ivy and non-Ivy points sitting on top of each other along the same upward line.

<!-- TODO: knit FencingDataAnalysis.Rmd, save the two charts (boxplot/violin and scatterplot) into an /images folder, then uncomment these lines:
![Distribution of victories by Ivy status](images/victories_by_ivy.png)
![Victories vs indicator score](images/victories_vs_ind.png)
-->

## Conclusion

Ivy fencers had a modest edge in wins, touch differential, and placement. But the regression suggests that edge is explained by how much they outscored their opponents rather than by school affiliation itself, so performance in this dataset looks more individual than institutional.

## Limitations

- **One tournament, small sample.** 144 fencers from a single championship may not represent a full season.
- **Fencers are grouped within schools.** Fencers from the same school aren't fully independent, which the t-tests and regression don't account for.
- **`Ind` is not a separate measure of skill.** It is calculated from touches scored and received in the same bouts that determine victories, so the strong link between `Ind` and `Victories` is partly built in. The regression shows the Ivy gap lines up with touch differential, but it can't show that skill causes wins.
- **Selection.** These are only the fencers who qualified for nationals, so the Ivy and non-Ivy groups may differ in how they got there.
- **Association, not cause.** Nothing here shows that school type causes performance differences.
- **Percentile is approximate.** `Place` runs from 1 to 24 within each of the six events, so the percentile is a simple rescaling of rank.

## Repository Contents

| File | Description |
|---|---|
| `FencingDataAnalysis.Rmd` | R Markdown file with the full analysis and code |
| `NCAAfencing.csv` | Dataset from Kaggle |
| `Fencing Data Analysis Presentation.pdf` | Slide deck summarizing the project |
| `README.md` | This file |

## How to Run

1. Install [R](https://cran.r-project.org/) and [RStudio](https://posit.co/download/rstudio-desktop/).
2. Download or clone this repo and open `FencingDataAnalysis.Rmd` in RStudio.
3. Install the packages:
   ```r
   install.packages(c("tidyverse", "effectsize"))
   ```
4. Keep `NCAAfencing.csv` in the same folder as the `.Rmd`, then click **Knit** to reproduce the report.

## What I'd Explore Next

- Gender and weapon interactions: Ivy schools may perform differently in men's vs. women's events or in specific weapons
- Ranking percentiles to compare Ivy and non-Ivy placements in more detail
- Touch patterns: whether Ivy fencers win by larger or smaller margins
- Historical context: comparing 2024 to earlier NCAA tournaments to see whether the gap is widening or narrowing
- Models that account for school-level grouping, such as a mixed-effects model
