# Airbnb Listing Profiles in New York City

**CDS 303 – Final Project 1**

**Team Members:** Seohyun Hong, Seoyoung Yun, Sieun Woo


## 1. Project Overview

Our team wants to find different groups of Airbnb listings in New York City. We will group listings based on their nightly price, availability, and minimum-night requirements.

After clustering, we will use room type, borough, host listing count, and license status to understand the differences between the groups.

Our goal is to understand different types of Airbnb listings in New York City. The results may help Airbnb hosts compare their listings with similar properties.

### Research Question

**Can New York City Airbnb listings be grouped into distinct profiles based on nightly price, availability (30- and 365-day), and minimum-night requirements, and to what extent do room type, borough, host listing count, and license status explain these profiles?**

### Current Dataset

- **Dataset:** Inside Airbnb – New York City Detailed Listings
- **Date:** September 14, 2026
- **Provider:** Inside Airbnb
- **Source:** [Inside Airbnb](https://insideairbnb.com/get-the-data/)
- **File:** `listings.csv`

Our dataset includes information about Airbnb listings, such as price, availability, room type, location, and host information.

In Final Project 0, we used Python and Pandas to check our dataset.

The dataset has **29,918 rows and 90 columns**.

We also found **10,512 missing values in the price column**. Therefore, we need to clean the data before clustering.

---

## 2. Session 1 – Paper Summary

### Research Papers

**Paper 1: A Comprehensive Framework for Explainable Cluster Analysis (2024)**

The first paper explains how to group similar data and understand the results.

The researchers introduce four steps: data preprocessing, dimensionality reduction, clustering, and classification.

These steps help us prepare data, find groups, and understand what makes the groups different.

**Paper 2: Uncovering Student Profiles: An Explainable Cluster Analysis Approach to PISA 2022 (2024)**

The second paper uses the same method with student data.

The researchers studied 30,800 students in Spain and found seven student groups.

They used classification and SHAP to understand which characteristics were important for each group.

We want to use a similar method for our Airbnb project.

### Four-Step Framework

**Step 1. Data Preprocessing**

This step prepares the data for analysis.

We need to check missing values, unusual values, and data types.

For our project, we will change prices into numbers and check missing values before clustering.

**Step 2. Dimensionality Reduction**

This step helps reduce the number of variables while keeping useful information.

We learned about PCA in Demo 6. We plan to try PCA with our four clustering variables and check if it is helpful.

**Step 3. Clustering**

Clustering puts similar data into the same group.

We learned about K-means in Demo 7.

We plan to use K-means to group Airbnb listings. We will also use the Elbow Method and Silhouette Score to help choose the number of clusters.

**Step 4. Classification for Explainability**

This step helps us understand the groups created by clustering.

We will use the cluster labels as the target variable and other listing characteristics as input variables.

This can help us find which characteristics are important for explaining the groups.

### Why Do We Need to Interpret Clusters?

Clustering gives us group numbers, such as Cluster 0 and Cluster 1. However, these numbers do not tell us what each group means.

We need to compare the price, availability, and minimum-night requirements of each group.

Then, we can use other listing characteristics to understand the differences.

This will help us explain our results more clearly.

### How Can Classification Help Explain Clusters?

Classification can help us find which variables are useful for distinguishing the groups.

For our project, we will use room type, borough, host listing count, and license status as possible input variables.

The cluster labels will be the target variable, not an input variable.

Classification can help explain our clustering results, but it does not prove that the clusters are correct.

---

## 3. Session 1 – Variable Selection

We selected four variables for clustering and four main variables for classification.

We chose these variables based on our research question.

### Variable-Selection Table

| Variable | Description and Unit | Data Type | Proposed Role | Rationale / Preparation Needed |
|---|---|---|---|---|
| `price` | Nightly price (USD) | Numerical after conversion | Clustering Input | Helps group listings by price. We need to convert text into numbers and handle missing values. |
| `availability_30` | Available days in the next 30 days (days) | Numerical | Clustering Input | Shows short-term availability. Scaling is needed. |
| `availability_365` | Available days in the next 365 days (days) | Numerical | Clustering Input | Shows long-term availability. Scaling is needed. |
| `minimum_nights` | Minimum stay (nights) | Numerical | Clustering Input | Helps compare stay requirements. We need to check missing and unusual values. |
| `room_type` | Type of room | Categorical | Classification Input | Helps compare room types between groups. Dummy encoding is needed. |
| `neighbourhood_group_cleansed` | NYC borough | Categorical | Classification Input | Helps compare locations between groups. Dummy encoding is needed. |
| `calculated_host_listings_count` | Number of listings linked to a host (listings) | Numerical | Classification Input | Helps compare hosts with different numbers of listings. |
| `license` | Original license information | Text / Categorical | Used to create a new variable | We will check the values and create `license_status`. |
| `license_status` | Grouped license information | Derived Categorical | Classification Input | Helps compare license information between groups. |
| `id` | Listing ID | Identifier | Exclude / Integration | Not useful for clustering, but needed to connect datasets. |
| `bathrooms` | Number of bathrooms | Numerical | Exclude | All values are missing in our dataset. |
| `availability_60`, `availability_90` | Available days in 60 or 90 days | Numerical | Exclude Initially | They may have similar information to our selected availability variables. |

### Why We Selected the Clustering Variables

We selected `price`, `availability_30`, `availability_365`, and `minimum_nights`.

These variables are directly related to our research question.

Price shows how much a listing costs per night.

The two availability variables show how many days the listing is available in the short term and long term.

Minimum nights shows how long guests must stay.

We think these four variables can help us find different Airbnb listing groups.

However, the two availability variables may have similar information, so we will check their relationship before clustering.

### Candidate Predictors for Classification

We plan to use four main variables to explain the groups.

- **Room Type:** To check which room types are common in each group.
- **Borough:** To see whether groups are different by location.
- **Host Listing Count:** To compare hosts with different numbers of listings.
- **License Status:** To compare reported license information between groups.

These variables will help us understand the characteristics of each cluster.

### Variables to Exclude

We will exclude variables that are not useful for our analysis.

For example, `id` and `host_id` are identification numbers. They do not explain the characteristics of a listing.

However, we will keep `id` because we need it to connect our datasets.

We will also exclude variables with too many missing values or information that may be repeated in other variables.

### Data Preparation Needed

**Missing Values**

Our dataset has missing price values. We will check these values and decide how to handle them.

**Data Type Conversion**

The `price` column is stored as text. We need to change it into numerical values.

**Scaling**

Our clustering variables have different ranges. We plan to use StandardScaler so the variables can be compared more fairly.

**Categorical Encoding**

We will use dummy variables for categorical data, such as room type and borough.

**Unusual Values**

We will check very high prices and unusual minimum-night requirements before clustering.

**New Variables**

We plan to create `license_status` using the original `license` column. We will not assume that missing license information means a listing is unlicensed.

For the full project, we may also create new variables such as `price_per_guest`, `amenity_count`, and `calendar_available_rate`.

These variables have not been created yet.

---

## 4. Session 1 – Supplementary Dataset

### Inside Airbnb – New York City Calendar Data

- **Dataset Title:** New York City Detailed Calendar Data
- **Provider:** Inside Airbnb
- **Source:** [Inside Airbnb](https://insideairbnb.com/get-the-data/)
- **File:** `calendar.csv.gz`

### Relevant Variables

| Variable | Description |
|---|---|
| `listing_id` | Airbnb listing ID |
| `date` | Calendar date |
| `available` | Whether the listing is available on that date |
| `price` | Listed price for that date |

We will check these columns after loading the calendar dataset.

### Why We Selected This Dataset

We selected the Airbnb Calendar Data because our research question includes availability.

Our main Listings Data contains basic information about each listing.

The Calendar Data provides information about availability and prices for specific dates.

We can use this information to study availability in more detail and compare price changes across different dates.

This may help us understand the characteristics of each cluster.

### How We Can Connect the Datasets

The Listings Data has a column called `id`.

The Calendar Data has a column called `listing_id`.

We can use these two columns to connect the datasets.

One listing can have many calendar records, so we will first summarize the calendar data for each listing.

Then, we plan to merge it with the Listings Data.

### Important Note

An unavailable day does not always mean the listing was booked. The host may have blocked that date.

Therefore, we should not treat calendar availability as an exact booking rate.

For Session 1, we only selected the supplementary dataset and explained how we can use it.

We have not merged the datasets yet.

---

## 5. Future Project Updates

### Session 2 – Data Preprocessing

*To be updated.*

### Session 3 – Dimensionality Reduction

*To be updated.*

### Session 4 – Clustering

*To be updated.*

### Later Sessions – Classification and Interpretation

*To be updated.*

### Final Results and Conclusion

*To be updated.*

---

## 6. References

**Research Papers**

Alvarez-Garcia, M., Ibar-Alonso, R., & Arenas-Parra, M. (2024). A comprehensive framework for explainable cluster analysis. *Information Sciences, 663*, 120282.

https://doi.org/10.1016/j.ins.2024.120282

Alvarez-Garcia, M., Arenas-Parra, M., & Ibar-Alonso, R. (2024). Uncovering student profiles: An explainable cluster analysis approach to PISA 2022. *Computers & Education, 223*, 105166.

https://doi.org/10.1016/j.compedu.2024.105166

**Dataset Source**

Inside Airbnb. (2026). New York City Listings and Calendar Data.

https://insideairbnb.com/get-the-data/

**Course Materials**

- CDS 303 – Demo 6: Dimensionality Reduction (PCA)
- CDS 303 – Demo 7: Clustering
