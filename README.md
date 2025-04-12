# Loan-Default-Prediction-Dashboard

### Dashboard Link: *(https://app.powerbi.com/groups/me/reports/7c35cf37-1aea-413d-8ff6-09c7a898129d/bb86ca447e09fee46945?experience=power-bi)*

---

## Problem Statement

This dashboard helps financial institutions and credit analysts understand borrower behavior and identify risk of loan default. By analyzing a wide array of attributes—such as credit score, employment type, income, loan amount, and default history—the dashboard facilitates evidence-based decision-making. It also monitors year-over-year changes in default rates and loan disbursement trends.

The goal is to provide actionable insights to help reduce default rates by profiling borrowers, segmenting them by demographic and financial indicators, and uncovering risk patterns hidden in the data.

---

### Steps Followed

- **Step 1**: Loaded the dataset from an Excel file into **Microsoft SQL Server** using SQL Server Management Studio (SSMS).
- **Step 2**: Created a new database `Loan`, and imported the table as `Loan_default` using the "Import Flat File" wizard.
- **Step 3**: Verified the data structure and previewed columns using:
  ```sql
  SELECT * FROM dbo.Loan_default;
  ```
- **Step 4**: Launched Power BI Service and created a **Dataflow** by connecting to the SQL Server database.
- **Step 5**: In Power Query Editor, enabled **“Column profiling based on entire dataset”** for deeper analysis.
- **Step 6**: Verified and fixed data types, especially converting the `Loan Date` column to `DateTime` to allow **incremental refresh** setup.
- **Step 7**: Created a **calculated column** in Power BI to group applicants into different age groups:

```DAX
Age Group = 
IF('Loan_default'[Age]<=25, "Young",
IF('Loan_default'[Age]<=45, "Middle Age Adults",
"Older Adults"))
```

![Snap_Age_Group](https://github.com/user-attachments/assets/08879e66-4656-4f2b-9d15-84a06862e139)

- **Step 8**: Set up **Scheduled Refresh** in Power BI Service to update the Dataflow daily at 5:30 AM (EAT).

- **Step 9**: Configured **Incremental Refresh** to store 5 years of data and refresh only the last 10 days using the `Loan Date` column.

- **Step 10**: Created key **DAX Measures** for financial and risk analytics:

```DAX
Average Income by Employment type = 
CALCULATE(
    AVERAGE('Loan_default'[Income]),
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
)

Default Rate by Employment Type = 
VAR totalrecords = COUNTROWS(ALL('Loan_default'))
VAR defaultcases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
RETURN CALCULATE(DIVIDE(defaultcases, totalrecords), ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])) * 100
```

- **Step 11**: Built **3 separate report pages** in Power BI Desktop:
  - Loan Default Overview
  - Applicant Demographics & Financial Profile
  - Financial Risk Metrics

- **Step 12**: Inserted text boxes, KPI cards, bar/line/pie charts, ribbon chart, decomposition tree, and matrix visuals.

- **Step 13**: Added slicers for:
  - Employment Type
  - Education
  - Credit Score Bins
  - Loan Purpose
  - Age Group

- **Step 14**: Created a custom theme and applied layout formatting for clarity and consistency.

- **Step 15**: Published report to **Power BI Service** and validated all refresh configurations.

---

## Report Snapshots

### Page 1: Loan Default Overview

- **Visuals Used**:
  - Pie chart (Default vs Non-Default)
  - KPI cards (Avg Loan, Interest Rate, Default Rate)
  - Bar chart (Loan Purpose breakdown)
  - Line chart (Loan Issuance over time)

![Snap_Page1](https://github.com/user-attachments/assets/bd631002-24b5-459a-9bd5-dfcbab5f0a11)

---

### Page 2: Demographics & Financial Profile

- **Visuals Used**:
  - Ribbon chart (Education vs Loan Amount)
  - Decomposition Tree (Income → Default Rate)
  - Tree map (Marital Status by Loan Volume)
  - Matrix visual (Employment Type vs Income and Default)

![Snap_Page2](https://github.com/user-attachments/assets/a94c456c-9e66-4bab-a716-d34bb21cbbbc)

---

### Page 3: Financial Risk Metrics

- **Visuals Used**:
  - Line Chart (YoY Default Rate Change)
  - Bar Chart (Loan Amount by Credit Score Bin)
  - Card visuals (Median Loan by Credit Bin)
  - KPI: YoY Loan Growth

![Snap_Page3](https://github.com/user-attachments/assets/f28d3659-69cf-4fb9-ac23-ded32fdd45fc)

---

## Insights

### [1] Employment & Risk Patterns
- **Self-Employed** applicants show higher default rates compared to full-time employees.
- **Part-Time** employees have the lowest average income and highest DTI ratios.

### [2] Age & Credit Score
- Most loans are issued to **Middle Age Adults (30–45)** with moderate default risk.
- **Low Credit Score** borrowers are overrepresented in default cases.

### [3] Loan Purpose
- **Debt Consolidation** and **Home Purchase** account for the largest share of high-value loans.
- **Education Loans** show higher defaults, especially among young borrowers.

### [4] YoY Trends
- **YoY Default Rate** increased by nearly 8% from previous year.
- Loan issuance also rose by 12%, indicating looser lending criteria.

---

## Dataset Overview

| Column Name         | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| LoanID              | Unique identifier for each loan                                             |
| Age                 | Borrower’s age                                                              |
| Income              | Borrower's annual income                                                    |
| LoanAmount          | Total amount requested/approved                                             |
| CreditScore         | Score indicating borrower creditworthiness (300–850 scale)                 |
| MonthsEmployed      | Duration employed with current employer                                     |
| NumCreditLines      | Total number of active credit lines                                         |
| InterestRate        | Annual percentage rate on loan                                              |
| LoanTerm            | Loan repayment period in months                                             |
| DTIRatio            | Debt-to-Income ratio                                                        |
| Education           | Education level (e.g., Bachelor’s, Master’s)                                |
| EmploymentType      | Full-Time, Part-Time, Self-Employed, etc.                                   |
| MaritalStatus       | Single, Married, Divorced, etc.                                             |
| HasMortgage         | Yes/No – Indicates existing mortgage                                        |
| HasDependents       | Yes/No – Indicates responsibility for dependents                            |
| LoanPurpose         | Purpose of loan (e.g., Education, Home Purchase, Debt Consolidation)        |
| HasCoSigner         | Yes/No – Indicates if a co-signer is present                                |
| Default             | Yes/No – Indicates loan default                                              |
| Loan Date           | Date loan was issued (DD/MM/YYYY)                                           |

---

## Deployment & Automation

- **Scheduled Refresh**: Runs daily at 5:30 AM using Power BI Service.
- **Incremental Refresh**: Retains 5 years of historical data; refreshes last 10 days only.
- **Notification Setup**: Alerts for refresh failures sent to dataflow owner via email.
- **Power BI Workspace**: Report and dataflow published to secured workspace.

---

## Conclusion

This project offers a powerful, automated solution for loan default analysis using Microsoft SQL Server and Power BI. Through custom DAX, dataflows, and advanced visuals, it helps credit managers, data analysts, and financial institutions make informed decisions on lending strategies, customer targeting, and risk reduction.



---

## DAX Measures – Purpose and Output

Below are some of the key DAX measures created during the project, including rationale and interpretation of the results. These measures were instrumental in deriving business insights and powering the visualizations across the three report pages.

---

### 1. Average Income by Employment Type

```DAX
Average Income by Employment type = 
CALCULATE(
    AVERAGE('Loan_default'[Income]),
    ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
)
```

 This measure was designed to compare average income across different employment types (e.g., Full-Time, Part-Time, Self-Employed).  
**What it shows:** Self-Employed applicants have significantly lower average income, which correlates with higher default rates.

---

### 2. Average Loan by Age Group

```DAX
Average Loan by Age Group = 
AVERAGEX(
    VALUES('Loan_default'[Age Group]),
    AVERAGE('Loan_default'[LoanAmount])
)
```

To assess loan distribution by age group and identify which demographic borrows the most.  
**Insight:** Middle Age Adults (30–45) tend to borrow the highest loan amounts, while younger borrowers request smaller loans.

---

### 3. Default Rate by Employment Type

```DAX
Default Rate by Employment Type = 
VAR totalrecords = COUNTROWS(ALL('Loan_default'))
VAR defaultcases = COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE()))
RETURN 
    CALCULATE(
        DIVIDE(defaultcases, totalrecords), 
        ALLEXCEPT('Loan_default', 'Loan_default'[EmploymentType])
    ) * 100
```

To evaluate which employment category is riskier in terms of defaults.  
**Insight:** Part-time and Self-Employed categories show notably higher default percentages, signaling a credit risk concern.

---

### 4. Default Rate per Year

```DAX
Default Rate per Year = 
VAR total_loans = 
    CALCULATE(
        COUNTROWS('Loan_default'),
        ALLEXCEPT('Loan_default', 'Loan_default'[Year])
    )
VAR default = 
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())),
        ALLEXCEPT('Loan_default', 'Loan_default'[Year])
    )
RETURN 
    DIVIDE(default, total_loans) * 100
```

This was used to track how default rates evolved yearly.  
**Insight:** The trend showed an increase in defaults over the last two years, despite stable loan issuance volumes.

---

### 5. Year-over-Year (YoY) Default Loan Change

```DAX
YoY Default Loans Change = 
DIVIDE(
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))
    )
    - CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ),
    CALCULATE(
        COUNTROWS(FILTER('Loan_default', 'Loan_default'[Default] = TRUE())), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ), 0
) * 100
```

To understand year-over-year shifts in loan default volume.  
**Insight:** A sharp 7.9% increase in default volume was observed in the last calendar year.

---

### 6. YoY Loan Amount Change

```DAX
YoY Loan Amount Change = 
DIVIDE(
    CALCULATE(
        SUM('Loan_default'[LoanAmount]), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY]))
    )
    - CALCULATE(
        SUM('Loan_default'[LoanAmount]), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ),
    CALCULATE(
        SUM('Loan_default'[LoanAmount]), 
        'Loan_default'[Year] = YEAR(MAX('Loan_default'[Loan_Date_DD_MM_YYYY])) - 1
    ), 0
) * 100
```

To monitor annual changes in disbursed loan volume.  
**Insight:** Loan disbursements increased by 11.6%, indicating higher borrowing demand or looser underwriting standards.

---

### 7. Median Loan Amount by Credit Score Bin

```DAX
Median by Credit Score Bins = 
MEDIANX('Loan_default', 'Loan_default'[LoanAmount])
```

This measure was used to assess loan distributions by creditworthiness brackets.  
**Insight:** Higher credit score bins were associated with larger median loans, aligning with responsible borrowing patterns.

---

### 8. Total Loan by Middle Age Adults

```DAX
Total Loan(Middle Age Adults) = 
SUMX(
    FILTER('Loan_default', 'Loan_default'[Age Group] = "Middle Age Adults"),
    'Loan_default'[LoanAmount]
)
```

To isolate the segment that contributes the most to the loan portfolio.  
**Insight:** Middle-aged borrowers accounted for 53% of the total loan disbursement volume.

---

This rich set of measures provides both **descriptive** and **predictive** power to the dashboard, helping stakeholders to:

- Quantify borrower risk
- Profile demographics
- Track lending performance
- Strategize for better credit policy

