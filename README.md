# Bank Marketing Campaign Analysis

## Brief One-Line Summary

Exploratory data analysis of 41,176 telemarketing calls from a Portuguese bank to uncover which customers, contact strategies and campaign patterns lead to term deposit subscriptions.

---

## Overview

Banks run large telemarketing campaigns to sell products such as term deposits, but most calls do not result in a sale. Each call costs time and money, so understanding who says "yes", and under which conditions, can make campaigns far more efficient.

This project analyses a real-world bank marketing dataset using Python. It cleans the raw data, handles placeholder and duplicate values, and answers a series of business questions through summary statistics and visualisations. The results show how conversion varies by customer profile, contact method, call frequency, campaign history and month of contact.

---

## Problem Statement

The bank wants to improve the return on its telemarketing campaigns. This project answers the following questions:

1. What proportion of customers subscribed to a term deposit?
2. How does customer age relate to the other numeric variables in the data?
3. What is the distribution of job types among the customers contacted?
4. What is the average duration of a call?
5. What proportion of calls were made in each month?
6. How many contacts does it take to convert a customer, and does repeated calling still pay off?
7. Which customer segments (job, age group, previous campaign outcome, contact type) subscribe at the highest rates?

---

## Dataset

**File:** `bank-additional-full.xlsx`
**Source:** UCI Machine Learning Repository, Bank Marketing dataset (Portuguese banking institution).
**Size:** 41,188 rows and 21 columns in the raw file (41,176 rows after removing duplicates).
**Target variable:** `y` (renamed `subscribed` in the project): whether the customer subscribed to a term deposit (`yes` / `no`).

> Note: the dataset description supplied with the project also mentions fields such as `balance` and `day`, which belong to the related `bank-full` version. This project uses `bank-additional-full`, which contains call details and economic indicators instead, so age is compared with the numeric variables that are actually available.

### Raw data columns

| Group | Column | Description |
|---|---|---|
| Customer profile | `age` | Age of the customer (numeric) |
| | `job` | Type of job (12 categories, e.g. admin., blue-collar, student, retired) |
| | `marital` | Marital status |
| | `education` | Education level |
| | `default` | Has credit in default? (yes / no / unknown) |
| | `housing` | Has a housing loan? |
| | `loan` | Has a personal loan? |
| Last contact of the current campaign | `contact` | Communication type (cellular or telephone) |
| | `month` | Month of last contact |
| | `day_of_week` | Day of the week of last contact |
| | `duration` | Duration of last call in seconds |
| Campaign history | `campaign` | Number of contacts made to this customer during the current campaign |
| | `pdays` | Days since the customer was last contacted in a previous campaign (999 means never contacted) |
| | `previous` | Number of contacts made before the current campaign |
| | `poutcome` | Outcome of the previous campaign (success, failure, nonexistent) |
| Economic indicators | `emp.var.rate` | Employment variation rate (quarterly) |
| | `cons.price.idx` | Consumer price index (monthly) |
| | `cons.conf.idx` | Consumer confidence index (monthly) |
| | `euribor3m` | Euribor 3-month interest rate (daily) |
| | `nr.employed` | Number of employees (quarterly) |
| Target | `y` | Subscribed to a term deposit? (yes / no) |

### Problems found in the raw data

| Problem | Details | How it was handled |
|---|---|---|
| Duplicate rows | 12 fully duplicated records | Removed with `drop_duplicates()`, leaving 41,176 rows |
| Placeholder value in `pdays` | 999 is used to mean "never contacted before" (39,673 rows in the raw data), not an actual number of days | Created a `previously_contacted` yes/no flag, then replaced 999 with `NaN` so averages are not distorted. Only 1,515 customers had a real previous contact |
| `unknown` categories | Missing information is stored as the text "unknown" rather than blank, for example in `default` (8,597), `education` (1,731), `job` (330), `housing` and `loan` (990 each) and `marital` (80) | Kept as their own category, so no records were lost and the analysis stays transparent |
| Class imbalance | Only about 11% of customers subscribed | Reported as a finding; rates (not raw counts) are used when comparing groups |
| Skewed numeric fields | Call `duration` reaches 4,918 seconds and `campaign` reaches 56 contacts | Median is reported next to the mean, and contact counts are capped at 10 in the contact charts |
| Unclear column name | The target was called `y` | Renamed to `subscribed`, and a 0/1 version (`sub_num`) was created for rate calculations |
| Excel format | Data is supplied as `.xlsx` | Loaded with `pd.read_excel()` |

---

## Tools and Technologies

| Tool | Purpose |
|---|---|
| Python 3.11 | Core programming language |
| pandas | Data loading, cleaning and aggregation |
| NumPy | Numerical operations and conditional logic |
| Matplotlib | Charts |
| Seaborn | Statistical visualisation |
| openpyxl | Reading the Excel dataset |
| Jupyter Notebook | Interactive analysis environment |

---

## Methods

1. **Data loading and inspection:** loaded the Excel file and checked its shape, data types and summary statistics.
2. **Data quality checks:** counted missing values (none) and duplicates (12) before changing anything.
3. **Cleaning:** removed the duplicates, converted the `pdays` placeholder (999) into a missing value, added a `previously_contacted` flag and renamed the target to `subscribed`.
4. **Feature preparation:** created a numeric target (`sub_num`, 1 for yes and 0 for no) and grouped ages into bands (`<25`, `26-35`, `36-45`, `46-55`, `56-65`, `65+`).
5. **Descriptive analysis:** calculated the subscription proportion, call duration statistics, and the correlation of age with the other numeric variables.
6. **Visual analysis:** produced bar charts, a pie chart and a segment dashboard to compare counts and subscription rates across groups.
7. **Interpretation:** turned each result into a business insight.

---

## Key Insights

### 1. Overall conversion is low

Only **11.3%** of customers (4,639 of 41,176) subscribed to a term deposit, while 88.7% declined. Because so few calls succeed, targeting matters more than call volume.

![Proportion of customers who subscribed](https://github.com/kaursamanpreet970-pixel/Bank-Marketing-Campaign-Analysis-EDA/blob/main/01_subscription_proportion.png)

### 2. Age has no strong linear relationship with other numeric variables

Age shows no meaningful correlation with any numeric variable. The strongest is consumer confidence index at about +0.13, which is still weak.

### 3. Three job groups make up most of the customer base

Admin (10,419), blue-collar (9,253) and technician (6,739) roles account for about **64%** of customers contacted. Students, unemployed customers and the "unknown" group are small.

![Distribution of job type](https://github.com/kaursamanpreet970-pixel/Bank-Marketing-Campaign-Analysis-EDA/blob/main/02_job_distribution.png)

### 4. Calls are short, with a few very long ones

The average call lasts **4.3 minutes (258 seconds)**, but the median is only **3 minutes (180 seconds)**. A small number of very long calls, up to about 82 minutes, pull the average up.

### 5. The campaign is strongly seasonal

May alone accounts for **33.4%** of all calls, and May to August together for about **79%**. No calls were made in January or February.

![Calls by month](https://github.com/kaursamanpreet970-pixel/Bank-Marketing-Campaign-Analysis-EDA/blob/main/03_calls_by_month.png)

### 6. Most subscribers say yes early, but this is partly a volume effect

Of the subscribers contacted ten times or fewer, 2,299 subscribed on the first contact and the numbers fall with each extra call. This chart shows counts, and most customers were only called once or twice, so the next chart uses rates.

![Subscribers by number of contacts](https://github.com/kaursamanpreet970-pixel/Bank-Marketing-Campaign-Analysis-EDA/blob/main/04_subscribers_by_contacts.png)

### 7. Repeated calling has diminishing returns

The success rate is **13.0%** on the first contact and falls to about **5%** by the tenth. Capping contact attempts would free up effort for better-fit customers.

![Subscription rate by number of contacts](https://github.com/kaursamanpreet970-pixel/Bank-Marketing-Campaign-Analysis-EDA/blob/main/05_rate_by_contacts.png)

### 8. Some customer segments convert far better than others

- **Job:** students (31.4%) and retired customers (25.3%) convert best; blue-collar customers (6.9%) convert least.
- **Age:** customers over 65 (46.9%) and 25 or under (21.0%) convert far above the 8% to 12% seen among ages 26 to 55.
- **Previous outcome:** customers who subscribed in an earlier campaign convert at **65.1%**, against 8.8% for customers never contacted before.
- **Contact type:** cellular contacts convert at 14.7%, nearly three times the 5.2% of landline (telephone) contacts.

![Subscription rate by job, age group, previous outcome and contact type](https://github.com/kaursamanpreet970-pixel/Bank-Marketing-Campaign-Analysis-EDA/blob/main/06_segment_subscription_rates.png)

---

##  Model / Output

| `banking_project.ipyn` | Complete, reproducible analysis notebook with code, results and written insights |
| `images/` | The six charts used in this README |
| Project PDF | Notebook exported as a PDF for easy sharing | 

---

## Results and Conclusion

**Results**

- Overall conversion is low at 11.3%, so improving targeting has more impact than increasing call volume.
- Conversion falls from 13.0% at the first contact to about 5% by the tenth, so repeated calling brings diminishing returns.
- The highest-converting groups are previous campaign successes (65.1%), customers over 65 (46.9%), students (31.4%), retirees (25.3%) and cellular contacts (14.7%).
- Activity is concentrated in May to August, which suggests the campaign calendar could be reviewed.

**Recommendations**

1. Prioritise customers who subscribed in earlier campaigns, as well as retired, student and older customers.
2. Set a limit on contact attempts per customer, since returns drop steadily after the first few calls.
3. Favour cellular contact over landline where possible.
4. Review the heavy concentration of calls in May to August.

**Limitations and next steps**

- This is a descriptive analysis; it shows associations, not causes.
- Some segments are small (for example, only 618 customers are over 65), so their rates are less stable than those of large groups.
- Call `duration` is only known after a call ends, so it should not be used to predict outcomes in advance; here it is used for description only.
- Next steps: build a classification model (such as logistic regression or a random forest) to score customers by likelihood to subscribe, and create an interactive Power BI dashboard.

---

## Contact

**SAMANPREET KAUR**

- LinkedIn: [linkedin.com/in/your-profile](hhttps://www.linkedin.com/in/kaursamanpreet/)
- Email: [your.email@example.com](mailto:kaursamanpreet970@outlook.com)
- GitHub: [github.com/your-username](https://github.com/kaursamanpreet970-pixel)
