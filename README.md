# How Many Worlds? PCA and Clustering of UN Country Data

**Research question:** Can the world be meaningfully divided into only two groups (developed and developing countries), or does the data suggest a more complex structure?

## Repository contents

| File | Description |
|---|---|
| `STATS_GROUP_WORK.ipynb` | Complete analysis: data preparation, PCA, K-Means clustering, interpretation |
| `UN.csv` | Dataset (207 countries, 14 variables) |
| `elbow_silhouette.png` | Elbow rule and silhouette score for k = 2 to 10 |
| `pca_k2_vs_preferred.png` | Countries in PCA space, coloured by the k = 2 and k = 4 solutions |
| `requirements.txt` | Python packages needed to run the notebook |

To reproduce the analysis, open the notebook in Google Colab or Jupyter and run all cells from top to bottom. K-Means uses `random_state=42`, so the results are reproducible.

---

## 1. Can the world be meaningfully divided into only two clusters (developed vs. developing)?

No. Dividing the world into just two clusters is a significant oversimplification that hides critical variation within the groups.

With k = 2, we get one large cluster of 143 countries and a second cluster of 64 countries. The split does separate the extremes, but the large "developed" cluster puts wealthy countries like Norway and Japan in the same group as middle-income countries like Brazil, China, Mexico and Bolivia, despite large differences in income, health and fertility. The two clusters capture only 41% of the variation between countries.

The PCA plot shows why. Countries form one continuous arc from the poorest (Niger, Afghanistan) to the richest (Norway, Japan), with no natural gap where a dividing line should be. The k = 2 solution simply cuts this continuum in half.

## 2. How many clusters are suggested by the data according to the Elbow Rule and your other evaluation metrics?

The data suggests **four clusters**.

| k | Inertia | Silhouette | Structure captured |
|---|---|---|---|
| 2 | 1364 | **0.398** | 41% |
| 3 | 1089 | 0.336 | 53% |
| 4 | 933 | **0.343** | **60%** |
| 5 | 800 | 0.285 | 66% |
| 6 | 713 | 0.282 | 69% |

- **Elbow Rule:** the curve softens around k = 4. Inertia drops sharply up to k = 4 (−275, then −156), and each further cluster adds less.
- **Silhouette score:** the score is highest at k = 2 (0.398). This happens because PC1 explains over half the variance, so countries lie along one continuous axis, and cutting a continuum in half always produces two compact halves. That boundary is arbitrary, not a natural gap. Among the solutions with more than two clusters, k = 4 has the best silhouette (0.343), and the score drops clearly from k = 5 onwards.
- k = 4 also gives the most distinct and interpretable groups of countries (see Question 6).

## 3. How much of the data structure is captured by two clusters compared with your preferred number of clusters?

Using structure captured = 1 − inertia / total sum of squares:

- **k = 2 captures 41%** of the structure in the data.
- **k = 4 captures 60%** of the structure.

Moving to four clusters explains 19 percentage points more of the differences between countries, almost 50% more than the two-cluster solution. The crosstab in the notebook shows where the gain comes from. The k = 2 "developed" group splits into a rich cluster (26 countries) and a middle cluster (117), and the "developing" group splits into a poor cluster (52) and a poorest cluster (8).

## 4. Does the evidence support the argument in Factfulness that the world should not be understood as simply two groups, but rather as four or more?

Yes, the evidence supports Hans Rosling's argument in *Factfulness*.

Rosling argues that the division into "developed" and "developing" is outdated, and that the world is better understood as four development levels, with most countries in the middle and no gap between "us" and "them". Our results match this on three points:

- **No gap:** the PCA plot shows a continuous arc of countries, not two separate groups.
- **Four levels:** the elbow rule and the best silhouette score above k = 2 both point to four clusters.
- **Most countries are in the middle:** the largest cluster, with 121 of 207 countries, is the middle-income group.

Rosling's levels are based on income alone, while our clusters combine income, health, fertility, education and labour market variables, so the groups are not identical. The overall structure is the same.

## 5. What does PCA tell you about the variables that differentiate countries?

- **PC1 (53.2% of variance)** represents socioeconomic development and health. It has high positive loadings for female and male life expectancy, contraception, education and GDP per capita, and high negative loadings for infant mortality, total fertility rate (tfr) and illiteracy.
- **PC2 (13.1% of variance)** represents education and literacy. It is driven mainly by years of schooling (male and female), illiteracy and GDP per capita. The education variables were 63% missing and imputed, so this component should be interpreted with caution.
- **PC3** represents labour force participation. It is driven almost entirely by female economic activity.

**Key takeaway:** the main thing that differentiates countries is their overall level of development and health (PC1). Because this one component is so dominant, development is better described as a gradient than as a binary. Six components are needed to explain over 90% of the variance (93.6%), and these six were used for clustering.

## 6. Using one representative country from each cluster, how would you describe and interpret the clusters?

| Cluster | Level | Countries | Representative | Fertility | Female life exp. | Infant mortality | GDP per capita |
|---|---|---|---|---|---|---|---|
| 2 | Level 4: High income | 26 | **Norway** | 1.9 | 80.6 | 5 | $33,734 |
| 0 | Level 3: Middle income | 121 | **Brazil** | 2.2 | 71.2 | 42 | $4,510 |
| 1 | Level 2: Low income | 52 | **India** | 3.1 | 62.7 | 72 | $365 |
| 3 | Level 1: Extreme poverty | 8 | **Mozambique** | 6.1 | 48.4 | 110 | $77 |

**Cluster 2: Level 4, high income (Norway).** Very high GDP per capita, long life expectancy (about 80 years), low fertility (median 1.6) and very low infant mortality. Members include Western Europe, the United States, Canada, Japan, Australia and Singapore. They are very similar to each other. Surprising: Greece and Portugal belong here despite much lower incomes, because their health and fertility indicators match Western Europe.

**Cluster 0: Level 3, middle income (Brazil).** Transitioning economies with growing GDP per capita, life expectancy in the high 60s to 70s, fertility close to replacement level, and widespread contraception use. This is the largest and most varied cluster, covering Latin America, Eastern Europe, China, Turkey and the Gulf states. Surprising: oil-rich Kuwait and Qatar are here, not in the rich cluster, which shows that income alone does not determine a country's cluster.

**Cluster 1: Level 2, low income (India).** Higher fertility (about 3 to 5 children), infant mortality of roughly 50 to 85 per 1,000, life expectancy in the 50s and 60s, and high female illiteracy (62% in India). Mostly Sub-Saharan Africa, plus South Asia and parts of North Africa (Egypt, Morocco).

**Cluster 3: Level 1, extreme poverty (Mozambique).** Very high fertility (over 6 children), female life expectancy below 50, infant mortality above 100 per 1,000 births, and high illiteracy. A small group of 8 countries: Mozambique, Burundi, Burkina Faso, DR Congo, Eritrea, Gambia, Laos and Zambia. What separates it from Level 2 is very high female economic activity (86% in Mozambique), which reflects subsistence agriculture rather than formal jobs.

---

## Limitations

- **Imputation:** missing values were filled with the global median. Rich countries often did not report illiteracy, so they received an unrealistically high value (for example, 20.1% female illiteracy for Norway and Germany). This is why the median illiteracy of the high-income cluster looks too high. Imputing by regional median would be more accurate.
- **Small territories:** microstates such as Monaco, Andorra and Tuvalu are missing most indicators, so their cluster assignments are driven by imputed values.
- **K-Means on a continuum:** because countries lie on a continuous gradient, any cluster boundary is partly a modelling choice rather than a natural break.
- **Data age:** the data reflects the late 1990s, and many countries have moved up levels since then.
