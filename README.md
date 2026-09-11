Markdown
# Real Estate Data Engineering Pipeline (ETL & Analysis)

**Course:** Big Data Engineering with Python  
**Project Title:** House Price ETL & Descriptive Analysis  
**Team Members:** Marco Hany & Ahmed Khaled  

---

## 1. Project Overview & Objective
This project implements an end-to-end, reproducible Data Engineering pipeline (ETL) built using Python and `pandas`. The primary objective is to clean, standardize, transform, and analyze a real-estate dataset containing **187,531 raw property records**, and then load the clean data into structured CSV files and an SQLite database.

> **Scope Note:** Per course guidelines, no machine learning models or advanced big-data platforms (e.g., Spark/Hadoop) are included. The focus remains strictly on robust Data Engineering processes (Extract, Transform, Analyze, Load, and Logging).

---

## 2. Directory & Folder Structure
```text
project/
├── data/
│   ├── raw/
│   │   └── house_prices.csv        # Raw dataset
│   └── output/
│       ├── cleaned_house_prices.csv    # Cleaned dataset
│       ├── summary_house_prices.csv    # Aggregated summary table
│       └── house_prices.db             # SQLite database output
├── project.ipynb                        # Complete runnable Jupyter Notebook pipeline
├── logfile.txt                          # Timestamped ETL execution log
└── README.md                            # Project documentation and report
```

## 3. Dataset Description

The source dataset contains real estate property details across various cities in India with 21 initial columns:

Identifier: Index, Title, Society

Monetary Fields: Amount(in rupees), Price (in rupees)

Physical Characteristics: Carpet Area, Super Area, Dimensions, Plot Area, Floor, Bathroom, Balcony, Car Parking

Categorical & Descriptive: location, Status, Transaction, Furnishing, facing, overlooking, Ownership, Description

## 4. ETL Architecture & Pipeline Design

### A. Extract (extract())

Ingests the raw CSV dataset from data/raw/187k_house_prices.csv.

Validates initial row and column counts using try/except blocks to handle file I/O errors.

### B. Transform (transform())

Duplicate Handling: Detects and removes exact duplicate property rows.

Monetary Standardization:

Parses text-based amount expressions (e.g., 42 Lac →4,200,000 , 1.6 Cr →16,000,000).
Standardizes raw prices by stripping currency symbols and commas.
Handles unlisted/hidden prices (Call for Price, Price on Request) by converting them to NaN.
Performs cross-column missing value imputation: fills missing Price values using cleaned Amount values where available.

Area Standardization:

Cleans Carpet Area and Super Area by extracting numerical values and stripping unit labels (sqft).
Creates a unified area measure (final_area_sqft) using Carpet Area as the primary choice and Super Area as a fallback.

Feature Engineering:

Computes price_per_carpet_area (Price/Carpet Area) to establish a price-per-unit metric for location comparison.

Categorical Standardization:

Cleans categorical fields (location, Status, Transaction, Furnishing, facing, Ownership) using title-case formatting and stripping extra spaces.

Data Quality & Flagging Rule:

Identifies records with non-positive price or area values (≤0) and flags them with an is_invalid = True indicator.
No Silent Deletion: Invalid records are flagged rather than silently deleted to preserve auditability and overall row metrics.

### C. Analyze (analyze())

Evaluates the clean dataset (is_invalid == False) to answer business questions:

Top Locations by Price: Aggregates average and median property prices per location.
Price Per Area Metric: Identifies locations with the highest median price per square foot.
Categorical Distribution: Analyzes common property statuses and furnishing types.
Data Quality Audit: Measures total missing values in key fields before and after cleaning.

### D. Load (load())

Exports the clean dataset to data/output/cleaned_house_prices.csv.
Exports the location-aggregated summary metrics to data/output/summary_house_prices.csv.
Writes both tables into an SQLite database (data/output/house_prices.db).

### E. Logging (log())

Tracks execution timestamps, milestone step completions, data shape metrics, and potential runtime exceptions into logfile.txt.

## 5. Summary of Analysis Results

Missing Value Resolution: High missingness in Price (in rupees) was significantly reduced by imputing values from Amount(in rupees).

Area Metrics: Carpet area was successfully isolated into numeric floats, enabling meaningful price-per-sqft evaluations across locations.

Data Quality: Outlier and impossible prices (≤0) were successfully tagged without dropping essential metadata.

## 6. How to Run the Pipeline

Ensure Python 3.x is installed with required packages:

Bash
pip install pandas numpy
Place the raw CSV dataset in data/raw/house_prices.csv.

Open and run all cells sequentially in project.ipynb.

Outputs will be generated automatically in data/output/ alongside the execution log in logfile.txt.