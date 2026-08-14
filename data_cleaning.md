# 🧹 Netflix Dataset — Data Cleaning & Transformation

This document describes the data-cleaning and transformation process performed on the Netflix titles dataset before building the Power BI dashboard.

The cleaning process was performed using **Microsoft Power Query** within Power BI Desktop.

---

## 1. Objective

The raw Netflix dataset contains information about movies and TV shows, including titles, directors, cast, countries, dates, ratings, genres, and descriptions.

Before creating visualizations, the dataset was cleaned and transformed to:

* Remove completely empty records.
* Identify duplicate records.
* Correct inappropriate data types.
* Detect mismatched values.
* Handle missing values appropriately.
* Standardize text fields.
* Prepare country information for accurate geographical analysis.
* Create a dataset suitable for Power BI analysis.

---

# 2. Source Dataset

The primary table used in the dashboard is:

```text
netflix_titles
```

The dataset contains fields such as:

| Column         | Description                                    |
| -------------- | ---------------------------------------------- |
| `show_id`      | Unique identifier for a title                  |
| `type`         | Movie or TV Show                               |
| `title`        | Name of the title                              |
| `director`     | Director information                           |
| `cast`         | Cast information                               |
| `country`      | Country or countries associated with the title |
| `date_added`   | Date the title was added to Netflix            |
| `release_year` | Original release year                          |
| `rating`       | Content rating                                 |
| `duration`     | Movie duration or number of seasons            |
| `listed_in`    | Genre/category information                     |
| `description`  | Description of the title                       |

---

# 3. Initial Data Inspection

The dataset was first loaded into Power BI using:

**Home → Get Data → Text/CSV → Transform Data**

The data was inspected in Power Query before being loaded into the Power BI report.

The initial inspection focused on:

* Column names
* Data types
* Empty rows
* Missing values
* Duplicate records
* Invalid values
* Column alignment
* Potential data-type conversion errors

---

# 4. Removing Completely Blank Rows

Completely empty rows do not provide useful information for analysis.

Power Query was used to remove rows where the entire record was blank.

### Operation

```text
Home
→ Remove Rows
→ Remove Blank Rows
```

This removes records that contain no meaningful information.

---

# 5. Handling Missing Values

Missing values were not automatically treated as errors.

This distinction is important because some fields are naturally optional.

For example, a Netflix title may not have available information for:

* `director`
* `cast`
* `country`
* `date_added`

These missing values do not necessarily mean that the record is corrupted.

Therefore, valid records containing missing optional information were retained.

### Example

```text
title:       Example Movie
director:    null
cast:        null
country:     United States
release_year: 2020
```

This record is still useful and should not be removed simply because `director` or `cast` is missing.

---

# 6. Identifying Important Missing Values

Some fields are more important for identifying a valid record.

The following fields were treated as critical:

### `show_id`

Every valid Netflix title should have a unique identifier.

Rows without a valid `show_id` should be investigated and removed if they are genuinely invalid.

### `title`

A record without a title is also considered suspicious and should be investigated.

---

# 7. Duplicate Record Handling

The `show_id` column was used as the primary identifier for detecting duplicate Netflix title records.

Duplicate records can cause:

* Incorrect title counts
* Incorrect KPI values
* Distorted charts
* Incorrect aggregations

Therefore, duplicate `show_id` records were investigated and removed where appropriate.

### Power Query operation

```text
Select show_id
→ Remove Rows
→ Remove Duplicates
```

This ensures that each Netflix title is represented once in the main title-level table.

---

# 8. Data Type Correction

Automatic type detection can sometimes assign inappropriate data types.

For example, attempting to interpret a value such as:

```text
TV-PG
```

as a Date produces a Power Query error.

Therefore, data types were reviewed manually.

The intended data types are:

| Column         | Data Type                                            |
| -------------- | ---------------------------------------------------- |
| `show_id`      | Text                                                 |
| `type`         | Text                                                 |
| `title`        | Text                                                 |
| `director`     | Text                                                 |
| `cast`         | Text                                                 |
| `country`      | Text                                                 |
| `date_added`   | Text / Date depending on transformation requirements |
| `release_year` | Whole Number                                         |
| `rating`       | Text                                                 |
| `duration`     | Text                                                 |
| `listed_in`    | Text                                                 |
| `description`  | Text                                                 |

The `release_year` column was converted to **Whole Number** because it represents a year.

The `date_added` field was initially treated carefully to avoid invalid automatic conversions.

---

# 9. Handling Data-Type Errors

Power Query can identify records that cannot be converted to the selected data type.

For example:

```text
Expected type: Date
Actual value: TV-PG
```

This indicates that the value is incompatible with the selected data type.

Instead of blindly removing every error, the data was inspected to determine whether the problem originated from:

* An incorrect data type
* A malformed record
* Incorrect column alignment
* An invalid source value

Only genuinely invalid records should be removed.

---

# 10. Text Cleaning

Text fields can contain unnecessary whitespace or non-printing characters.

Power Query provides transformations for standardizing these values.

### Trim

```text
Transform
→ Format
→ Trim
```

This removes unnecessary leading and trailing spaces.

Example:

```text
"  India"
```

becomes:

```text
"India"
```

### Clean

```text
Transform
→ Format
→ Clean
```

This removes non-printing characters that may interfere with analysis.

---

# 11. Country Data Transformation

The original `country` column can contain multiple countries in a single cell.

Example:

```text
United States, India, Canada
```

If this column is used directly in Power BI, the entire string can be interpreted as one category.

That would make country-level analysis inaccurate.

Therefore, a separate country table was created.

---

## 11.1 Country Table

A duplicate/reference of the cleaned Netflix title data was used to create a country-specific table.

The required fields were:

```text
show_id
country
```

---

## 11.2 Splitting Multiple Countries

The `country` field was split using a comma delimiter.

Conceptually:

### Before

```text
show_id | country
--------|---------------------------
s1      | United States, India
```

### After

```text
show_id | country
--------|----------------
s1      | United States
s1      | India
```

This allows one title to be associated with multiple countries.

---

# 12. Country Text Standardization

After splitting the country values, whitespace was removed using:

```text
Transform
→ Format
→ Trim
```

Non-printing characters were also removed where necessary using:

```text
Transform
→ Format
→ Clean
```

This prevents values such as:

```text
" India"
```

and:

```text
"India"
```

from being treated as different categories.

---

# 13. Country Blank Values

Blank or null country values were reviewed.

A missing country does not necessarily indicate that the entire title record is invalid.

Therefore, the country-specific table excludes unusable country values for country-level analysis while preserving the original title record in the main Netflix table.

---

# 14. Country Duplicate Removal

After splitting countries, duplicate title-country combinations were removed.

The combination:

```text
show_id + country
```

was used to identify duplicate relationships.

For example:

```text
s1 | India
s1 | India
```

becomes:

```text
s1 | India
```

This prevents the same title from being counted multiple times for the same country.

---

# 15. Data Model

The cleaned data was organized into a main title table and a country analysis table.

Conceptually:

```text
┌────────────────────────────┐
│      Netflix Titles        │
│                            │
│ show_id                    │
│ type                       │
│ title                      │
│ release_year               │
│ rating                     │
│ date_added                 │
│ duration                   │
│ listed_in                  │
│ description                │
└─────────────┬──────────────┘
              │
              │ show_id
              │
              │ 1 : *
              ▼
┌────────────────────────────┐
│     Netflix Countries      │
│                            │
│ show_id                    │
│ country                    │
└────────────────────────────┘
```

The relationship allows country-level analysis without modifying the original title-level structure.

---

# 16. Data Quality Rules

The following rules were used as a general guideline during cleaning:

| Condition                           | Treatment          |
| ----------------------------------- | ------------------ |
| Entire row is blank                 | Remove             |
| Duplicate `show_id`                 | Investigate/remove |
| Missing `show_id`                   | Investigate/remove |
| Missing `title`                     | Investigate/remove |
| Missing `director`                  | Keep               |
| Missing `cast`                      | Keep               |
| Missing `country`                   | Keep in main table |
| Missing `date_added`                | Keep               |
| Invalid `release_year`              | Investigate        |
| Invalid data type                   | Correct            |
| Duplicate title-country combination | Remove             |
| Leading/trailing whitespace         | Trim               |
| Non-printing characters             | Clean              |

---

# 17. Why Missing Values Were Not Automatically Removed

A common data-cleaning mistake is to remove every row containing a null value.

That approach would unnecessarily reduce the dataset.

For example:

```text
director = null
```

does not mean:

```text
The entire Netflix title is invalid.
```

It only means:

```text
Director information is unavailable.
```

Therefore, missing values were handled according to their analytical importance rather than automatically deleting every incomplete record.

---

# 18. Final Data Preparation

After cleaning and transformation, the dataset was prepared for Power BI visualization.

The final preparation included:

* Blank-row removal
* Duplicate checking
* Data-type correction
* Error inspection
* Text standardization
* Missing-value handling
* Country normalization
* Country duplicate removal
* Relationship creation

The cleaned data was then loaded into the Power BI data model using:

**Close & Apply**

---

# 19. Result

The cleaned dataset provides a more reliable foundation for:

* KPI calculations
* Movie vs TV Show analysis
* Release-year analysis
* Rating analysis
* Country analysis
* Dynamic visualizations
* Interactive filtering

The cleaning process also reduces the possibility of misleading results caused by duplicate records, invalid data types, malformed values, and improperly structured country information.

---

# 20. Power BI Dashboard

The cleaned dataset is used by the interactive Power BI dashboard.

The dashboard supports:

* Dynamic Field Parameters
* Movie/TV Show filtering
* Release-year filtering
* Rating analysis
* Country analysis
* KPI cards
* Cross-filtering
* Dynamic charts

The complete Power BI report is available in:

```text
PowerBI/Netflix_Analytics.pbix
```

---

## 📝 Summary

The data-cleaning process followed a simple principle:

> **Do not remove data merely because it is incomplete; remove or correct data when it is demonstrably invalid, duplicated, or structurally inconsistent.**

This approach preserves as much valid information as possible while preparing the dataset for accurate and interactive analysis in Power BI.
