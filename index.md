# CDS 303 Final Project: Explainable Cluster Analysis of New York City Airbnb Listings

**Team members:** Seohyun Hong, Sieun Woo, Seoyoung Yun

## Project Overview
We apply a four-step explainable cluster analysis framework (preprocessing → dimensionality reduction → clustering → classification for explainability) to New York City Airbnb listing data. Our goal is not only to group listings into profiles but also to explain *what characterizes* each profile, using a classifier trained on the resulting cluster labels.

## Research Question
> Can Airbnb listings in New York City be grouped into distinct profiles based on their prices, room types, and availability, and what characteristics explain these groups?

## Dataset
| Item | Details |
|---|---|
| Name | Inside Airbnb – New York City Listings |
| Snapshot | September 14, 2026 |
| Source | [insideairbnb.com/get-the-data](https://insideairbnb.com/get-the-data/) |
| File | `listings.csv.gz` (detailed listings) |
| Size | 29,918 listings × 90 variables |
| Unit of analysis | One Airbnb listing |

The dataset describes each listing's location (borough, neighbourhood, coordinates), property and room type, nightly price, availability over the next 30–365 days, minimum-stay rules, host information, and review activity and scores. Thirteen columns are entirely empty in this snapshot, and `price` is missing for about 35% of listings, so data preparation is a key part of the project.

## Project Updates
| Session | Contents | Link |
|---|---|---|
| Session 0 | Initial data exploration | [Notebook](notebooks/CDS303_Final_Project0_Airbnb.ipynb) |
| Session 1 | Paper summary, variable selection, supplementary dataset | [Session 1](Session1.md) |
| Session 2 | *(coming
