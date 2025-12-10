#  Sales Data Analysis and PostgreSQL Star Schema ETL

This project performs an End-to-End (E2E) sales data analysis, including data cleaning, transformation into a Star Schema for a data warehouse, loading into a PostgreSQL database, and generating key business insights for strategic decision-decision making.


--

Environment setup (Windows PowerShell)

Prerequisites:

- Python 3.8+ (3.10 or 3.11 recommended)
- PostgreSQL server (local or reachable from the machine)
- Optional: `virtualenv` or use Python venv

Sample steps (PowerShell):

```powershell
# 1) Create and activate a virtual environment
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# 2) Upgrade pip and install requirements
python -m pip install --upgrade pip
pip install -r requirements.txt

# 3) (Optional) Install Jupyter if you want to run the notebook interactively
pip install jupyterlab notebook

# 4) Start Jupyter Notebook (or use VS Code to open the .ipynb)
jupyter notebook Notebook.ipynb
```

## 📦 Project Requirements

To replicate the environment and run the code, the following Python libraries are required:

* `pandas`
* `sqlalchemy`
* `psycopg2-binary`
* `matplotlib`
* `seaborn`
* `python-dotenv`
* `unicodedata2`
* `datetime`

## ⚙️ Database Connection


PostgreSQL setup (local)

1. Install PostgreSQL (https://www.postgresql.org/download/).
2. Create database and (optionally) a dedicated user. Example psql commands:

```sql
-- Connect as postgres superuser, then run:
CREATE DATABASE "Riwi_Ventas";
CREATE USER rwuser WITH ENCRYPTED PASSWORD 'change_me_strong_password';
GRANT ALL PRIVILEGES ON DATABASE "Riwi_Ventas" TO rwuser;
```

3. If using the notebook's variables, update them to match your DB user/password/host/port. Example environment variables approach (recommended below).

--

Secure credential recommendation

- Do NOT keep credentials in plaintext inside notebooks. Instead:
  - Use environment variables and read them in the notebook with `os.environ`.
  - Or store secrets in a `.env` file and load them with `python-dotenv` (and add `.env` to `.gitignore`).

Example code snippet to replace the hard-coded variables in the notebook:

```python
import os
from dotenv import load_dotenv
load_dotenv()  # reads .env in the repo root

user = os.getenv('RW_USER')
password = os.getenv('RW_PASSWORD')
host = os.getenv('RW_HOST', 'your_localhost')
port = os.getenv('RW_PORT', '5432')
database = os.getenv('RW_DATABASE', 'your_db_name')
schema = os.getenv('RW_SCHEMA', 'your-schema')

engine = create_engine(f"postgresql+psycopg2://{user}:{password}@{host}:{port}/{database}")
```

Add `.env` to `.gitignore` and never commit secrets.

## 🧹 Data Cleaning and Transformation (ETL)
The data cleaning process, detailed in Read_Load_Cleaning.ipynb, involved steps like cleaning special characters, reordering columns, standardizing text case, correcting typographical errors, and ensuring product types are accurate:

Column Renaming: The column originally detected as 'Pais' was renamed to 'Costo_Total'.

Rows after cleaning: 975,363.

Date Conversion: The Fecha column was successfully converted to a datetime64[ns] object.

Missing Values: After cleaning, the columns Descuento, Costo_Envio, and Costo_Total still showed null values.

## ⭐ Star Schema Creation and Load

The Export_To_Sql.ipynb notebook transforms the cleaned data (clean_sales.csv) into a Star Schema and loads the tables into PostgreSQL.

Dimension Tables and Fact Table
The following tables were successfully loaded into the RWventas database:

Dimension Tables:

DimProduct

DimClient

DimSaleType

DimCity

DimDate

Fact Table:

FactSales

The FactSales table uses foreign keys (date_id, product_id, client_id, sale_type_id, city_id) and includes measures such as Cantidad, Precio_Unitario, Descuento, Costo_Envio, and Costo_Total.

## 🖼️ Dashboard Screenshots (Placeholder)

![Dashboards PBI ](img\Dashboards.png)

## 💡 Key Findings and Strategic Recommendations
Based on the sales dashboard analysis, the following actionable insights were generated:

1. Leading Products and Category Preference
Insight: Leading products include Cheese, Yogurt, Chocolate, Arepa, and Coffee. The "Dairy" category has a significant share of total sales.

Strategic Recommendation: Prioritize marketing campaigns, promotions, and commercial packages around dairy products (e.g., combos, special bundles). Evaluate expanding the product offering within the "Dairy" category.

2. Sales Stability and Trends
Insight: Sales show relatively consistent behavior with occasional peaks. A visible uptick occurs towards the end of the month (or end of the period), suggesting periodic purchasing patterns.

Strategic Recommendation: Implement remarketing campaigns or temporary promotions around these identified peaks (e.g., monthly offers near the end of the month). This can optimize recurring revenue and take advantage of purchasing patterns.

3. Geographic Distribution of Sales
Insight: Sales are distributed across several cities/regions, indicating customer geographic diversification.

## 1. Key Measures (DAX)
### Top 5 Products & Top 5 Clients (Visualización)
```
Total Sales = SUM(FactSales[Costo_Total])
```
###  Comparison of Current Year vs. Previous Year
```
Sales LY = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Fecha]))

YoY Growth % = DIVIDE([Total Sales] - [Sales LY], [Sales LY])
```

### Strategic Recommendation: Design regional commercial strategies, such as targeted promotions by city, adapting the product mix, and differentiated shipping logistics. This can maximize conversion and optimize inventory according to geographic demand.


## -- Created by : Dieguito Teran 
