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

### 1.4 Implications for Our Airbnb Project
We follow the same four steps, using the tools covered in the course demos. The papers' more specialized tools are noted for comparison.

| Framework step | Papers | Our plan (course tools) |
|---|---|---|
| Preprocessing | Model-based + KNN hot-deck imputation; Isolation Forest | Price text → number; drop rows without price; remove top 1% of prices; log transforms; `StandardScaler` (Demo 1: effect of normalization on distance) |
| Dimensionality reduction | Sparse PCA (numerical) + MCA (categorical) | `PCA` with explained-variance plot, loadings, and biplot (Demo 6). With only five clustering inputs, PCA mainly supports visualization and component interpretation |
| Clustering | Compare k-means, agglomerative, GMM; elbow method | `KMeans` and `AgglomerativeClustering` (Ward) with dendrogram; choose *k* using silhouette score and the elbow (`KElbowVisualizer`); profile clusters with `groupby('cluster').mean()` (Demo 7) |
| Classification | XGBoost + SHAP | `DecisionTreeClassifier` with `plot_tree` for readable rules (Demo 2); `RandomForestClassifier` with impurity and permutation importance (Demo 3); SHAP bar and beeswarm plots per cluster (Demo 4) |

---

## 2. Variable Selection

**Data snapshot (NYC, 29,918 listings).**
| Item | Value |
|---|---|
| Boroughs | Manhattan 13,681 · Brooklyn 10,290 · Queens 4,637 · Bronx 989 · Staten Island 321 |
| Room types | Entire home/apt 16,410 · Private room 12,853 · Hotel room 463 · Shared room 192 |
| Price (n = 19,406) | Median $182 · IQR $104–$337 · 99th percentile $2,000 · Max $31,538 |
| Minimum nights ≥ 30 | 80.7% of all listings (73.7% of listings with a price) |
| Neighbourhoods / property types | 223 / 68 categories |

**Design logic.** Our research question defines the clustering space: **price, room type, and availability**. All other informative variables are reserved for the classification stage, where they explain the clusters.

### 2.1 Clustering Inputs
| Variable | Description and unit | Data type | Proposed role | Rationale / preparation needed |
|---|---|---|---|---|
| `price` | Nightly price (USD) | Numerical | Clustering input | Core variable of the RQ. Stored as text (`"$150.00"`) → strip `$` and `,`, convert to float. Missing for 10,512 listings (35.1%), and **not at random**: 99.9% of listings with zero availability in the next year have no price, versus 15.3% of the others. We drop these rows and define our scope as *actively offered listings*. Highly right-skewed (median $182, max $31,538) → remove prices above the 99th percentile ($2,000) and log-transform; 19,212 listings remain. |
| `room_type` | Entire home/apt, Private room, Shared room, Hotel room | Categorical | Clustering input | Core variable of the RQ. One-hot encode. Among priced listings: entire home 10,008, private room 8,658, hotel room 393, shared room 153. The two rare types may be merged or analyzed separately. |
| `availability_30` | Days available in the next 30 days (0–30) | Numerical | Clustering input | Short-term availability; captures how actively a listing is open for booking. Kept as the representative of the 30/60/90-day and end-of-year windows (Spearman 0.89–0.94). Standardize. |
| `availability_365` | Days available in the next 365 days (0–365) | Numerical | Clustering input | Long-term availability; separates full-time rentals from occasional ones. Only moderately related to `availability_30` (Spearman 0.65), so it adds distinct information. Standardize. |
| `minimum_nights` | Minimum stay required (nights) | Numerical | Clustering input | Availability *policy*. Among priced listings the median is exactly 30 nights and 73.7% require 30+ nights (consistent with NYC short-term rental rules), while the lowest quarter require 4 nights or fewer. The distribution is therefore split rather than continuous → encode as a **monthly-stay flag (≥ 30 nights)**, or cap at 60 and log-transform. 8 missing → drop. |

### 2.2 Classification Inputs (Candidate Predictors)
| Variable | Description and unit | Data type | Proposed role | Rationale / preparation needed |
|---|---|---|---|---|
| `neighbourhood_group_cleansed` | Borough (5 levels) | Categorical | Classification input | Location is a likely driver of price/availability profiles. One-hot encode. |
| `latitude`, `longitude` | Coordinates (degrees) | Numerical | Classification input | Fine-grained location; also used to join the supplementary dataset. |
| `property_type` | e.g., Entire rental unit, Private room in home | Categorical | Classification input | 68 levels → group rare types into "Other", then one-hot encode. |
| `accommodates` | Maximum number of guests | Numerical | Classification input | Listing size; explains price differences. |
| `bedrooms` | Number of bedrooms | Numerical | Classification input | Listing size. 39 missing → median impute. |
| `beds` | Number of beds | Numerical | Classification input | Listing size. 1,154 missing → median impute. |
| `bathrooms_text` | e.g., "1.5 shared baths" | Categorical (text) | Classification input | `bathrooms` is 100% empty, so extract number of baths and a *shared* flag from this text. |
| `amenities` | List of amenities | Text (list) | Classification input | Convert to `amenities_count`, optionally flags for key amenities (e.g., kitchen, washer). |
| `number_of_reviews` | Total reviews (count) | Numerical | Classification input | Popularity/track record. Log transform. |
| `reviews_per_month` | Average reviews per month | Numerical | Classification input | Booking activity. Missing (8,114) means no reviews → fill with 0. |
| `review_scores_rating` | Overall rating (0–5) | Numerical | Classification input | Guest satisfaction. Missing when no reviews → add `has_reviews` flag and impute. |
| `review_scores_location` | Location rating (0–5) | Numerical | Classification input | Perceived location quality. Same missing treatment. |
| `review_scores_value` | Value rating (0–5) | Numerical | Classification input | Perceived value for money; relevant to price profiles. |
| `host_is_superhost` | Superhost status (t/f) | Categorical (binary) | Classification input | Host quality. Encode as 0/1. |
| `calculated_host_listings_count` | Listings the host has in NYC | Numerical | Classification input | Separates individual hosts from commercial operators (max 889). Representative of host-count variables (Spearman 0.96 with `host_listings_count`). Log transform. |
| `hosts_time_as_host_years` | Years as host | Numerical | Classification input | Host experience. 2 missing → impute. |
| `license` | Registration number or "Exempt" | Text | Classification input | Recode as `license_status` with three levels: registered (`OSE-STRREG-…`, 2,231), exempt (2,877), none (24,810). Directly relevant to NYC short-term rental regulation. |

### 2.3 Contextual Variables (Interpretation Only)
| Variable | Description and unit | Data type | Proposed role | Rationale / preparation needed |
|---|---|---|---|---|
| `neighbourhood_cleansed` | Neighbourhood name (223 levels) | Categorical | Contextual variable | Too many levels for modeling; used for maps and cluster descriptions. |
| `estimated_occupancy_l365d` | Estimated nights booked in last 12 months | Numerical | Contextual variable | Describes cluster performance; not used as a predictor to keep the explanation focused on listing traits. |
| `estimated_revenue_l365d` | Estimated revenue in last 12 months (USD) | Numerical | Contextual variable | Derived from price × occupancy → would leak price information; used only to describe clusters. |
| `last_review` | Date of most recent review | Date | Contextual variable | Convert to "days since last review" to identify inactive listings. |

### 2.4 Excluded Variables
| Variable(s) | Description | Data type | Proposed role | Rationale |
|---|---|---|---|---|
| `id`, `listing_url`, `host_id`, `host_url`, `host_profile_id`, `host_profile_url`, `picture_url`, `host_picture_url`, `scrape_id` | Identifiers and URLs | Identifier | Exclude | No analytical meaning. |
| `name`, `description`, `host_name`, `host_about`, `host_location` | Free text | Text | Exclude | Out of scope (would need NLP); `host_name` is personal data. |
| `neighborhood_overview`, `host_since`, `host_response_time`, `host_response_rate`, `host_acceptance_rate`, `host_thumbnail_url`, `host_neighbourhood`, `host_total_listings_count`, `host_verifications`, `neighbourhood`, `bathrooms`, `calendar_updated`, `instant_bookable` | Various | — | Exclude | **100% missing** in this snapshot (13 columns). |
| `availability_60`, `availability_90`, `availability_eoy` | Availability over other windows | Numerical | Exclude | Redundant with `availability_30` (Spearman 0.89–0.94). |
| `maximum_nights`, `minimum_minimum_nights`, `maximum_minimum_nights`, `minimum_maximum_nights`, `maximum_maximum_nights`, `minimum_nights_avg_ntm`, `maximum_nights_avg_ntm` | Variants of stay rules | Numerical | Exclude | Minimum-night variants are redundant with `minimum_nights` (Spearman 0.97–1.00); maximum nights is mostly a default value (1,125 nights for 11,661 listings). |
| `price_quote_checkin_date`, `price_quote_checkout_date`, `price_quote_total_price`, `price_quote_price_per_night`, `price_quote_raw` | Price quote details | Mixed | Exclude | Redundant with `price` (`price_quote_price_per_night` correlates 1.00 with price). |
| `review_scores_accuracy`, `_cleanliness`, `_checkin`, `_communication` | Review sub-scores | Numerical | Exclude | Correlated with `review_scores_rating` (Spearman 0.61–0.74). `review_scores_location` (0.48) and `review_scores_value` are kept because they carry distinct meaning. |
| `number_of_reviews_ltm`, `number_of_reviews_l30d`, `number_of_reviews_ly`, `first_review` | Review counts by period | Mixed | Exclude | Redundant with `number_of_reviews` and `reviews_per_month`. |
| `host_listings_count`, `calculated_host_listings_count_entire_homes`, `_private_rooms`, `_shared_rooms` | Host listing counts | Numerical | Exclude | Redundant with `calculated_host_listings_count`. |
| `hosts_time_as_user_years`, `hosts_time_as_user_months`, `hosts_time_as_host_months` | Account age | Numerical | Exclude | Redundant with `hosts_time_as_host_years`. |
| `host_has_profile_pic`, `has_availability`, `host_identity_verified` | Binary flags | Categorical | Exclude | Near-constant (96.5%, 96.4%, and 98.7% in one category); little information. |
| `source`, `last_scraped`, `calendar_last_scraped` | Scraping metadata | Mixed | Exclude | Describe data collection, not listings. |

### 2.5 Discussion
- **Clustering variables and the research question.** The five clustering inputs map directly onto the three dimensions in our research question: *price* (`price`), *room type* (`room_type`), and *availability* (`availability_30`, `availability_365`, `minimum_nights`). Clustering in this space should reveal listing profiles such as budget private rooms, full-time entire-home rentals, or monthly-stay units.
- **Candidate predictors for explaining clusters.** Location (borough, coordinates), size (accommodates, bedrooms, beds, baths), amenities, review activity and scores, host characteristics (superhost, number of listings, experience), and license status (registered / exempt / none). Because these are mostly **different** from the clustering inputs, the classifier shows which listing characteristics are associated with each profile.
- **Excluded variables.** Identifiers and URLs, free text, 13 completely empty columns, and redundant variants of availability, stay rules, price quotes, review scores, and host counts.
- **Preparation needs.**
  - *Missing values:* drop rows with missing `price` (35%; mostly listings with no availability, so results describe actively offered listings); fill `reviews_per_month` with 0; add a `has_reviews` flag and impute review scores; median-impute bedrooms/beds.
  - *Type conversion:* `price` text → float; `bathrooms_text` → number + shared flag; `amenities` → count; `license` → three-level status; `minimum_nights` → monthly-stay flag.
  - *Scaling and skew:* log-transform `price` (after removing the top 1%), review counts, and host listing counts (skewness 4–20); standardize all numeric clustering inputs.
  - *Categorical encoding:* one-hot encode `room_type`, borough, and grouped `property_type`; binary-encode t/f flags.
- **Leakage rule.** Cluster labels are the classification target only. Variables derived from price (e.g., `estimated_revenue_l365d`) are kept out of the classifier.

---

## 3. Supplementary Datasets

### 3.1 MTA Subway Stations and Complexes
- **Provider:** Metropolitan Transportation Authority (MTA), via NY State Open Data
- **Link:** [catalog.data.gov – MTA Subway Stations and Complexes](https://catalog.data.gov/dataset/mta-subway-stations-and-complexes)
- **Relevant variables:** station name, borough, latitude, longitude, lines served
- **Contribution:** Transit access is a plausible explanation for price and availability differences. We can compute each listing's **distance to the nearest subway station** and the **number of stations within 500 m**, then use these as additional classification predictors or to describe clusters.
- **Connection to our data:** Spatial join using `latitude` and `longitude`.

### 3.2 NYPD Complaint Data (Current Year to Date)
- **Provider:** New York City Police Department, via NYC Open Data
- **Link:** [catalog.data.gov – NYPD Complaint Data Current (Year To Date)](https://catalog.data.gov/dataset/nypd-complaint-data-current-year-to-date)
- **Relevant variables:** offense category, borough, precinct, latitude, longitude, date
- **Contribution:** Neighbourhood safety may relate to price levels and guest location ratings. Aggregated complaint counts near each listing can help interpret clusters.
- **Connection to our data:** Count complaints within a radius of each listing (coordinates), or aggregate by borough (`neighbourhood_group_cleansed`). Restricting to the same 2026 period aligns it with our September 2026 snapshot.

*Data integration is not performed in this session.*

---

## Supporting Code
- [Session 0 – Initial data exploration (notebook)](notebooks/CDS303_Final_Project0_Airbnb.ipynb)
- [Session 1 – Variable screening (notebook)](notebooks/CDS303_Session1_Variable_Screening.ipynb): missing values, price missingness, distributions, redundancy correlations, and encoding previews that support the table above.

*Data file:* `listings.csv.gz` for New York City (14 September 2026) from [Inside Airbnb](https://insideairbnb.com/get-the-data/). The file (~78 MB) is not stored in this repository.
