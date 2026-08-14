# 🎬 Netflix Content Analytics — Interactive Power BI Dashboard

An interactive **Power BI dashboard** for exploring Netflix's movie and TV show catalog through dynamic visualizations, interactive filters, and data-driven analysis.

The project focuses on **data cleaning, transformation, modeling, dynamic visualization, and interactive business intelligence** using Microsoft Power BI.

---

## 📊 Dashboard Preview

> Add your main dashboard screenshot here.

![Netflix Analytics Dashboard](Screenshots/dashboard_overview.png)

---

## 🚀 Project Overview

This project analyzes a Netflix titles dataset containing information about movies and TV shows available on the platform.

The dashboard allows users to interactively explore the catalog based on:

* 🎥 Content type — Movies vs TV Shows
* 📅 Release year
* ⭐ Content rating
* 🌍 Country
* 📈 Selected analytical dimension

Instead of creating completely static charts, the dashboard uses **Power BI Field Parameters and interactive slicers** to allow users to dynamically change what the visualizations analyze.

For example, the same analytical chart can switch between:

**Release Year → Rating → Country → Type**

without requiring separate charts for every dimension.

---

## 🎯 Objectives

The main objectives of this project are:

1. Clean and transform raw Netflix data using Power Query.
2. Identify and handle missing and inconsistent values.
3. Remove duplicate and invalid records where appropriate.
4. Build a structured Power BI data model.
5. Create interactive KPIs and visualizations.
6. Implement dynamic analysis using Field Parameters.
7. Enable cross-filtering through interactive slicers.
8. Analyze Netflix content across time, ratings, countries, and content types.
9. Present the results through a professional BI dashboard.

---

## 🛠️ Technologies Used

| Technology                     | Purpose                                 |
| ------------------------------ | --------------------------------------- |
| **Microsoft Power BI Desktop** | Dashboard development and visualization |
| **Power Query**                | Data cleaning and transformation        |
| **DAX**                        | Measures and calculations               |
| **Field Parameters**           | Dynamic chart dimensions                |
| **CSV**                        | Source dataset                          |

---

# 🗂️ Dataset

The dataset contains information about Netflix titles and includes fields such as:

| Column         | Description                                 |
| -------------- | ------------------------------------------- |
| `show_id`      | Unique identifier for each title            |
| `type`         | Movie or TV Show                            |
| `title`        | Title of the content                        |
| `director`     | Director(s)                                 |
| `cast`         | Cast members                                |
| `country`      | Country/countries associated with the title |
| `date_added`   | Date the title was added to Netflix         |
| `release_year` | Original release year                       |
| `rating`       | Content rating                              |
| `duration`     | Movie duration or number of TV show seasons |
| `listed_in`    | Genre/category information                  |
| `description`  | Description of the title                    |

---

# 🧹 Data Cleaning & Transformation

The raw dataset was processed using **Power Query** before being used for visualization.

### Cleaning operations included:

* Removed completely blank rows.
* Checked for duplicate records using `show_id`.
* Investigated data-type mismatches.
* Corrected inappropriate automatic data-type conversions.
* Kept legitimate missing values where information was unavailable.
* Removed invalid records where appropriate.
* Applied appropriate data types to numerical and textual fields.
* Trimmed unnecessary whitespace.
* Cleaned text values.
* Checked missing values in important fields.

### Important data-quality decision

Not every missing value was treated as an error.

For example, missing values in:

* `director`
* `cast`
* `country`
* `date_added`

can represent unavailable information rather than corrupted records.

Therefore, these values were retained when the underlying record was otherwise valid.

---

# 🌍 Country Data Transformation

The original `country` field can contain multiple countries in a single record.

For example:

```text
United States, India, Canada
```

For accurate country-level analysis, the country information was transformed into a separate country table.

This allows a title associated with multiple countries to contribute to each relevant country rather than being treated as one combined category.

### Example

Original:

```text
show_id | country
s1      | United States, India
```

Transformed:

```text
show_id | country
s1      | United States
s1      | India
```

This provides more meaningful country-level analysis.

---

# 📐 Data Model

The project uses the main Netflix titles table along with a transformed country table.

Conceptually:

```text
┌─────────────────────────┐
│     Netflix Titles      │
│                         │
│ show_id                 │
│ title                   │
│ type                    │
│ release_year            │
│ rating                  │
│ date_added              │
│ ...                     │
└────────────┬────────────┘
             │
             │ show_id
             │ 1 : *
             ▼
┌─────────────────────────┐
│   Netflix Countries     │
│                         │
│ show_id                 │
│ country                 │
└─────────────────────────┘
```

This structure enables country-level analysis while maintaining the original title-level information.

---

# 📈 Dashboard Features

## 1. KPI Cards

The dashboard provides high-level metrics such as:

* **Total Titles**
* **Total Movies**
* **Total TV Shows**
* **Country Entries**

These metrics respond to applicable filters.

---

## 2. Dynamic Analysis Chart

One of the main features of the dashboard is a dynamic analytical chart powered by a **Power BI Field Parameter**.

Users can select:

```text
Release Year
Rating
Country
Type
```

The same chart then dynamically changes its analytical dimension.

### Example

Selecting:

**Release Year**

produces:

> Number of Netflix titles by release year

Selecting:

**Rating**

changes the same visualization to:

> Number of Netflix titles by rating

Selecting:

**Country**

changes it to:

> Number of Netflix titles by country

This reduces the need for multiple separate visualizations and provides a more interactive analytical experience.

---

## 3. Content Type Filtering

Users can filter the dashboard by:

* Movie
* TV Show

The visualizations update according to the selected content type.

---

## 4. Release Year Filtering

A release-year slicer allows users to focus on specific periods.

For example:

```text
2015 ─────────────── 2021
```

Selecting a particular range updates the relevant dashboard visuals.

---

## 5. Rating Analysis

The dashboard provides an analysis of Netflix titles across content ratings such as:

* TV-MA
* TV-14
* PG-13
* R
* PG
* TV-PG
* TV-Y
* TV-G
* and others present in the dataset.

---

## 6. Country Analysis

The dashboard provides country-level analysis using the transformed country table.

A Top-N country visualization can be used to identify countries associated with the highest number of Netflix titles.

---

# 🧮 DAX Measures

The dashboard uses DAX measures for key calculations.

### Total Movies

```DAX
Total Movies =
CALCULATE(
    DISTINCTCOUNT('netflix_titles (3)'[show_id]),
    'netflix_titles (3)'[type] = "Movie"
)
```

### Total TV Shows

```DAX
Total TV Shows =
CALCULATE(
    DISTINCTCOUNT('netflix_titles (3)'[show_id]),
    'netflix_titles (3)'[type] = "TV Show"
)
```

### Country Entries

```DAX
Total Countries =
DISTINCTCOUNT('netflix_titles (3)'[country])
```

> `Country Entries` represents distinct country-value combinations in the original dataset. It should not be interpreted as the exact number of individual countries.

---

# 🔄 Interactivity

The dashboard supports interactive filtering through slicers.

The main interaction flow is:

```text
User selects filter
       ↓
Power BI filter context changes
       ↓
Measures recalculate
       ↓
Charts update
       ↓
Dashboard reflects the selected segment
```

For example:

```text
Type = Movie
        +
Release Year = 2015–2021
        +
Analyze By = Rating
        ↓
Dashboard shows ratings specifically for
the selected movie/time segment.
```

---

# 📊 Key Analytical Questions

The dashboard can be used to investigate questions such as:

### Content

* How many Movies and TV Shows are present?
* How does the catalog composition differ between Movies and TV Shows?
* Which content type dominates the dataset?

### Time

* How has Netflix's content catalog changed over the years?
* Which release years contain the highest number of titles?
* How does the distribution change when filtering Movies vs TV Shows?

### Ratings

* Which content ratings are most common?
* How does the rating distribution differ between Movies and TV Shows?
* Which ratings dominate within a selected period?

### Geography

* Which countries are associated with the highest number of titles?
* How does country distribution change when filtering by content type?
* Which countries contribute significantly to the Netflix catalog?

---

# 📁 Project Structure

```text
Netflix-PowerBI-Dashboard/
│
├── README.md
│
├── PowerBI/
│   └── Netflix_Analytics.pbix
│
├── Screenshots/
│   ├── dashboard_overview.png
│   ├── dashboard_rating_analysis.png
│   ├── dashboard_country_analysis.png
│   └── dashboard_tv_shows.png
│
└── Documentation/
    └── Data_Cleaning.md
```

If redistribution of the source dataset is permitted, the repository can additionally contain:

```text
Dataset/
└── netflix_titles.csv
```

Otherwise, the dataset source should be referenced in this README instead of redistributing the file.

---

# 🖥️ How to Use

### 1. Clone the repository

```bash
git clone <your-repository-url>
```

### 2. Open the Power BI file

Navigate to:

```text
PowerBI/Netflix_Analytics.pbix
```

Open it using **Microsoft Power BI Desktop**.

### 3. Interact with the dashboard

Use the available slicers to explore:

* Content type
* Release year
* Rating
* Country
* Dynamic analytical dimensions

### Requirements

* Windows
* Microsoft Power BI Desktop
* Access to the dataset if the `.pbix` requires an external source refresh

---

# 📸 Dashboard Screenshots

## Main Dashboard

![Dashboard Overview](Screenshots/dashboard_overview.png)

## Rating Analysis

![Rating Analysis](Screenshots/dashboard_rating_analysis.png)

## Country Analysis

![Country Analysis](Screenshots/dashboard_country_analysis.png)

## TV Show Analysis

![TV Show Analysis](Screenshots/dashboard_tv_shows.png)

---

# 💡 Key Insights

The dashboard enables users to identify patterns such as:

* The relative proportion of Movies and TV Shows in the dataset.
* The concentration of titles across different release years.
* The dominance of particular content ratings.
* Geographic concentration of Netflix titles.
* Differences between Movie and TV Show distributions.

> Exact numerical insights should be added here after the final dashboard is completed and the values have been verified.

---

# 🔮 Future Improvements

Potential improvements include:

* Add a dedicated calendar/date dimension.
* Add a true individual-country dimension.
* Add genre-level analysis.
* Add director and cast analysis.
* Add movie-duration analysis.
* Add TV-show season analysis.
* Add year-over-year growth calculations.
* Add advanced DAX measures.
* Add drill-through pages for individual titles.
* Add tooltip pages for richer visual exploration.
* Add navigation buttons between dashboard pages.
* Publish the dashboard to Power BI Service for online sharing.
* Add automated data-refresh functionality.

---

# 🧠 Skills Demonstrated

This project demonstrates practical experience with:

* **Data Cleaning**
* **Data Transformation**
* **Power Query**
* **Data Modeling**
* **DAX**
* **Power BI**
* **Interactive Dashboards**
* **Field Parameters**
* **Dynamic Visualizations**
* **Slicers & Cross-filtering**
* **Data Quality Handling**
* **Analytical Storytelling**

---

# 📌 Project Status

**Status:** Completed / Portfolio Project

The dashboard is designed as an interactive analytical application rather than a collection of static charts.

---

# 👤 Author

**Amish Awasthi**

B.Tech — Information Technology

Interested in:

* Data Analytics
* Machine Learning
* Artificial Intelligence
* Software Development
* Data Visualization

---

## ⭐ If you found this project useful

Feel free to explore the `.pbix` file, examine the Power Query transformations and DAX measures, and experiment with the interactive dashboard.

---
