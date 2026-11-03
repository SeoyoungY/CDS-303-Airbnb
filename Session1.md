[← Back to Home](index.md)

# Session 1: Framework Review, Variable Selection, and Supplementary Data

## 1. Paper Summary

**Papers reviewed**
- *Method article:* Alvarez-Garcia, M., Ibar-Alonso, R., & Arenas-Parra, M. (2024). A comprehensive framework for explainable cluster analysis. *Information Sciences, 663*, 120282. https://doi.org/10.1016/j.ins.2024.120282
- *Application article:* Alvarez-Garcia, M., Arenas-Parra, M., & Ibar-Alonso, R. (2024). Uncovering student profiles. An explainable cluster analysis approach to PISA 2022. *Computers & Education, 223*, 105166. https://doi.org/10.1016/j.compedu.2024.105166

**The problem the papers address.** Clustering is a popular way to find structure in large datasets, but it has two weak points. First, its results are only as good as the data: real-world data contain missing values, outliers, many variables, and a mix of numerical and categorical types. Second, because clustering is unsupervised, it produces groups without telling us what the groups mean. Earlier explainable-clustering methods focused only on the second problem and were tied to a specific algorithm. The method article proposes one end-to-end workflow that handles both problems and lets the analyst swap in different algorithms. The authors also released it as an open-source Python package, **Clust-learn**.

### 1.1 The Four-Step Framework
1. **Data preprocessing** – (작성 예정)
2. **Dimensionality reduction** – (작성 예정)
3. **Clustering** – (작성 예정)
4. **Classification for explainability** – (작성 예정)

### 1.2 Why Clustering Results Need Interpretation
(작성 예정)

### 1.3 How Classification Characterizes Clusters
(작성 예정)

## 2. Variable Selection

| Variable | Description and unit | Data type | Proposed role | Rationale / preparation needed |
|---|---|---|---|---|
| price | Nightly price (USD) | Numerical | Clustering input | Convert "$65.73" string to float; log-transform |
| (추가 예정) | | | | |

### Discussion
- **Clustering variables and the research question:** (작성 예정)
- **Candidate predictors for classification:** (작성 예정)
- **Excluded variables:** IDs, URLs, and 15 columns that are 100% missing
- **Preparation needs:** missing values, scaling, categorical encoding

> Note: Cluster labels will be the classification target and will not be used as input features.

## 3. Supplementary Dataset
- **Title / Provider / Link:** (작성 예정)
- **Relevant variables:** (작성 예정)
- **Contribution to the research question:** (작성 예정)
- **Connection to current data:** (작성 예정)

## Supporting Code
- (노트북 링크 예정)
