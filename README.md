# Real Estate House Price ETL Pipeline

An end-to-end Python data engineering project for extracting, cleaning, analyzing, and loading Indian real estate listing data.

**Course:** Big Data Engineering with Python  
**Project:** House Price ETL and Descriptive Analysis  
**Team:** Marco Hany and Ahmed Khaled
## Project Overview

The pipeline processes a large property dataset with approximately 187,000 records. It converts inconsistent price and area values into analysis-ready fields, preserves unavailable prices as `Call for Price`, flags invalid records, and produces reusable CSV and SQLite outputs.

The project focuses on classical ETL and descriptive analysis. It does not use machine learning, Spark, or Hadoop.
## Pipeline Stages

### 1. Extract

`extract()` reads `data/raw/house_prices.csv` with pandas and logs the input shape and any file errors.

### 2. Transform

`transform()` performs the following operations:

- Removes exact duplicate rows.
- Standardizes `Amount(in rupees)` and `Price (in rupees)`.
- Converts values such as `42 Lac` and `1.6 Cr` into numeric INR values.
- Preserves unavailable prices as `Call for Price`.
- Combines `Carpet Area` and `Super Area` into `Area_sqft`.
- Derives a missing amount or unit price when the required values are available.
- Standardizes categorical fields and fills missing categories with explicit defaults.
- Adds `is_invalid` and `price_status` quality indicators.

Temporary numeric values are used for calculations but are not added to the final cleaned-data schema.
### 3. Analyze

`analyze()` filters invalid records and creates a location-level summary containing:

- Property count.
- Total and average property value.
- Median property price.
- Average area.
- Median price per square foot.

The notebook also includes additional descriptive questions covering locations, furnishing, bathrooms, missing values, categories, duplicates, and invalid records.
### 4. Load

`load()` writes the results to:

- `data/output/cleaned_house_prices.csv`
- `data/output/summary_house_prices.csv`
- `data/output/house_prices.db`

The SQLite database contains `cleaned_house_prices` and `summary_house_prices` tables.
### 5. Logging

The `log()` function records timestamps, pipeline milestones, row counts, quality statistics, and errors in `logfile.txt`.

## Project Structure

```text
project_python/
├── data/
│   ├── raw/
│   │   └── house_prices.csv
│   └── output/
│       ├── cleaned_house_prices.csv
│       ├── summary_house_prices.csv
│       └── house_prices.db
├── project.ipynb
├── logfile.txt
├── .gitattributes
├── .gitignore
└── README.md
```

## Requirements

- Python 3.9 or newer
- pandas
- NumPy
- Jupyter Notebook or VS Code with the Jupyter extension

Install the Python dependencies with:

```bash
pip install pandas numpy jupyter
```

## Running the Project

1. Clone the repository.
2. Open `project.ipynb` in Jupyter or VS Code.
3. Run the notebook cells in order.
4. Confirm the generated datasets in `data/output/` and execution details in `logfile.txt`.

The notebook expects the raw input file at:

```text
data/raw/house_prices.csv
```

## Validation Checks

The notebook validates that:

- Temporary price columns are not present in the final schema.
- Price columns do not contain unresolved null values.
- `is_invalid` contains Boolean values.
- A known reference amount is transformed correctly.
- `Call for Price` records are preserved.

## Large Files and Git LFS

The raw CSV, cleaned CSV, and SQLite database exceed GitHub's regular file-size limit. They are tracked with [Git LFS](https://git-lfs.com/).

Install Git LFS before cloning or pulling the full data files:

```bash
git lfs install
git lfs pull
```

## Contributors

- Marco Hany
- Ahmed Khaled
Markdown
# Real Estate House Prices: ETL and Descriptive Analysis

An end-to-end data engineering project for cleaning, validating, analyzing, and storing Indian real-estate listings. The pipeline is implemented in Python and pandas and is delivered as a reproducible Jupyter Notebook.

**Course:** Big Data Engineering with Python  
**Project:** House Price ETL and Descriptive Analysis  
**Team:** Marco Hany and Ahmed Khaled

## Project Objectives

- Extract property listings from the raw CSV dataset.
- Standardize prices, areas, and categorical values.
- Derive missing amount or unit-price values when reliable inputs exist.
- Flag invalid records without silently deleting them.
- Produce location-level summary metrics.
- Persist clean data and summaries as CSV files and SQLite tables.

The project focuses on practical ETL and data-quality workflows. It does not use machine-learning models, Spark, or Hadoop.

## Repository Structure

```text
project_python/
|-- data/
|   |-- raw/
|   |   `-- house_prices.csv
|   `-- output/
|       |-- cleaned_house_prices.csv
|       |-- summary_house_prices.csv
|       `-- house_prices.db
|-- project.ipynb
|-- logfile.txt
|-- .gitattributes
|-- .gitignore
`-- README.md
```

## Pipeline Design

### 1. Extract

`extract()` loads `data/raw/house_prices.csv` with pandas and logs the input shape. File errors are logged and re-raised so failures remain visible.

### 2. Transform

`transform()` performs the main cleaning workflow:

- Removes exact duplicate rows.
- Converts price expressions such as `42 Lac` and `1.6 Cr` to INR values.
- Preserves unavailable prices as `Call for Price`.
- Extracts numeric values from `Carpet Area` and `Super Area` into `Area_sqft`.
- Uses area and unit price to derive a missing amount when possible.
- Uses amount and area to derive a missing unit price when possible.
- Standardizes categorical fields and fills missing categories with explicit labels.
- Adds `is_invalid` and `price_status` quality indicators.

Temporary numeric price Series are used for calculations but are not added to the final output schema.

### 3. Analyze

`analyze()` excludes records flagged as invalid and creates a location-level summary containing:

- Total properties.
- Total property value in INR.
- Average and median property price.
- Average area in square feet.
- Median price per square foot.

The notebook also includes descriptive questions covering location prices, furnishing, bathrooms, missing critical values, category distributions, and data quality.

### 4. Load

`load()` writes the results to:

- `data/output/cleaned_house_prices.csv`
- `data/output/summary_house_prices.csv`
- `data/output/house_prices.db`

The SQLite database contains `cleaned_house_prices` and `summary_house_prices` tables.

### 5. Logging and Validation

The `log()` function records timestamps, pipeline stages, row counts, errors, and output events in `logfile.txt`. The validation section checks that:

- Temporary price columns are absent.
- Required price columns contain no missing values after transformation.
- `is_invalid` is Boolean.
- A known reference amount is transformed correctly.
- `Call for Price` records are preserved.

## Installation and Execution

### Requirements

- Python 3.9 or later
- Jupyter Notebook or JupyterLab
- pandas
- NumPy

Install the Python dependencies with:

```bash
python -m pip install pandas numpy jupyter
```

### Run the Notebook

1. Clone the repository.
2. Ensure the raw dataset is available at `data/raw/house_prices.csv`.
3. Open `project.ipynb` in VS Code or Jupyter.
4. Run the cells from top to bottom.

The cleaned files and execution log will be regenerated in their respective output locations.

## Large Files

The raw and generated datasets are tracked with Git LFS because they exceed GitHub's standard file-size limit. Install Git LFS before cloning or pushing:

```bash
git lfs install
git lfs pull
```

## Data Quality Policy

Invalid records are flagged rather than silently removed. A record is considered invalid when its amount or area is missing, zero, or negative after transformation. This preserves auditability while allowing the analysis stage to work with valid records only.