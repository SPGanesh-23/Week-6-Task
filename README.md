# Week 6 — Healthcare Dataset: Exploratory Data Analysis

## Overview

This notebook (`Week_6.ipynb`) performs exploratory data analysis (EDA) on a healthcare dataset, as part of the **Big Data Analytics** coursework. EDA is the process of examining a dataset's structure, quality, and patterns *before* building models or drawing conclusions — it answers questions like "what does this data actually contain?", "is it clean?", and "what relationships exist between variables?"

The dataset (`healthcare_dataset.csv`) contains patient-level hospital records, including admission and discharge dates, medical conditions, billing amounts, and insurance providers. The notebook progresses through four stages: **data loading, cleaning, feature engineering, and visualization/analysis.**

---

## 1. Setup and Data Loading

**Libraries used:**
| Library | Purpose |
|---|---|
| `pandas` | Loading and manipulating tabular data (DataFrames) |
| `numpy` | Numerical operations |
| `matplotlib.pyplot` | Base plotting |
| `seaborn` | Statistical visualizations (built on matplotlib, with better defaults) |

The dataset is read from Google Drive (this notebook was authored in **Google Colab**, so the file path assumes a mounted Drive):

```python
df = pd.read_csv("/content/drive/MyDrive/Colab Notebooks/healthcare_dataset (1).csv")
```

> **Note:** To run this notebook outside Colab, update the path to wherever `healthcare_dataset.csv` is stored locally, and remove the Drive-mount dependency.

Initial inspection uses:
- `df.head()` — preview the first 5 rows to understand column structure
- `df.shape` — confirm the number of rows (records) and columns (features)
- `df.describe()` — summary statistics (mean, std, min, max, quartiles) for numeric columns

---

## 2. Data Cleaning

**Why this step matters:** raw datasets almost always contain missing or malformed values. Analyzing dirty data produces misleading results, so cleaning comes before any analysis.

Steps performed:
1. **Missing value check** — `df.isnull().sum()` counts nulls per column.
2. **Missing value removal** — `df.dropna()` removes any row containing at least one null value.
3. **Date type conversion** — `Date of Admission` and `Discharge Date` are converted from plain text/object columns into proper `datetime` objects using `pd.to_datetime(..., utc=True)`. This is necessary because dates stored as strings cannot be subtracted, sorted chronologically, or grouped by month — converting them unlocks date-based arithmetic and analysis.

---

## 3. Feature Engineering

**Feature engineering** means creating new, more useful columns from existing raw data.

Two new features are derived:

| New Column | How it's calculated | Purpose |
|---|---|---|
| `Hospital_Stays` | `Discharge Date − Date of Admission` (in days) | Measures length of stay per patient — a key operational/clinical metric |
| `Admission Month` | `Date of Admission` converted to a monthly `Period` (e.g., `2024-03`) | Enables grouping/trend analysis by calendar month rather than exact date |

---

## 4. Analysis and Visualizations

Each visualization answers a specific analytical question:

### a. Billing Amount by Medical Condition and Insurance Provider
A **stacked bar chart** showing total billing amount per medical condition, broken down by insurance provider (via `groupby(...).sum().unstack()`).
**Question answered:** *Which conditions generate the highest healthcare costs, and how is that cost distributed across insurers?*

### b. Distribution of Hospital Stays by Medical Condition
A **violin plot** (`seaborn.violinplot`) showing the full distribution — not just the average — of stay lengths per condition. A violin plot is used instead of a simple bar chart because it reveals spread, skew, and multiple peaks in the data, not just a single summary number.
**Question answered:** *Do certain conditions lead to consistently longer or more variable hospital stays?*

### c. Monthly Admissions Trend
A **line chart** of patient admissions aggregated by `Admission Month`.
**Question answered:** *Are admissions rising, falling, or seasonal over time?*

### d. Correlation Matrix (Age, Hospital Stays, Billing Amount)
A **correlation matrix** (`df.corr()`) quantifies the linear relationship between numeric variables, visualized as a **heatmap** (`seaborn.heatmap`, annotated, `coolwarm` colormap). Correlation values range from -1 (perfect inverse relationship) to +1 (perfect direct relationship), with 0 meaning no linear relationship.
**Question answered:** *Does age or length of stay have any relationship with billing amount?*

---

## How to Run

1. Ensure the following packages are installed: `pandas`, `numpy`, `matplotlib`, `seaborn`.
   ```bash
   pip install pandas numpy matplotlib seaborn
   ```
2. Place `healthcare_dataset.csv` in the same directory as the notebook (or update the file path in Cell 1 accordingly — see the Colab note above).
3. Run all cells in order — later cells depend on the cleaned DataFrame and derived columns (`Hospital_Stays`, `Admission Month`) created earlier in the notebook.

---

## Dataset Columns Referenced

| Column | Type | Description |
|---|---|---|
| `Date of Admission` | datetime | Date patient was admitted |
| `Discharge Date` | datetime | Date patient was discharged |
| `Medical Condition` | categorical | Diagnosed condition |
| `Insurance Provider` | categorical | Patient's insurer |
| `Billing Amount` | numeric | Total billed cost |
| `Age` | numeric | Patient age |

---

## Key Takeaways

- The dataset required cleaning (null removal) and type correction (string → datetime) before any meaningful analysis was possible.
- Two engineered features — `Hospital_Stays` and `Admission Month` — were essential for enabling time-based and duration-based analysis.
- The visualizations collectively explore **cost drivers** (billing by condition/insurer), **clinical variation** (stay length distribution), **temporal trends** (monthly admissions), and **inter-variable relationships** (correlation heatmap).
