# Financial Data Analytics & Reporting Automation

## Project Overview

This project focuses on building a financial data analytics and
reporting workflow that transforms customer financial data into
actionable business insights.

The project is based on a real-world scenario presented in a three-part
data analytics tutorial: a survey/financial research firm has **25 field
agents** collecting data from different fields. The collected data is
maintained in Excel files and ultimately needs to be cleaned, analyzed,
and presented through a business dashboard.

I recreated the core data-ingestion, transformation, analysis, and
visualization workflow using **Google Cloud Platform, Google Drive API,
Python, Pandas, Power Query, Power BI, and DAX**.

> **Implementation note:** The original workflow also demonstrated
> Outlook/email automation. I could not implement that portion because
> it required an organizational Outlook environment. The implemented
> workflow begins with the files stored in Google Drive. The source
> dataset contains 25 Excel files; due to local hardware/processing
> constraints, the current Power BI analysis was developed using one
> representative file containing **6,000 records and 28 columns**.

------------------------------------------------------------------------

## Business Problem

The manual reporting process requires a manager to collect files from
field agents, clean inconsistent data, combine the information, and
prepare reports.

The project aims to demonstrate how this process can be made more
structured and scalable by introducing programmatic file ingestion, data
transformation, and interactive business intelligence reporting.

### Key Business Questions

-   What is the overall financial and credit profile of customers?
-   How does credit behaviour vary across age groups?
-   What payment behaviours are associated with different credit-mix
    categories?
-   Which loan types are most frequently represented?
-   How do credit-card and credit-inquiry patterns vary with age?
-   Which customers/segments have higher value according to a custom
    LTV-style score?
-   How can customer-value segments be associated with targeted offers?

------------------------------------------------------------------------

## Project Workflow

``` text
Field Agents
    ↓
Excel Data Files
    ↓
Google Drive
    ↓
Google Cloud Platform + Google Drive API
    ↓
Python
    ├── Authenticate
    ├── Access Drive folder
    ├── Retrieve supported files
    ├── Read files with Pandas
    └── Combine DataFrames
    ↓
Power Query
    ├── Data cleaning
    ├── Data type correction
    ├── Missing-value handling
    ├── Value/text standardization
    ├── Feature extraction
    └── Derived columns
    ↓
Power BI Data Model
    ↓
DAX Analysis
    ├── Custom LTV-style scoring
    └── Loan-type analysis
    ↓
Interactive Power BI Dashboard
```

------------------------------------------------------------------------

## Technology Stack

  Technology              Purpose
  ----------------------- -----------------------------------------------
  Python                  Data ingestion and file processing
  Pandas                  Reading, transforming, and combining datasets
  Google Cloud Platform   Cloud/API configuration
  Google Drive API        Programmatic access to Drive files
  Power Query             Data cleaning and transformation
  Power BI                Data modeling and dashboard development
  DAX                     Customer-value scoring and loan-type analysis

------------------------------------------------------------------------

## Data Ingestion with Python

A Google Cloud service account was created and used to authenticate with
the Google Drive API.

The Python workflow:

1.  Authenticates using the service-account credentials.
2.  Connects to Google Drive through the Drive API.
3.  Accesses the specified Drive folder.
4.  Identifies supported Excel, CSV, and Google Sheets files.
5.  Downloads the file content.
6.  Reads Excel files with `pandas.read_excel()` and CSV content with
    `pandas.read_csv()`.
7.  Stores the resulting DataFrames.
8.  Combines the DataFrames using `pd.concat()`.

### Core Python libraries

``` python
from google.oauth2 import service_account
from googleapiclient.discovery import build
import pandas as pd
import requests
from io import StringIO, BytesIO
```

The authentication uses a **Google Cloud service account with read-only
Google Drive access**.

------------------------------------------------------------------------

## Data Cleaning & Transformation

Power Query was used as the main transformation layer before
visualization.

### Cleaning operations

-   Removed unnecessary columns
-   Renamed columns for consistency
-   Corrected data types
-   Handled null and missing values
-   Replaced invalid values and special characters
-   Created conditional columns
-   Extracted values from existing fields

### Derived Features

Several analytical fields were created or standardized, including:

-   Median Age
-   Monthly In-Hand Salary
-   Number of Bank Accounts
-   Number of Credit Cards
-   Interest Rate
-   Number of Loans
-   Number of Credit Inquiries
-   Credit History in Months
-   Age Group

------------------------------------------------------------------------

## Power BI Data Model

The final PBIX model contains two tables:

### `df`

The primary customer financial dataset used for the dashboard.

Key fields include Customer ID, Age, Annual Income, Monthly In-Hand
Salary, Number of Bank Accounts, Number of Credit Cards, Interest Rate,
Number of Loans, Type of Loan, Delay from Due Date, Number of Credit
Inquiries, Credit Mix, Credit History Age, Payment Behaviour, Monthly
Balance, and Credit Score.

### `LoanTypeTable`

A DAX-generated table containing:

-   `CustomerID`
-   `LoanType`

This table separates multiple loan types stored within a customer's
original loan field, allowing individual loan types to be counted and
compared.

------------------------------------------------------------------------

## DAX Analysis

### Custom LTV-Style Customer Value Score

A custom weighted customer-value model was developed using:

  Factor                      Weight Direction
  ------------------------- -------- -----------
  Annual Income                  30% Positive
  Delay from Due Date            15% Negative
  Credit Mix Score               40% Positive
  Amount Invested Monthly       7.5% Positive
  Monthly Balance               7.5% Positive

Credit Mix was converted into a numerical score:

``` text
Good            → 3
Above Standard  → 2
Standard        → 1
Bad             → 0
```

The resulting score is then mapped to different promotional
recommendations.

> **Methodology note:** This is a custom LTV-style customer-value
> scoring model, not a traditional Customer Lifetime Value calculation
> based on expected future profit and customer lifetime.

------------------------------------------------------------------------

## Dashboard

The Power BI report contains **two pages**.

### Page 1 --- Financial & Credit Overview

The first page provides an overview of customer financial and credit
behaviour, including:

-   Annual income KPI
-   Monthly balance KPI
-   Delay from due date KPI
-   Credit utilization KPI
-   Age distribution
-   Change in credit-card limit by age
-   Payment behaviour by credit mix
-   Credit mix by age group

### Page 2 --- Customer Value & Loan Analysis

The second page focuses on:

-   LTV Score Based Performance
-   Credit cards and credit inquiries by age
-   Loan count distribution by loan type
-   Average credit inquiries by age group

------------------------------------------------------------------------

## Key Insights

-   The dashboard reports an average annual income of **164.02K**,
    average monthly balance of **407.09**, average delay from due date
    of **20.95**, and average credit utilization ratio of **32.31**.
-   **Standard and Good** credit-mix categories represent substantial
    portions of the analyzed customer base.
-   Credit-card and credit-inquiry activity varies with age and is
    generally higher among younger customers in the analyzed data.
-   The displayed average credit inquiries are highest for the **Teen**
    group (9.36) and lowest for **Old2** (5.18).
-   **Payday Loan (488)** and **Personal Loan (486)** are the most
    frequently represented loan types in the analyzed data.
-   The custom LTV-style score provides a framework for associating
    customer-value levels with targeted promotional offers.

> Loan-type counts are not mutually exclusive customer counts because a
> customer can have multiple loan types.

------------------------------------------------------------------------

## Business Recommendations

1.  Use customer-value segmentation to prioritize high-value customers
    for retention and targeted offers.
2.  Analyze loan preferences to identify opportunities for loan-product
    cross-selling.
3.  Combine credit mix and payment behaviour to create more relevant
    customer segments.
4.  Monitor credit activity by age group when designing
    customer-specific campaigns.
5.  Validate the custom LTV-style score against actual customer
    profitability before production use.

------------------------------------------------------------------------

## Project Limitations

-   The source scenario contains **25 Excel files**, but the current
    Power BI implementation uses one representative 6,000-row file
    because of local hardware/processing limitations.
-   Outlook/email-to-Drive automation from the original tutorial could
    not be implemented because an organizational Outlook environment was
    required.
-   The LTV component is a custom weighted scoring model and should not
    be interpreted as a traditional Customer Lifetime Value calculation.
-   Insights should be revalidated after scaling the analysis to the
    complete dataset.

------------------------------------------------------------------------

## Future Scope

-   Process all 25 field-agent Excel files.
-   Fully automate Outlook/email → Google Drive ingestion.
-   Schedule automated data refresh.
-   Scale the Power BI model to the complete dataset.
-   Implement incremental processing for larger datasets.
-   Validate the LTV-style score against actual business outcomes.
-   Deploy the dashboard for management and stakeholder use.

------------------------------------------------------------------------

## Recommended Repository Structure

``` text
financial-data-analytics/
│
├── data/
│   └── combined_part_1.xlsx
│
├── python/
│   └── drive_data_ingestion.py
│
├── powerbi/
│   └── Financial Data Project.pbix
│
├── screenshots/
│   ├── dashboard_page_1.png
│   └── dashboard_page_2.png
│
└── README.md
```

> **Security:** Never upload the Google Cloud service-account JSON
> credential to GitHub. Add credential files to `.gitignore` and use
> your own local credential path when running the script.

------------------------------------------------------------------------

## Skills Demonstrated

**Python • Pandas • Google Drive API • Google Cloud Platform • Power
Query • Power BI • DAX • Data Cleaning • Data Transformation •
Exploratory Data Analysis • Data Visualization • Business Intelligence •
Customer Segmentation**

------------------------------------------------------------------------

## Author

**Rahul Bansal** --- Data Analyst

[LinkedIn](https://www.linkedin.com/in/rahul-bansal-48101b306/)
