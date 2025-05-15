# Final-Project-Statistical-Modelling-with-Python

## Project/Goals
(fill in your description and goals here)

The goal of this project is to investigate whether the number of bikes at a given bike-sharing station is related to the surrounding environment — specifically, nearby restaurants, bars, and other points of interest (POIs).

To explore this relationship, I:
- Pulled live station data using the **CityBikes API**
- Enriched that data with POI information from **Yelp** and **Foursquare APIs**
- Cleaned, joined, and stored the data in a structured **SQLite3 database**
- Performed **exploratory data analysis (EDA)** and built a **linear and logistic regression model**
- Assessed whether POI features could meaningfully predict station characteristics like capacity

---

## Process
### (your step 1)
### (your step 2)

### Part 1: Retrieving Station Data
I began by querying the CityBikes API to retrieve real-time data on bike stations in our chosen city (Abu Dhabi and Acces Velo Saguenay initially, then Montreal and Acces Velo Saguenay due to better data availability). Each station included attributes like `latitude`, `longitude`, `free_bikes`, and `empty_slots`.

I parsed the JSON, saved the response into a Pandas DataFrame, and exported it to CSV for reuse across notebooks.

### Part 2: Enriching with POI Data
I queried both the Foursquare and Yelp APIs using the latitude/longitude of each station. For each station, I retrieved:
- Total number of venues nearby (within 1km)
- Categories (e.g., restaurants, bars)
- Ratings and review counts (Yelp)
- Distance to closest venue

I aggregated these POIs to the station level and created summary tables, including:
- `total_yelp_venues`
- `avg_yelp_rating`
- `closest_yelp_distance`
- `total_fsq_venues`
- `closest_fsq_distance`

### Part 3: Joining and Storing the Data
I merged the bike station data with the Yelp and Foursquare summaries into a clean, station-level dataset.

I designed a normalized SQLite database with three core tables:
- `bike_stations`
- `yelp_summary`
- `fsq_summary`

This structure made it easy to rejoin or query subsets of the data without redundancy.

I also performed exploratory data visualizations (scatter plots, histograms, and geospatial density plots) to explore potential patterns between station size and POI density.

### Part 4: Modeling
I built two models:
1. **Linear regression** to predict `slots` (station capacity) using POI features.
2. **Logistic regression** to classify stations as high-capacity (`slots > 10`) or not.

I found that some features (like `avg_yelp_rating`) had a statistically significant impact on station size, while others (like Foursquare distances) were less relevant.

I interpreted coefficients, validated statistical significance, and discussed model strengths and weaknesses.

---

## Results
(fill in what you found about the comparative quality of API coverage in your chosen area and the results of your model.)

- Yelp had **better POI coverage** for our selected city than Foursquare — more venues per station, more detailed ratings, and stronger correlations with station size.
- The **linear model** had low explanatory power (R-square ~ 0.03), suggesting that station capacity is driven by more than just nearby POIs.
- The **logistic regression** performed better (Pseudo R-squred ~ 0.43). It revealed that:
  - Higher **average Yelp ratings** correlated with high-capacity stations
  - Surprisingly, **fewer Yelp venues** correlated with higher-capacity stations — possibly reflecting that large stations are located in less dense but more open areas like campuses or transit hubs.

---

## Challenges 
(discuss challenges you faced in the project)

- **CityBikes API coverage** was limited for some cities. Initial choice (Abu Dhabi) returned many stations with no corresponding POIs in Yelp.
- **Yelp API limits** (300 calls/day) forced careful testing and working with a limited subset of stations.
- Inconsistent or missing data required frequent `dropna()` operations to ensure clean model inputs.
- Columns had mismatched names (e.g., `name` vs `station`), leading to broken joins until identified and corrected.
- Ensuring aligned indices between `X` and `y` for modeling took troubleshooting due to `ValueError` from statsmodels.

---


## Future Goals
(what would you do if you had more time?)

Further work proposed on this project include:
- Incorporating **temporal data** (e.g., time of day, day of week) since CityBikes data is dynamic and availability changes hourly.
- Integrate **weather conditions** as a feature — a strong potential influence on bike usage and station size.
- Expand the analysis to multiple cities to see if trends hold across urban vs. suburban zones.
