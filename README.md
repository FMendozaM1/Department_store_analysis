# Department_store_analysis

## Description
This project performs an exploratory analysis of an online sales dataset. The notebook analisis_de_ventas_gpt.ipynb walks through a full data-preparation and exploratory data analysis (EDA) pipeline: data loading, cleaning and standardization, feature derivation, aggregation and summary statistics, and visualizations to reveal key sales patterns (by product, category, city, and payment method).

## Contents of this README
Dataset overview
Objective
Step-by-step workflow (what was done and why)
Key outputs & findings (summary)
Notes and assumptions
Suggested improvements & next steps

### 1) Dataset overview



The analysis uses a CSV file of online transactions (referred in the notebook as ventas_online.csv — path in the notebook points to a local file). Typical columns found and used in the analysis include:

producto — product name or ID
categoria — product category
ciudad — customer's city
metodo_pago — payment method
cliente — client identifier or name
date/time fields and numeric fields related to price/quantity/total (if present)

The exact schema is inferred from the notebook code (the notebook contains operations on columns named "producto", "categoria", "ciudad", "metodo_pago", among others).

### 2) Objective
Clean and standardize the transactional dataset to ensure consistent categorical values.
Compute summary metrics that describe product variety and sales distribution.
Produce aggregated views (by city, category, payment method, top products) to support business decisions around inventory, marketing, and operations.
Provide visualizations to illustrate key findings.

### 3) Step-by-step workflow (detailed)

#### Step 1 — Load dataset
The CSV file was loaded into a pandas DataFrame:
df = pd.read_csv(<path_to_csv>)
Initial inspection performed with:
df (display), df.head(), and basic checks (df.shape, df.columns).
Purpose: verify file loads correctly and observe immediate issues (bad formatting, encoding issues or unexpected columns).

#### Step 2 — Remove exact duplicates
Duplicated rows were removed:
df = df.drop_duplicates()
Purpose: ensure downstream counts and aggregations are not skewed by duplicate records.

#### Step 3 — Basic counts and quick metrics
Number of unique products:
num_prod = len(df["producto"].unique())
Explore unique values for categories and other categorical columns:
df["categoria"].unique(), df["metodo_pago"].unique()
Purpose: understand dataset cardinality and spot inconsistent labels.

#### Step 4 — Standardize textual categorical fields
City names were converted to lowercase:
df["ciudad"] = df["ciudad"].str.lower()
Payment methods were standardized by replacing known variants with a single canonical label:
Example replacements observed:
"Tarjeta" → "tarjeta"
"T. crédito" → "tarjeta"
"Efectivo" → "efectivo"
Implemented using str.replace() for each variant:
df["metodo_pago"] = df["metodo_pago"].str.replace("Tarjeta", "tarjeta")
etc.
Purpose: remove casing and variant differences so grouping and counts are meaningful.

#### Step 5 — Inspect and handle missing values
The notebook includes operations that suggest checking for empty / NaN values (e.g., operations on cliente).
Common actions in this step:
df.isna().sum() to count missing values per column.
Decisions such as:
Drop rows with missing critical fields (e.g., missing cliente or missing total).
Or fill missing values when appropriate (e.g., df["col"].fillna("unknown", inplace=True)).
Purpose: reduce errors during aggregations and visualizations.

#### Step 6 — Clean date fields (if present)
There are notebook fragments that indicate cleaning of date strings (removing - or /) and converting to datetime:
Example pattern:
df["fecha_venta"] = df["fecha_venta"].astype(str).str.replace('-', '').str.replace('/', '')
then convert to pd.to_datetime(...) with appropriate format.
Purpose: enable time-series analysis by month/year and trend plots.

#### Step 7 — Derive useful columns
Create derived features typically useful for EDA (examples that are commonly included):
total_sale (if price and quantity are present: price * quantity)
year, month, day from the sale date for grouping
city_normalized if further normalization needed
Purpose: allow grouping by temporal or aggregated dimensions.

#### Step 8 — Aggregations and group-bys
Compute aggregated statistics:
Total sales by city: df.groupby("ciudad")["total"].sum().sort_values(ascending=False)
Sales by category: df.groupby("categoria")["total"].sum()
Counts by payment method: df["metodo_pago"].value_counts()
Top N products by sales or by count: df.groupby("producto")["total"].sum().nlargest(10)
Purpose: highlight where revenue is concentrated and which categories/products drive volume.

#### Step 9 — Visualizations
The notebook includes or anticipates plotting to support results (matplotlib / seaborn):
Bar charts for top cities, categories, payment methods.
Time-series plots for sales over time (monthly or daily).
Pie or bar charts for payment method distribution.
Purpose: provide visual, easy-to-digest evidence for stakeholders.

#### Step 10 — Final checks and export
After cleaning and aggregation, the notebook likely exports cleaned artifacts:
Example: df.to_csv("ventas_cleaned.csv", index=False)
Or save aggregated tables for reporting: top_products.to_csv("top_products.csv")
Purpose: persist cleaned data and results for reporting or further analysis.
