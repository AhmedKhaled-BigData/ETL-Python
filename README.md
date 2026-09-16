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

The notebook answers the following six descriptive questions:

1. Which locations have the highest median and average property price?
2. Which property characteristics are associated with higher prices?
3. Which locations have the highest price per square foot?
4. How many records have missing critical price or area values?
5. Which furnishing and property-status categories are most common?
6. How many duplicate and invalid records are found?

Each question is implemented and answered in a separate section of `project.ipynb`.

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