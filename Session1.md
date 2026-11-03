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
- **A label is not an insight.** A clustering algorithm only says that observation A belongs to "Cluster 3". The method article stresses that the last and most important step is to understand the clusters and give them meaningful names, which requires putting the result into the context of the study.
- **The inputs to clustering are abstract.** After dimensionality reduction, clusters are formed in a space of derived components (e.g., 16 components instead of 74 variables in the PISA study). These components have no natural units, so a cluster's position in that space cannot be explained directly to decision-makers.
- **Clusters are used for decisions.** In the application article, the purpose was to design targeted education policies. A profile can only guide action if we know who is in it and what sets it apart. The same is true for our Airbnb profiles.
- **Interpretation checks plausibility.** Describing clusters with domain knowledge helps reveal whether a cluster reflects a real pattern or an artifact of preprocessing or algorithm choice.


### 1.3 How Classification Characterizes Clusters
Once cluster labels exist, the unsupervised problem can be turned into a supervised one: *"Given an observation's original characteristics, which cluster does it belong to?"*
1. **Translating back to original variables.** The classifier is trained on the original, interpretable variables rather than on the abstract components, so the explanation is expressed in real units.
2. **Global explanation.** The average absolute SHAP value of each feature ranks the variables that best separate all clusters (e.g., in the PISA study, socio-economic status had by far the highest importance).
3. **Local, per-cluster explanation.** SHAP beeswarm plots for each cluster show which variables push an observation toward that cluster and in which direction (high vs. low values). This is how the authors labeled each cluster.
4. **Revealing what cluster means miss.** Comparing cluster means shows how clusters differ on average, but not which differences actually *separate* them. In the method article, some variables with large mean differences contributed little to classification, whereas a variable weakly related to the clustering components turned out to be a strong predictor for one cluster.
5. **Credibility and reuse.** High classification accuracy shows that the clusters are separable and consistently defined, and the trained model can assign new observations to existing profiles.

> **Characterization vs. validation.** In both papers, the classifier's predictors are the same original variables that were summarized into the clustering components. High accuracy therefore shows that the classifier can *reproduce and describe* the clustering result, not that the clusters are independently "true". In our project, the classifier will mainly use variables that were **not** clustering inputs (location, size, host, and review variables; see Section 2). This means it will also show how the price-room-availability profiles relate to other listing characteristics. The cluster labels are used **only** as the target and never as input features.

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
