# 🎬 Netflix Dataset: Data Cleaning & Exploratory Analysis

## 📖 Project Overview
This project focuses on cleaning, preprocessing, and analyzing the Netflix Movies and TV Shows dataset (sourced from Kaggle). The primary objective is to transform a raw, messy dataset into a reliable foundation for business intelligence. 

The ultimate goal of this project is to build an interactive Power BI dashboard that uncovers trends in content production, geographical distribution, and genre popularity over time.

[You can find the original Dataset and Instructions clicking this link](https://www.kaggle.com/datasets/shivamb/netflix-shows)

## 🧹 Data Cleaning & Preprocessing (The "Why" and "How")
Data cleaning is rarely just running `.dropna()`. This phase required a deep understanding of the dataset's context to avoid biasing the analysis. Here is the thought process behind handling the major data quality issues:

### 1. Handling Missing Categorical Data (Director, Cast, Country)
*   **The Problem:** The `director` column was missing ~30% of its values, while `cast` and `country` were missing roughly 10% each.
*   **The Decision:** Filled missing values with `'Unknown'`.
*   **The Rationale:** 
    *   **Why not drop rows?** Dropping 30% of the dataset would heavily bias the analysis (TV shows and stand-up specials often lack a single "director"). 
    *   **Why not use Forward/Backward fill (`ffill`/`bfill`)?** This is a time-series technique. There is no logical connection between the director of Row 5 and Row 6. Using it would corrupt the data.
    *   **Why not fill with the mode?** Inventing a director (e.g., assigning a random kids' movie to a famous horror director) destroys analytical integrity. `'Unknown'` preserves the truth.

### 2. Rescuing Shifted Data (Duration & Rating)
*   **The Problem:** The `duration` column had missing values (0.03%). However, upon inspection, the `rating` column contained values like `'74 min'`.
*   **The Decision:** Moved the duration data from the `rating` column back to the `duration` column, and filled the now-empty `rating` cells with `'UR'` (Unrated).
*   **The Rationale:** This was a classic column-shifting error in the raw data. By using a boolean mask and `Series.str.contains()`, I rescued valid duration data. Instead of dropping the row and losing that duration, I assigned a domain-accurate placeholder (`'UR'`) for the rating, maximizing data retention.

### 3. Standardizing Dates (`date_added`)
*   **The Problem:** The `date_added` column contained leading/trailing whitespaces and was stored as a string.
*   **The Decision:** Stripped whitespaces, parsed to datetime using `errors='coerce'`, and dropped the remaining `NaT` (Not a Time) values.
*   **The Rationale:** `errors='coerce'` prevents the script from crashing on unparseable strings. Because the missing dates were less than 0.2% of the dataset, dropping them was statistically safe and necessary to enable time-series analysis (e.g., content added per year).

## 🧠 Key Learnings & Pandas Techniques Used
*   **`Series.str.contains()`**: Learned how to use regex-like patterns (`'min'`) to identify misplaced data across columns.
*   **Boolean Masking & `.loc`**: Used conditional filtering to safely reassign data between specific rows and columns without affecting the rest of the DataFrame.
*   **Strategic `fillna()`**: Differentiated between filling categorical metadata (`'Unknown'`) and handling core metrics. 
*   **`pd.to_datetime(..., errors='coerce')`**: Learned the best practice for converting messy strings to datetime objects safely.

---

## 🚀 What's Next: Power BI Visualization
With the dataset now clean and validated, the next phase is to import this data into **Power BI** to build an interactive dashboard. 

**Planned Analytical Questions for the Dashboard:**
1.  **Top Creators:** Which directors have the highest volume of content on the platform?
2.  **Global Reach:** Which countries are the most prolific producers of Netflix content? (Visualized via a map or a bar chart).
3.  **Content Strategy Shift:** How has the ratio of TV Shows vs. Movies added to Netflix changed over the last decade? (Line chart showing the trend).

## 🛠️ Tools Used
*   **Python (Pandas)**: Data cleaning, preprocessing, and feature engineering.
*   **Jupyter Notebook**: Development environment.
*   *(Coming Soon)* **Power BI**: Data visualization and dashboarding.
