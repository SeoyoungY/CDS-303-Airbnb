[← Back to Home](index.md)

# Session 1: Framework Review, Variable Selection, and Supplementary Data

## 1. Paper Summary

**Papers reviewed**
- *Method article:* Alvarez-Garcia, M., Ibar-Alonso, R., & Arenas-Parra, M. (2024). A comprehensive framework for explainable cluster analysis. *Information Sciences, 663*, 120282. https://doi.org/10.1016/j.ins.2024.120282
- *Application article:* Alvarez-Garcia, M., Arenas-Parra, M., & Ibar-Alonso, R. (2024). Uncovering student profiles. An explainable cluster analysis approach to PISA 2022. *Computers & Education, 223*, 105166. https://doi.org/10.1016/j.compedu.2024.105166

**The problem the papers address.** Clustering is a popular way to find structure in large datasets, but it has two weak points. First, its results are only as good as the data: real-world data contain missing values, outliers, many variables, and a mix of numerical and categorical types. Second, because clustering is unsupervised, it produces groups without telling us what the groups mean. Earlier explainable-clustering methods focused only on the second problem and were tied to a specific algorithm. The method article proposes one end-to-end workflow that handles both problems and lets the analyst swap in different algorithms. The authors also released it as an open-source Python package, **Clust-learn**.

### 1.1 The Four-Step Framework
| Step | Purpose (in our words) | What the method article does |
|---|---|---|
| **1. Data preprocessing** | Make the data complete and trustworthy before measuring similarity between observations. | Drops variables and records with too many missing values; imputes the rest by using strongly related variable pairs (regression or hot-deck), then groups of related variables and KNN-based hot-deck; removes multivariate outliers with **Isolation Forest**. |
| **2. Dimensionality reduction** | Reduce many correlated variables to a few components so that (a) distances are not distorted by the curse of dimensionality and (b) humans can still understand the inputs. | Uses **Sparse PCA** for numerical variables (each component depends on only a few original variables, so it is easier to name) and **Multiple Correspondence Analysis (MCA)** for categorical variables. The number of components is chosen with the elbow method, and each component is named after the original variables most correlated with it. |
| **3. Clustering** | Group similar observations into profiles. | There is no universally best algorithm, so several (k-means, agglomerative, Gaussian mixture) are compared using validity metrics such as within-cluster sum of squares, Davies-Bouldin, silhouette, and Calinski-Harabasz. The number of clusters is chosen with the elbow method. Clusters are first described by comparing each cluster's mean on each component with the overall mean. |
| **4. Classification for explainability** | Explain what distinguishes each cluster, and create a tool that can assign new observations to a profile. | Uses the **cluster labels as the target** and the **original variables as predictors**. Pipeline: 80/20 train-test split → feature selection (remove redundant variables, random forest + recursive feature elimination) → grid-search hyperparameter tuning with cross-validation → **XGBoost** classifier evaluated with accuracy, precision, recall, AUC, and the confusion matrix → **SHAP values** for global and per-cluster explanations. |

**How the application article used it.** The same authors applied the framework to 30,800 Spanish students in PISA 2022 with 74 background variables. They obtained 16 derived variables, **7 student clusters** (weighted k-means), and an XGBoost classifier with **0.864 test accuracy**. SHAP values showed that socio-economic status and home ICT access/use were the strongest differentiators, and the authors used the top predictors of each cluster to give the clusters names (e.g., "Students with limited access to ICT", "Emotionally vulnerable students"). Finally, they compared the clusters on variables that were **not** used to form them (gender, PISA test scores, educational expectations) to derive policy insights.

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
