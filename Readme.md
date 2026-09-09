# Tesla Historical Stock & Revenue Analysis

## Project Overview

This project analyzes **Tesla's historical stock price and quarterly revenue data** using Python.

The project combines two different data sources:

- Historical Tesla stock market data collected using **Yahoo Finance through `yfinance`**
- Tesla quarterly revenue data extracted from a web page using **BeautifulSoup**

The extracted data is cleaned, transformed, stored as structured data, and visualized together to understand the relationship between Tesla's historical share price and reported revenue over time.

---

## Objectives

The main objectives of this project are to:

- Extract Tesla historical stock data using `yfinance`
- Extract Tesla quarterly revenue data using web scraping
- Clean and transform the scraped revenue data
- Convert revenue values into numeric format
- Convert date columns into datetime format
- Explore historical stock price data
- Compare stock price and revenue trends
- Build visualizations for business and financial analysis

---

## Technologies Used

| Technology | Purpose |
|---|---|
| Python | Data analysis and programming |
| Pandas | Data manipulation and cleaning |
| NumPy | Numerical operations |
| yfinance | Historical stock data extraction |
| BeautifulSoup | Web scraping |
| Requests | Sending HTTP requests |
| Matplotlib | Data visualization |
| Jupyter Notebook | Development and analysis |

---

## Project Workflow

```text
Data Collection
       ↓
Tesla Stock Data
       ↓
yfinance
       ↓
Tesla Revenue Data
       ↓
BeautifulSoup Web Scraping
       ↓
Data Cleaning & Transformation
       ↓
Pandas DataFrames
       ↓
Historical Analysis
       ↓
Stock Price + Revenue Visualization
       ↓
Business Insights
```

---

## Data Sources

### 1. Tesla Historical Stock Data

Tesla stock data is collected using the `yfinance` library.

```python
import yfinance as yf

tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")

tesla_data.reset_index(inplace=True)

tesla_data.head()
```

The resulting dataset contains historical market information such as:

- Date
- Open
- High
- Low
- Close
- Volume
- Dividends
- Stock Splits

---

### 2. Tesla Quarterly Revenue Data

Tesla quarterly revenue data is extracted from a web page using `Requests` and `BeautifulSoup`.

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"

response = requests.get(url)

soup = BeautifulSoup(response.content, "html.parser")
```

The relevant table is identified and converted into a Pandas DataFrame.

```python
tesla_revenue = pd.DataFrame(columns=["Date", "Revenue"])

tables = soup.find_all("table")

for table in tables:

    if "Tesla Quarterly Revenue" in str(table):

        for row in table.tbody.find_all("tr"):

            cols = row.find_all("td")

            if len(cols) == 2:

                date = cols[0].text.strip()
                revenue = cols[1].text.strip()

                tesla_revenue.loc[len(tesla_revenue)] = [
                    date,
                    revenue
                ]
```

---

## Data Cleaning

The revenue data initially contains formatting characters such as commas and dollar signs.

These values are cleaned before analysis.

```python
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].str.replace(
    ",", "", regex=False
)

tesla_revenue["Revenue"] = tesla_revenue["Revenue"].str.replace(
    "$", "", regex=False
)
```

Empty revenue records are removed:

```python
tesla_revenue = tesla_revenue[
    tesla_revenue["Revenue"] != ""
]
```

Revenue is then converted to numeric format:

```python
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].astype(float)
```

The date column is converted into datetime format:

```python
tesla_revenue["Date"] = pd.to_datetime(
    tesla_revenue["Date"]
)
```

---

## Dataset Preview

### Tesla Stock Data

The first rows of the historical Tesla stock dataset are inspected using:

```python
tesla_data.head()
```

![Tesla Stock Dataset](screenshots/tesla-stock-data.png)

---

### Tesla Revenue Data

The scraped quarterly revenue dataset is inspected using:

```python
tesla_revenue.head()
```

![Tesla Revenue Dataset](screenshots/tesla-revenue-data.png)

---

## Data Processing

The project follows a simple data-processing pipeline:

### Stock Data

```text
Yahoo Finance
     ↓
yfinance
     ↓
Tesla Historical Data
     ↓
Pandas DataFrame
```

### Revenue Data

```text
Web Page
   ↓
Requests
   ↓
BeautifulSoup
   ↓
HTML Table
   ↓
Pandas DataFrame
   ↓
Cleaning
   ↓
Numeric Revenue Data
```

---

## Visualization

A custom `make_graph()` function is used to visualize Tesla's historical share price and revenue.

```python
def make_graph(stock_data, revenue_data, stock):

    stock_data_specific = stock_data[
        stock_data.Date <= "2021-06-14"
    ]

    revenue_data_specific = revenue_data[
        revenue_data.Date <= "2021-04-30"
    ]

    fig, axes = plt.subplots(
        2,
        1,
        figsize=(12, 8),
        sharex=True
    )

    axes[0].plot(
        pd.to_datetime(stock_data_specific.Date),
        stock_data_specific.Close.astype("float")
    )

    axes[0].set_ylabel("Price ($US)")
    axes[0].set_title(
        f"{stock} - Historical Share Price"
    )

    axes[1].plot(
        pd.to_datetime(revenue_data_specific.Date),
        revenue_data_specific.Revenue.astype("float")
    )

    axes[1].set_ylabel(
        "Revenue ($US Millions)"
    )

    axes[1].set_xlabel("Date")
    axes[1].set_title(
        f"{stock} - Historical Revenue"
    )

    plt.tight_layout()
    plt.show()
```

---

## Final Dashboard / Visualization

The final visualization compares:

- Tesla historical share price
- Tesla quarterly revenue

```python
make_graph(
    tesla_data,
    tesla_revenue,
    tesla
)
```

![Tesla Stock Price and Revenue Analysis](screenshots/tesla-stock-revenue-analysis.png)

---

## Key Analysis

The project demonstrates how multiple data sources can be combined for financial analysis.

### Stock Market Analysis

Historical Tesla stock data was collected using `yfinance`, allowing the project to work with actual market data rather than manually entered values.

### Revenue Analysis

Tesla quarterly revenue was extracted from an HTML table using web scraping and converted into a structured Pandas DataFrame.

### Data Cleaning

The revenue data required:

- Removing `$` symbols
- Removing commas
- Removing empty records
- Converting revenue to `float`
- Converting dates to datetime

### Comparative Visualization

The final graph places stock price and revenue trends into a single visualization so that both datasets can be examined over the same time period.

---

## Skills Demonstrated

This project demonstrates practical skills in:

### Python

- Functions
- Data structures
- File handling
- Libraries and modules

### Data Analysis

- Data loading
- Data cleaning
- Data transformation
- Data inspection
- Date handling

### Web Scraping

- HTTP requests
- HTML parsing
- BeautifulSoup
- Table extraction

### Financial Data

- Historical stock data
- Share price analysis
- Quarterly revenue data

### Data Visualization

- Matplotlib
- Time-series visualization
- Comparative analysis

---

## Project Structure

```text
Analyzing-Historical-Stock-Revenue-Data-and-Building-a-Dashboard/
│
├── data_analytics_full_practice/
│   ├── Analyzing-Historical-Stock-Revenue-Data-and-Building-a-Dashboard.ipynb
│   └── tesla_revenue.csv
│
├── csv_file/
│   ├── tesla_revenue.csv
│   ├── auto.csv
│   ├── automobiles.csv
│   ├── laptop.csv
│   └── module_5_auto.csv
│
├── 1.DA0101EN-Review-Introduction (1).ipynb
├── 2.Practice_data_loading.ipynb
├── 3.DA0101EN-2-Review-Data-Wrangling.ipynb
├── 4.practice_data_wrangling (1).ipynb
├── 5.Exploratory_data_analysis_cars.ipynb
├── 6.parctice_Exploratory_data_analysis.ipynb
├── 7.DA0101EN-4-Review-Model-Development.ipynb
├── 8.practice_model_development_laptops.ipynb
├── 9.Model_Evaluation_and_Refinement_cars.ipynb
├── 10.practice_model_evaluation.ipynb
├── 11.practice_project.ipynb
├── 12.House_Sales_in_King_Count_USA (2).ipynb
│
├── Readme.md
└── ...
```

---

## Main Project Notebook

The primary project notebook is:

```text
data_analytics_full_practice/
└── Analyzing-Historical-Stock-Revenue-Data-and-Building-a-Dashboard.ipynb
```

The notebook contains the complete workflow:

```text
Import Libraries
      ↓
Extract Tesla Stock Data
      ↓
Scrape Tesla Revenue Data
      ↓
Clean Revenue Data
      ↓
Save Revenue Data
      ↓
Create Visualization
      ↓
Compare Stock Price & Revenue
```

---

## Project Screenshots

### Tesla Stock Data

![Tesla Stock Data](screenshots/tesla-stock-data.png)

### Tesla Revenue Data

![Tesla Revenue Data](screenshots/tesla-revenue-data.png)

### Data Cleaning

![Data Cleaning](screenshots/data-cleaning.png)

### Final Stock & Revenue Analysis

![Final Stock Revenue Analysis](screenshots/tesla-stock-revenue-analysis.png)

---

## What I Learned

Through this project, I practiced the complete workflow of collecting and analyzing financial data.

### Data Collection

I learned how to collect historical stock information programmatically using `yfinance`.

### Web Scraping

I practiced extracting structured information from an HTML table using `Requests` and `BeautifulSoup`.

### Data Cleaning

I worked with real-world formatting issues such as:

- Currency symbols
- Commas
- Empty values
- Date formats
- Numeric type conversion

### Visualization

I learned how to create a combined time-series visualization to compare different financial metrics.

---

## Future Improvements

Possible improvements to this project include:

- Adding interactive dashboards using Plotly
- Adding more financial indicators
- Comparing Tesla with other companies
- Adding yearly revenue growth analysis
- Adding stock return calculations
- Adding moving averages
- Building an interactive Streamlit dashboard
- Automating data collection on a scheduled basis

---

## Author

**Divya Upadhyay**

B.Tech Computer Science | Artificial Intelligence

Interested in:

- Data Science
- Machine Learning
- Data Analytics
- Python
- SQL
- Artificial Intelligence

---

## Connect With Me

- GitHub: [CyberWol-12](https://github.com/CyberWol-12)
- LinkedIn: [Divya Upadhyay](https://www.linkedin.com/in/divya-upadhyay-a77060348)
- Email: divyau0802@gmail.com

---

## Conclusion

This project demonstrates an end-to-end workflow for **financial data collection, web scraping, data cleaning, analysis, and visualization** using Python.

It combines market data and revenue data from different sources and presents them through a clear visual analysis.

The project provides practical experience with real-world data workflows and forms a foundation for building more advanced financial analytics and dashboard applications.