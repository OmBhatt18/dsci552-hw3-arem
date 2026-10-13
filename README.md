# DSCI 552 HW3 - Activity Recognition (AReM)

**Name:** Om Bhatt<br>
**USC ID:** 7536194801

Time series classification part 1: feature creation/extraction on the UCI AReM dataset.

A person wore wireless sensors on the chest and both ankles while doing 7 activities
(bending1, bending2, cycling, lying, sitting, standing, walking).
Each `datasetN.csv` file is one instance: 6 time series of 480 values
(avg_rss12, var_rss12, avg_rss13, var_rss13, avg_rss23, var_rss23). There are 88 instances.

All the work is in [notebooks/bhatt_om_hw3.ipynb](notebooks/bhatt_om_hw3.ipynb), which is the notebook to grade. It's already run, so the outputs and plots show up on GitHub.
[notebooks/bhatt_om_hw3.html](notebooks/bhatt_om_hw3.html) is the same notebook exported to HTML.

## Contents

- `notebooks/bhatt_om_hw3.ipynb` - main assignment notebook (+ HTML export)
- `data/AReM/` - dataset, the 7 activity folders
- `output/` - CSV outputs from the notebook: the 42-feature table (`arem_time_domain_features.csv`) and the std + 90% bootstrap CI of each feature (`arem_feature_std_bootstrap_ci.csv`)

## What's in the notebook

Problem 1: Time Series Classification Part 1

- (a) load all 88 instances; the loader handles the messy files without editing them
  (bending2/dataset4 is space-delimited, cycling/dataset9 and dataset14 have a trailing comma, sitting/dataset8 has 479 rows)
- (b) train/test split: datasets 1-2 of bending1/bending2 and 1-3 of the other folders are test (19 test / 69 train)
- (c) i. list of time-domain features used in time series classification (central tendency, extremes, spread, order statistics, shape, energy, temporal structure, complexity)
- (c) ii. min, max, mean, median, std, 1st and 3rd quartile for all 6 series: 42 features per instance
- (c) iii. std of each feature with a 90% bootstrap confidence interval (B = 1000)
- (c) iv. the three most important features: mean, std and max (ANOVA F-statistic + correlation, training rows only)

Problem 2: ISLR 3.7.4 (linear vs cubic regression)

- (a) true relationship linear, training RSS: cubic is lower (or equal)
- (b) true relationship linear, test RSS: linear is lower
- (c) true relationship non-linear, training RSS: cubic is lower
- (d) true relationship non-linear, test RSS: not enough information, depends on how far from linear it is

## Results

Median ANOVA F-statistic across the 6 series (training rows, activity as the group):

| statistic | median F |
|---|---|
| mean | 307.4 |
| 3rd quartile | 297.1 |
| median | 252.8 |
| 1st quartile | 135.2 |
| std | 71.9 |
| max | 32.3 |
| min | 16.8 |

Some things I found:
- min2 and min4 are 0 in all 88 instances (the variance series always hit 0), so their std and bootstrap CI are exactly [0, 0]. min6 is 0 in 87 of 88.
- Median and both quartiles are correlated with the mean at 0.98-0.99, so they mostly repeat it. That's why only the mean is kept from that group.
- std adds new information (correlation with the mean is only 0.38) and separates moving activities from still ones.
- max is the best statistic on series 3 and 5 (avg_rss13, avg_rss23).
- The widest bootstrap intervals are for series 5 (avg_rss23).

## Running it

```
pip install -r requirements.txt
jupyter notebook notebooks/bhatt_om_hw3.ipynb
```

The notebook reads the data from `data/AReM/` and saves the CSV outputs to `output/` (paths are relative to the `notebooks/` folder).

## Data

UCI Machine Learning Repository: https://archive.ics.uci.edu/dataset/366/activity+recognition+system+based+on+multisensor+data+fusion+arem

- Palumbo, F., Gallicchio, C., Pucci, R., & Micheli, A. (2016). Activity Recognition system based on Multisensor data fusion (AReM) [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5SS33
- Filippo Palumbo, Claudio Gallicchio, Rita Pucci, Alessio Micheli, Human activity recognition using multisensor data fusion based on Reservoir Computing,
  Journal of Ambient Intelligence and Smart Environments, Volume 8, Number 2, 2016, Pages 87-107.

## AI usage disclaimer

I used Claude only to understand the concepts and the assignment, to set up a format, to ask general programming questions, and to generate sample code that explains how certain programming constructs work.
Claude Code was used to add explanatory comments to some notebook cells, which also helped me understand the concepts. The analysis and code are my own.
