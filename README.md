# 🛍️ Customer Shopping Behaviour Analysis

An end-to-end **Data Analytics project** that transforms raw customer
shopping data into a cleaned analytical dataset, stores it in **MySQL**,
answers business questions using **SQL**, and presents the results
through an interactive **BI dashboard**.

------------------------------------------------------------------------

## 📌 Project Overview

This project analyzes customer shopping behaviour using a dataset
containing **3,900 records and 18 original columns**.

The workflow covers:

> **Raw Data → Python Data Cleaning → Feature Engineering → MySQL → SQL
> Business Analysis → BI Dashboard → Business Insights**

The project is designed to demonstrate practical skills required for a
**Data Analyst / Business Analyst** workflow, including:

-   Data loading and exploration
-   Data quality checking
-   Missing-value treatment
-   Feature engineering
-   Data transformation
-   Relational database loading
-   SQL querying
-   Window functions and CTEs
-   KPI development
-   Interactive dashboard design
-   Business-oriented interpretation

------------------------------------------------------------------------

# 🎯 Project Objectives

The main objectives of this project are to:

1.  Understand customer purchasing behaviour.
2.  Analyze revenue across customer segments.
3.  Study the effect of discounts and promotions.
4.  Compare subscribed and non-subscribed customers.
5.  Identify popular products and categories.
6.  Analyze customer behaviour by age group.
7.  Examine repeat purchasing behaviour.
8.  Build an interactive dashboard for business users.
9.  Demonstrate a complete Python → SQL → Dashboard analytics workflow.

------------------------------------------------------------------------

# 📊 Dataset

The original dataset contains **3,900 rows and 18 columns**.

### Original columns

  Column                     Description
  -------------------------- ------------------------------------
  `Customer ID`              Unique customer identifier
  `Age`                      Customer age
  `Gender`                   Customer gender
  `Item Purchased`           Product purchased
  `Category`                 Product category
  `Purchase Amount (USD)`    Purchase amount
  `Location`                 Customer location
  `Size`                     Product size
  `Color`                    Product colour
  `Season`                   Purchase season
  `Review Rating`            Customer review rating
  `Subscription Status`      Whether the customer is subscribed
  `Shipping Type`            Shipping method
  `Discount Applied`         Whether a discount was applied
  `Promo Code Used`          Whether a promo code was used
  `Previous Purchases`       Number of previous purchases
  `Payment Method`           Payment method
  `Frequency of Purchases`   Purchase frequency

The dataset contains 37 missing values in `Review Rating`; the other
columns were non-null in the provided analysis.

------------------------------------------------------------------------

# 🧰 Technologies & Tools

## Programming & Data Analysis

-   **Python**
-   **Pandas**
-   **NumPy**

## Database

-   **MySQL**
-   **SQLAlchemy**
-   **mysql-connector-python**

## Visualization

-   **BI Dashboard tool** used to create the interactive dashboard

## Development Environment

-   Jupyter Notebook / JupyterLab
-   MySQL Server
-   MySQL client such as MySQL Workbench

------------------------------------------------------------------------

# 📦 Requirements

Install the Python dependencies with:

``` bash
pip install pandas numpy sqlalchemy mysql-connector-python
```

If you are working inside Jupyter Notebook:

``` bash
pip install pandas numpy sqlalchemy mysql-connector-python jupyter
```

### Software requirements

You should have:

-   Python 3.x
-   Jupyter Notebook or JupyterLab
-   MySQL Server
-   A MySQL client/tool
-   A compatible BI/dashboard application

------------------------------------------------------------------------

# 📁 Suggested Project Structure

A recommended repository structure is:

``` text
customer-shopping-behaviour-analysis/
│
├── README.md
│
├── data/
│   └── customer_shopping_behavior.csv
│
├── notebooks/
│   └── customer_behaviour_analysis.ipynb
│
├── sql/
│   └── customer_behaviour_analysis.sql
│
├── dashboard/
│   └── customer_behaviour_dashboard
│
└── images/
    └── dashboard.png
```

> The exact dashboard file extension depends on the BI tool you used.

------------------------------------------------------------------------

# 🔄 Complete Project Workflow

## Step 1 --- Load the Dataset

The raw CSV file is loaded using Pandas:

``` python
import pandas as pd

data = pd.read_csv("customer_shopping_behavior.csv")
```

The dataset contains:

``` text
3900 rows × 18 columns
```

------------------------------------------------------------------------

# 🔍 Step 2 --- Explore the Data

The project begins with basic exploratory checks.

### Dataset dimensions

``` python
data.shape
```

Result:

``` text
(3900, 18)
```

### Statistical summary

``` python
data.describe()
```

Important statistics from the analysis include:

-   Average age: **44.07**
-   Average purchase amount: **59.76 USD**
-   Average review rating: **3.75**
-   Average previous purchases: **25.35**
-   Purchase amount range: **20--100 USD**
-   Age range: **18--70**

### Dataset information

``` python
data.info()
```

The original dataset contains:

-   4 integer columns
-   1 floating-point column
-   13 categorical/object columns

------------------------------------------------------------------------

# 🧹 Step 3 --- Data Quality Check

Missing values were checked using:

``` python
data.isnull().sum()
```

The analysis found:

``` text
Review Rating → 37 missing values
```

All other columns contained zero missing values.

------------------------------------------------------------------------

# 🩹 Step 4 --- Handle Missing Review Ratings

Instead of deleting the affected rows, the project uses **category-wise
median imputation**.

First, the missing reviews were examined by category.

The missing values were distributed as:

  Category        Missing Ratings
  ------------- -----------------
  Clothing                     19
  Accessories                  11
  Footwear                      5
  Outerwear                     2
  **Total**                **37**

The missing values were then replaced using the median review rating of
the corresponding category:

``` python
data["Review Rating"] = (
    data.groupby("Category")["Review Rating"]
        .transform(lambda x: x.fillna(x.median()))
)
```

After this transformation, the dataset contained no missing values.

------------------------------------------------------------------------

# 🧹 Step 5 --- Standardize Column Names

The original column names contained spaces and mixed capitalization.

They were converted to lowercase and spaces were replaced with
underscores:

``` python
data.columns = data.columns.str.lower()
data.columns = data.columns.str.replace(" ", "_")
```

For example:

``` text
Customer ID
```

became:

``` text
customer_id
```

And:

``` text
Review Rating
```

became:

``` text
review_rating
```

The purchase amount column was also renamed:

``` python
data = data.rename(
    columns={"purchase_amount_(usd)": "purchase_amount"}
)
```

------------------------------------------------------------------------

# 🧠 Step 6 --- Feature Engineering

## 6.1 Create Age Groups

The project creates four age groups using quartiles:

``` python
labels = [
    "Young Adult",
    "Adult",
    "Middle Aged",
    "Senior"
]

data["age_group"] = pd.qcut(
    data["age"],
    q=4,
    labels=labels
)
```

This allows customer behaviour to be compared across age segments rather
than only using individual ages.

------------------------------------------------------------------------

## 6.2 Convert Purchase Frequency into Days

The categorical purchase frequency was converted into an approximate
number of days:

``` python
freq = {
    "Fortnightly": 14,
    "Weekly": 7,
    "Annually": 365,
    "Quarterly": 90,
    "Bi-Weekly": 14,
    "Monthly": 30,
    "Every 3 Months": 90
}

data["purchase_frequency_days"] = (
    data["frequency_of_purchases"].map(freq)
)
```

This creates a numerical representation that can be used for further
analysis.

------------------------------------------------------------------------

# 🔎 Step 7 --- Identify Redundant Information

The project checks whether:

``` text
promo_code_used
```

and:

``` text
discount_applied
```

contain identical information.

The comparison returned:

``` text
True
```

Therefore, `promo_code_used` was removed from the analytical table to
avoid keeping redundant information.

``` python
data = data.drop("promo_code_used", axis=1)
```

> **Note:** If reproducing the notebook from scratch, run the drop
> operation only once. Running it again after the column has already
> been removed produces a `KeyError`.

------------------------------------------------------------------------

# 🗄️ Step 8 --- Load the Clean Dataset into MySQL

The cleaned DataFrame is loaded into a MySQL database.

### Database

``` text
customer_behaviour
```

### Table

``` text
customer
```

The Python workflow uses:

``` python
import mysql.connector
import sqlalchemy
```

and creates a SQLAlchemy connection to MySQL.

The final DataFrame contains **3,900 rows and 19 columns**, including
the engineered features.

The data is then loaded using:

``` python
data.to_sql(
    name="customer",
    con=database_connection,
    if_exists="replace",
    index=False
)
```

------------------------------------------------------------------------

# 🧮 Step 9 --- SQL Business Analysis

After loading the cleaned data into MySQL, SQL is used to answer
business questions.

------------------------------------------------------------------------

## 1. Revenue by Gender

**Business question:**

> What is the total revenue generated by male vs. female customers?

``` sql
SELECT 
    gender,
    SUM(purchase_amount) AS total_revenue
FROM customer
GROUP BY gender;
```

------------------------------------------------------------------------

## 2. Discount Users Who Spend Above Average

**Business question:**

> Which customers used a discount but still spent more than the average
> purchase amount?

``` sql
SELECT 
    customer_id,
    purchase_amount
FROM customer
WHERE discount_applied = 'Yes'
  AND purchase_amount >= (
      SELECT AVG(purchase_amount)
      FROM customer
  );
```

This demonstrates the use of a **subquery**.

------------------------------------------------------------------------

## 3. Top 5 Products by Average Review Rating

``` sql
SELECT 
    item_purchased AS Product,
    ROUND(AVG(review_rating), 2) AS Average_Product_Review
FROM customer
GROUP BY item_purchased
ORDER BY Average_Product_Review DESC
LIMIT 5;
```

------------------------------------------------------------------------

## 4. Standard vs Express Shipping

``` sql
SELECT 
    shipping_type AS Shipping,
    ROUND(AVG(purchase_amount), 2) AS Average_Purchase_Amount
FROM customer
WHERE shipping_type IN ('Standard', 'Express')
GROUP BY shipping_type;
```

------------------------------------------------------------------------

## 5. Subscription Analysis

``` sql
SELECT 
    subscription_status,
    COUNT(customer_id) AS total_customers,
    ROUND(SUM(purchase_amount), 2) AS total_revenue,
    ROUND(AVG(purchase_amount), 2) AS average_purchase_amount
FROM customer
GROUP BY subscription_status
ORDER BY total_revenue DESC;
```

This helps determine whether subscribers have different spending
behaviour from non-subscribers.

------------------------------------------------------------------------

## 6. Products with the Highest Discount Percentage

``` sql
SELECT 
    item_purchased,
    ROUND(
        SUM(
            CASE 
                WHEN discount_applied = 'Yes' THEN 1
                ELSE 0
            END
        ) * 100.0 / COUNT(*),
        2
    ) AS discount_percentage
FROM customer
GROUP BY item_purchased
ORDER BY discount_percentage DESC
LIMIT 5;
```

This demonstrates conditional aggregation using:

``` sql
CASE WHEN
```

------------------------------------------------------------------------

## 7. Customer Segmentation

Customers are segmented into:

-   **New**
-   **Returning**
-   **Loyal**

using the project's defined business rules:

``` text
1 previous purchase      → New
2–10 previous purchases  → Returning
More than 10             → Loyal
```

SQL:

``` sql
WITH customer_type AS (
    SELECT 
        customer_id,
        previous_purchases,
        CASE
            WHEN previous_purchases = 1 THEN 'New'
            WHEN previous_purchases BETWEEN 2 AND 10 THEN 'Returning'
            ELSE 'Loyal'
        END AS customer_segment
    FROM customer
)
SELECT 
    customer_segment,
    COUNT(*) AS number_of_customers
FROM customer_type
GROUP BY customer_segment;
```

> These thresholds are business-defined segmentation rules used in this
> project.

------------------------------------------------------------------------

## 8. Top 3 Products in Each Category

A window function is used to rank products within each category:

``` sql
WITH item_counts AS (
    SELECT 
        category,
        item_purchased,
        COUNT(*) AS total_orders,
        ROW_NUMBER() OVER (
            PARTITION BY category
            ORDER BY COUNT(*) DESC
        ) AS item_rank
    FROM customer
    GROUP BY category, item_purchased
)
SELECT 
    category,
    item_rank,
    item_purchased,
    total_orders
FROM item_counts
WHERE item_rank <= 3
ORDER BY category, item_rank;
```

This demonstrates:

-   CTEs
-   `ROW_NUMBER()`
-   `PARTITION BY`
-   Aggregation

------------------------------------------------------------------------

## 9. Repeat Buyers vs Subscription

Repeat buyers are defined in this analysis as customers with more than
five previous purchases.

``` sql
SELECT 
    subscription_status,
    COUNT(customer_id) AS repeat_buyers
FROM customer
WHERE previous_purchases > 5
GROUP BY subscription_status;
```

------------------------------------------------------------------------

## 10. Revenue Contribution by Age Group

``` sql
SELECT 
    age_group,
    SUM(purchase_amount) AS total_revenue
FROM customer
GROUP BY age_group
ORDER BY total_revenue DESC;
```

For a more business-oriented analysis, the percentage contribution can
also be calculated.

------------------------------------------------------------------------

# 📊 Step 10 --- Interactive Dashboard

The final stage of the project is an interactive customer behaviour
dashboard.

## Main KPIs

The dashboard displays:

### 👥 Total Customers

**3.9K**

### 💰 Average Purchase Amount

**\$59.76**

### ⭐ Average Review Rating

**3.75**

------------------------------------------------------------------------

# 📈 Dashboard Visualizations

The dashboard contains the following analytical views:

### Subscription Analysis

A donut chart shows the proportion of customers who have a subscription.

Current dashboard view:

-   **Yes: 27%**
-   **No: 73%**

------------------------------------------------------------------------

### Revenue by Category

Compares revenue generated by:

-   Clothing
-   Accessories
-   Footwear
-   Outerwear

------------------------------------------------------------------------

### Sales by Category

Compares the number of purchases/sales across product categories.

------------------------------------------------------------------------

### Revenue by Age Group

Shows revenue contribution across:

-   Young Adult
-   Middle Aged
-   Adult
-   Senior

------------------------------------------------------------------------

### Total Sales by Age Group

Compares purchase volume across the age groups.

------------------------------------------------------------------------

# 🎛️ Dashboard Filters

The dashboard provides interactive filtering options for:

-   **Subscription Status**
-   **Gender**
-   **Category**
-   **Shipping Type**

These filters allow a business user to explore specific customer
segments without modifying the underlying SQL queries.

------------------------------------------------------------------------

# 🧭 How to Explore the Dashboard

A viewer can use the dashboard in the following order:

### Step 1 --- Start with the KPIs

Look at:

> Total Customers → Average Purchase Amount → Average Review Rating

This provides an overall view of customer behaviour.

### Step 2 --- Check subscription behaviour

Use the subscription chart to understand the proportion of subscribers.

### Step 3 --- Compare categories

Use:

> Revenue by Category

and:

> Sales by Category

to determine which product categories are driving revenue and purchasing
activity.

### Step 4 --- Analyze age groups

Compare:

> Revenue by Age Group

with:

> Total Sales by Age Group

This helps distinguish between **purchase volume** and **revenue
contribution**.

### Step 5 --- Apply filters

For example:

> Gender → Female\
> Category → Clothing\
> Subscription Status → Yes

The dashboard can then be used to investigate that specific customer
segment.

------------------------------------------------------------------------

# 🔗 Connecting the Project Components

The complete architecture can be understood as:

``` text
                 RAW CSV DATA
                      │
                      ▼
              ┌───────────────┐
              │    Python     │
              │    Pandas     │
              └───────┬───────┘
                      │
                      ▼
             DATA EXPLORATION
                      │
                      ▼
              DATA CLEANING
             ┌────────┴────────┐
             │                 │
       Missing Values      Standardization
             │                 │
             └────────┬────────┘
                      ▼
             FEATURE ENGINEERING
             ┌────────┴──────────┐
             │                   │
          Age Group       Purchase Frequency
             │                   │
             └────────┬──────────┘
                      ▼
              CLEAN DATAFRAME
                      │
                      ▼
                  MYSQL
             customer_behaviour
                      │
                      ▼
               customer table
                      │
                      ▼
               SQL ANALYSIS
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
       Revenue     Products     Customers
          │           │            │
          └───────────┼────────────┘
                      ▼
                BI DASHBOARD
                      │
                      ▼
             BUSINESS INSIGHTS
```

------------------------------------------------------------------------

# 💡 Key Analytical Areas

The project focuses on five major business areas:

## 1. Customer Behaviour

-   Age
-   Gender
-   Purchase frequency
-   Previous purchases
-   Customer segments

## 2. Product Performance

-   Product popularity
-   Category performance
-   Review ratings
-   Discount usage

## 3. Revenue

-   Gender-wise revenue
-   Category-wise revenue
-   Age-group revenue
-   Average purchase value

## 4. Customer Loyalty

-   Previous purchases
-   New / Returning / Loyal segmentation
-   Repeat buyers

## 5. Subscription Behaviour

-   Subscriber vs non-subscriber distribution
-   Subscriber revenue
-   Average purchase amount
-   Repeat buyers and subscription status

------------------------------------------------------------------------

# 🧪 Reproducing the Project

Follow these steps if you want to reproduce the project locally.

## Step 1 --- Clone the repository

``` bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd customer-shopping-behaviour-analysis
```

## Step 2 --- Install dependencies

``` bash
pip install pandas numpy sqlalchemy mysql-connector-python jupyter
```

## Step 3 --- Start MySQL

Create the database:

``` sql
CREATE DATABASE customer_behaviour;
```

Then select it:

``` sql
USE customer_behaviour;
```

## Step 4 --- Run the Python notebook

Open:

``` text
notebooks/customer_behaviour_analysis.ipynb
```

Run the cells in order:

``` text
1. Import libraries
2. Load CSV
3. Explore dataset
4. Check missing values
5. Impute missing review ratings
6. Standardize column names
7. Create age groups
8. Convert purchase frequency
9. Remove redundant column
10. Connect to MySQL
11. Load data into MySQL
```

## Step 5 --- Run SQL analysis

Open:

``` text
sql/customer_behaviour_analysis.sql
```

Execute the queries after the MySQL table has been created.

## Step 6 --- Open the dashboard

Open the dashboard file from:

``` text
dashboard/
```

Use the filters and visuals to explore the customer behaviour data.

------------------------------------------------------------------------

# 🔐 Security Note

**Never commit database credentials to GitHub.**

The original development notebook used MySQL connection credentials
directly in the code. Before publishing this project, replace hard-coded
credentials with environment variables.

For example:

``` python
import os

host = os.getenv("DB_HOST")
user = os.getenv("DB_USER")
password = os.getenv("DB_PASSWORD")
database = os.getenv("DB_NAME")
```

Add your credentials file to `.gitignore`:

``` text
.env
*.env
```

If a real password has already been pushed to a public repository,
**rotate/change that password immediately**.

------------------------------------------------------------------------

# 🚀 Possible Future Improvements

The current project can be extended with:

-   Customer lifetime value analysis
-   RFM customer segmentation
-   Monthly/seasonal purchasing trends
-   Customer retention analysis
-   Discount effectiveness analysis
-   Payment-method analysis
-   Shipping-method performance
-   Product profitability analysis
-   Correlation analysis
-   Predictive customer segmentation
-   Purchase amount prediction
-   Customer churn prediction

------------------------------------------------------------------------

# 📌 Portfolio Value

This project demonstrates an end-to-end analytics workflow rather than
only isolated SQL or Python exercises.

### Skills demonstrated

**Python** - Pandas - NumPy - Data cleaning - Missing-value treatment -
Feature engineering

**SQL** - Aggregations - `GROUP BY` - Subqueries - `CASE` - CTEs -
Window functions - `ROW_NUMBER()` - Conditional aggregation

**MySQL** - Database creation - Table loading - SQL analysis

**Data Visualization** - KPI design - Category analysis - Age-group
analysis - Subscription analysis - Interactive filtering - Dashboard
storytelling

------------------------------------------------------------------------

# 👨‍💻 Project Workflow Summary

``` text
1. Load raw customer data
             ↓
2. Explore dataset
             ↓
3. Check data quality
             ↓
4. Handle missing review ratings
             ↓
5. Standardize column names
             ↓
6. Engineer age & frequency features
             ↓
7. Remove redundant information
             ↓
8. Load cleaned data into MySQL
             ↓
9. Perform business analysis using SQL
             ↓
10. Build interactive dashboard
             ↓
11. Communicate business insights
```

------------------------------------------------------------------------

# ⭐ Final Takeaway

This project demonstrates how raw customer transaction data can be
transformed into a structured analytical solution.

The project combines:

> **Python for data preparation**\
> **MySQL for data storage**\
> **SQL for business analysis**\
> **BI dashboarding for visualization and communication**

The final result is an end-to-end **Customer Shopping Behaviour
Analytics solution** that can be presented as a portfolio project for
**Data Analyst, Business Analyst, BI Analyst, or entry-level Data
Science roles**.

------------------------------------------------------------------------

## 📂 Project Components

  Component         Purpose
  ----------------- ----------------------------------
  CSV dataset       Raw customer shopping data
  Python notebook   Cleaning and feature engineering
  MySQL database    Structured data storage
  SQL script        Business analysis
  BI dashboard      Interactive visualization
  README            Project documentation

------------------------------------------------------------------------

## 📬 Contact

**Rahul Ghoshal**

M.Sc. Data Science

LinkedIn: https://www.linkedin.com/in/imrahulghoshal/
GitHub: https://rahulghoshal.github.io/

------------------------------------------------------------------------

> **If you found this project useful, consider giving the repository a
> ⭐.**
