# Prism-insurance-analytics-dashboard # Insurance-Dashboard (Prism Insurance Pvt. Ltd.)


## Problem Statement

This dashboard helps **Prism Insurance Pvt. Ltd.** understand its policyholders and claims better. It gives the company a single view of how much premium is being collected, how much coverage is being underwritten, and how much is being paid out or held pending in claims. By breaking this down across policy type, gender, age group, and claim status, the business can spot which policy lines carry the most risk/exposure and where claims are getting stuck (rejected vs. settled vs. pending), and can track how many policies are currently active versus inactive.

Since **Rejected** claims (~43.5%) outnumber **Settled** (~33.8%) and **Pending** (~22.6%) claims, the claims-review process is an area the company should investigate further. Also, **Travel** policies bring in the highest premium (2.5M) and carry the highest coverage exposure of any policy type, so it is the line of business most worth monitoring closely.

## Steps followed

- **Step 1:** Downloaded and installed **MSSQL Server** (and SSMS) as the data source engine for this project.
- **Step 2:** Imported the raw insurance CSV data into MSSQL Server as a table.
- **Step 3:** Loaded the data from MSSQL Server into Power BI Desktop using the SQL Server connector.
- **Step 4:** In the Power Query Editor, used **Table View** and **Data Profiling** (column distribution, column quality, column profile — set to "column profiling based on entire dataset") to check for errors, blanks, and nulls. The only column with missing values was **ClaimDate** (blank whenever a claim status is "Rejected", since a rejected claim has no settlement/processing date) and consequently **ClaimAmount** is 0 for those rows.
- **Step 5:** In the report view, a theme was applied and two text boxes were added for the company name **"PRISM INSURANCE PVT. LTD."** and its tagline.
- **Step 6:** **Slicers** were added for **CustomerID**, **PolicyNumber**, and **ClaimNumber** so the report can be filtered down to any individual policy or customer.
- **Step 7:** Three **Card visuals** were added to the canvas — **Premium Amount**, **Coverage Amount**, and **Claim Amount** — using the DAX measures below.
- **Step 8:** A **Multi-row card** was added to show the customer count split by **Gender** (Male / Female).
- **Step 9:** A **Ribbon chart** ("Count of Active/Inactive") was added showing the proportion of policies that are currently active vs. inactive, based on whether the **PolicyEndDate** is in the future relative to today's date.
- **Step 10:** A **Bar chart** ("Premium Amount by PolicyType") was added to compare premium collected across the five policy types (Travel, Health, Auto, Life, Home).
- **Step 11:** A **Line chart** ("Claim Amount by Age Group") was added after creating a calculated **Age Group** column (Young Adult / Adult / Elder), to see how claim amounts trend across customer age brackets.
- **Step 12:** A **Donut chart** ("No. of Claims by Claim Status") was added to show the count of claims split across Pending, Rejected, and Settled.
- **Step 13:** A **Matrix visual** was added showing **Coverage Amount** broken down by **PolicyType** (rows) and **ClaimStatus** (columns), with row/column totals, to see where the underwriting exposure sits across each combination.
- **Step 14:** The report was published to **Power BI Service**.
- **Step 15:** A **scheduled refresh** was configured so the dataset stays up to date automatically from the MSSQL Server source.
- **Step 16:** A **drill-through page/filter** was set up so a user can right-click a policy type (or other field) and drill through to a detailed, filtered view.

## DAX Measures Used

```DAX
Premium Amount = SUM(InsuranceData[PremiumAmount])

Coverage Amount = SUM(InsuranceData[CoverageAmount])

Claim Amount = SUM(InsuranceData[ClaimAmount])

Age Group =
IF(InsuranceData[Age] <= 30, "Young Adult",
    IF(InsuranceData[Age] <= 60, "Adult", "Elder"))
```

## Snapshot of Dashboard (Power BI Desktop)

![Dashboard Snapshot](https://github.com/user-attachments/assets/50ca5f09-c1ce-469c-a34c-28462e06af20)

## Snapshot of Underlying Data (Power Query / Table View)

![Data Snapshot](https://github.com/user-attachments/assets/ea409fd5-b486-4ef9-a39f-92f04e9ae4a5)

# Insights

A single-page report was created in Power BI Desktop and published to Power BI Service.

### [1] Total Customers = 10,004

- Male customers: 5,003 (50.02%)
- Female customers: 5,001 (49.98%)

  Gender split is almost perfectly even across the customer base.

### [2] Key Totals (Card Visuals)

- **Premium Amount** = 5.98M
- **Coverage Amount** = 600.55M
- **Claim Amount** = 16.91M

### [3] Policy Status

- **Active policies**: ~74.91%
- **Inactive policies**: ~25.09%

  (Active/Inactive is based on whether the policy's end date is still in the future relative to today, so this ratio will shift over time as more policies expire.)

### [4] Premium Amount by Policy Type

| Policy Type | Premium Amount |
|---|---|
| Travel | 2.5M |
| Health | 1.2M |
| Auto | 1.0M |
| Life | 0.7M |
| Home | 0.6M |

Travel is the largest policy line by premium collected, followed by Health and Auto.

### [5] Claims by Claim Status

- **Rejected**: 4,355 claims (~43.5%)
- **Settled**: 3,386 claims (~33.9%)
- **Pending**: 2,263 claims (~22.6%)

Rejected claims outnumber both Settled and Pending claims, which is worth investigating further (e.g., common rejection reasons).

### [6] Claim Amount by Age Group

- **Adult**: 8.8M
- **Elder**: 6.4M
- **Young Adult**: 1.7M

Adult policyholders account for the largest share of claim payouts, followed by Elder customers.

### [7] Coverage Amount by Policy Type & Claim Status (Matrix)

| Policy Type | Pending | Rejected | Settled | Total |
|---|---|---|---|---|
| Auto | 20,810,615.30 | 40,671,711.59 | 32,984,558.70 | 94,466,885.59 |
| Health | 27,682,791.20 | 52,401,928.42 | 40,017,100.67 | 120,101,820.29 |
| Home | 13,001,816.73 | 27,406,202.63 | 20,645,568.43 | 61,053,587.79 |
| Life | 17,259,587.93 | 33,722,751.49 | 23,121,204.63 | 74,103,544.05 |
| Travel | 57,247,694.90 | 107,395,611.59 | 86,182,353.59 | 250,825,660.08 |
| **Total** | **136,002,506.05** | **261,598,205.64** | **202,950,786.03** | **600,551,497.72** |

Travel carries by far the largest coverage exposure of any policy type, and within every policy type, "Rejected" coverage amounts are consistently the largest bucket — reinforcing that claim rejections are the area most worth addressing.
