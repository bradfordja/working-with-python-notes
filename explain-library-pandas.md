# Pandas Library in Python

Pandas is the most popular Python library for data analysis, data manipulation, ETL (Extract, Transform, Load), reporting, and working with structured data.
It is built on top of NumPy and provides powerful data structures like Series and DataFrame.
It is commonly imported as:
import pandas as pd

Why Use Pandas?

Pandas is designed to make working with tabular data (similar to Excel spreadsheets or SQL tables) easy.
Typical use cases include:
* Reading CSV, Excel, JSON, SQL databases
* Cleaning messy data
* Handling missing values
* Filtering and sorting
* Aggregating data
* Joining datasets
* Creating reports
* Preparing data for Machine Learning

Core Data Structures
Structure	Description	Similar To
Series	One-dimensional labeled array	Single Excel column
DataFrame	Two-dimensional table	Excel spreadsheet / SQL table

## 1. Creating a DataFrame
```py
import pandas as pd

employees = pd.DataFrame({
    "id": [1, 2, 3],
    "name": ["Alice", "Bob", "Charlie"],
    "salary": [85000, 92000, 78000]
})

print(employees)
```

```sh
Output
   id     name  salary
0   1    Alice   85000
1   2      Bob   92000
2   3  Charlie   78000
```
Think of a DataFrame as an in-memory SQL table.

## 2. Reading Data
CSV
df = pd.read_csv("employees.csv")
Excel
df = pd.read_excel("employees.xlsx")
JSON
df = pd.read_json("employees.json")
SQL
df = pd.read_sql(query, connection)

## 3. Viewing Data
df.head()

Returns the first five rows.
df.tail()

Returns the last five rows.
df.info()

Shows:
* columns
* data types
* null values
df.describe()

Returns statistics:
* mean
* min
* max
* standard deviation
* quartiles

## 4. Selecting Columns
df["salary"]

Multiple columns
df[["name", "salary"]]

## 5. Filtering Rows
Employees making more than $80,000
high_salary = df[df["salary"] > 80000]

print(high_salary)
Result
   id   name  salary
0   1  Alice   85000
1   2    Bob   92000

## 6. Sorting
df.sort_values("salary")

Descending
df.sort_values(
    "salary",
    ascending=False
)

## 7. Adding Columns
df["bonus"] = df["salary"] * 0.10
Output
salary   bonus
85000    8500
92000    9200
78000    7800

## 8. Updating Data
df.loc[0, "salary"] = 90000

## 9. Removing Columns
df.drop(
    columns=["bonus"],
    inplace=True
)

## 10. Missing Values
Find missing values
df.isnull()

Count missing values
df.isnull().sum()

Fill missing values
df.fillna(0)
Or
df.fillna("Unknown")

Remove rows containing nulls
df.dropna()

## 11. Group By

Equivalent to SQL
```sql
SELECT department,
AVG(salary)
FROM employees
GROUP BY department;
```

Pandas
df.groupby("department")["salary"].mean()
Very common interview topic.

## 12. Aggregation
df["salary"].sum()

df["salary"].mean()

df["salary"].max()

df["salary"].min()

df["salary"].count()

13. Merge DataFrames

Similar to SQL JOIN.
```sql
employees.merge(
    departments,
    on="department_id"
)
```

Supports:
* inner
* left
* right
* outer

## 14. Concatenate
pd.concat([
    january,
    february
])
Like SQL UNION ALL.

15. Working with Dates
Convert to datetime
```py
df["hire_date"] = pd.to_datetime(
    df["hire_date"]
)
```

Extract year
df["hire_date"].dt.year
Month
df["hire_date"].dt.month
Day
df["hire_date"].dt.day

## 16. Apply Function
df["salary_after_tax"] = df["salary"].apply(
    lambda salary: salary * 0.80
)
Useful for custom transformations.

## 17. Export Data
CSV
df.to_csv(
    "employees.csv",
    index=False
)
Excel
df.to_excel(
    "employees.xlsx",
    index=False
)
JSON
df.to_json("employees.json")

## 18. Common ETL Workflow
import pandas as pd

# Read CSV
employees = pd.read_csv("employees.csv")

# Remove missing rows
employees = employees.dropna()

# Filter
employees = employees[
    employees["salary"] > 50000
]

# Calculate bonus
employees["bonus"] = (
    employees["salary"] * 0.10
)

# Save results
employees.to_csv(
    "processed.csv",
    index=False
)

This is a typical ETL pipeline:
CSV
 ↓
Read
 ↓
Clean
 ↓
Transform
 ↓
Filter
 ↓
Export

## 19. Real-World Example
Sales report
```py
sales = pd.read_csv("sales.csv")

report = (
    sales
    .groupby("region")
    ["amount"]
    .sum()
)

print(report)
```

Output
East      250000
West      180000
North     310000
South     210000

## 20. Common Pandas Interview Methods
Method	Purpose
```sh
read_csv()	Read CSV
read_excel()	Read Excel
head()	First rows
tail()	Last rows
info()	Metadata
describe()	Statistics
groupby()	Aggregation
merge()	SQL JOIN
concat()	Combine DataFrames
dropna()	Remove nulls
fillna()	Fill missing values
sort_values()	Sort
apply()	Custom transformation
loc[]	Label-based selection
iloc[]	Position-based selection
```

Pandas vs NumPy
Pandas	NumPy
Data analysis	Numerical computing
DataFrame	ndarray
Excel-like tables	Arrays
Handles mixed data types	Best for numeric data
Built on NumPy	Foundation library

A simple way to think about it:
* NumPy is optimized for fast numerical calculations.
* Pandas is optimized for working with structured business data.

Common Use Cases
* ETL pipelines
* Financial reporting
* Data analytics
* Dashboard preparation
* Machine learning preprocessing
* CSV/Excel processing
* SQL result analysis
* Time-series analysis
* Business intelligence

Senior Interview Answer
Pandas is the standard Python library for working with structured data. It provides the DataFrame and Series data structures, making it easy to read data from CSV, Excel, JSON, and SQL databases; clean and transform datasets; handle missing values; aggregate information with operations like groupby; join multiple datasets; and export results. In production, I commonly use Pandas in ETL pipelines, reporting applications, data analysis, and machine learning preprocessing before passing cleaned data to models or visualization tools.

Senior Interview Follow-Up
Interviewer: When would you use Pandas instead of NumPy?

Answer:
I use NumPy when working with high-performance numerical arrays, matrix operations, or scientific computing. I use Pandas when working with structured, tabular data that has labeled rows and columns, such as CSV files, SQL query results, or Excel spreadsheets. Pandas builds on NumPy but adds powerful features for data cleaning, filtering, grouping, joining, and time-series analysis, making it the preferred choice for business analytics and ETL workflows.
