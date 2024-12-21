# Tech Layoffs Post-COVID: A Comprehensive SQL Analysis (2020-2024)


## 🎯 Objectives 
This project aims to analyze tech industry layoffs from 2020 to 2024 using SQL to uncover key patterns and insights. It identifies trends such as peak layoff periods, and most affected industries, companies, and countries while enabling a better understanding of the post-COVID impact on workforce dynamics. The ultimate goal is to provide actionable insights for workforce management. 

### KEY COMPONENTS

### Data Cleaning and Standardization:
- Address duplicates, null values, and inconsistent formatting for accurate analysis.
  
### Exploratory Analysis:
- Identify peak layoff periods, regions, industries, and funding stages most affected.
- Detect trends in layoffs by month and year.

### Insight Delivery:
- Highlight actionable patterns, such as resilience in certain industries and regions.

## 📂 Data Source
- Dataset: Tech Layoffs Dataset
- Source: Kaggle: Tech Layoffs Dataset
- Contents: Details on layoffs, including Company, Location_HQ, Industry, Laid_Off_Count, Date, Source, Funds_Raised, Stage, Date_Added, Country, Percentage, List_of_Employees_Laid_Off
- The cleaned dataset is available here.

## 🛠️ Process

### 1️⃣ Data Cleaning

**Used SQL operations to**:

- Remove duplicates (ROW_NUMBER, CTE).
- Standardize inconsistent entries (TRIM, replacement of special characters).
- Handle null values by replacing or removing redundant records.
  
### 2️⃣ Data Transformation

- Converted columns to appropriate data types (e.g., dates, numeric formats).
- Aggregated data by regions, industries, and funding stages.
  
### 3️⃣ Exploratory Data Analysis (EDA)

- Identified trends using SQL functions:
- Rolling totals (SUM() OVER()).
- Ranking (DENSE_RANK, ROW_NUMBER).
- Year-over-year comparisons and percentage calculations.


## EXECUTIVE SUMMARY 

The Tech Layoffs Analysis (2020-2024) highlights major trends in workforce reductions across industries, Countries, and companies post-COVID.

Retail led layoffs (67,368; 12.76%), followed by Consumer (63,814; 12.09%) and Transportation (57,913; 10.96%).
The U.S. dominated layoffs (367,830; 69.66%), with India (8.93%) and Germany (4.80%) trailing.
January 2023 saw 13.43% of layoffs, with 2023 recording the highest annual layoffs (40.26%).
Amazon (27,840), Meta (21,000), and Tesla (14,500) led layoffs.
Layoffs peaked in 2023 (+40.17% YoY) but declined sharply in 2024 (-63.69%), signaling recovery.

- The entire sql queries used for this analysis can be found [here]((https://github.com/Yakubu-Moshood/WORLD-LAYOFFS-SQL-DATA-CLEANING-PROJECT/blob/main/%23%23%20Project%20Structure%20for%20WORLD%20TECH%20LAYOFFS.txt))

💡 Insights at a Glance

### 1️⃣ Industries Most Affected

The RETAIL industry accounted for 67,368 (12.76%) of total layoffs, making it the hardest-hit sector.

CONSUMER followed, with 63,814 employees laid off, contributing 12.09% of layoffs, reflecting reduced consumer demand.

Rounding up the top 3 industries mostly affected is TRANSPORTATION which laid off 57,913, accounting for 10.96% of the total layoffs

### 2️⃣ Country Trends

The United States led globally, with 367,830 employees laid off, making up 69.66% of the total layoffs. 

Following in a very distant second is India, with 47,127 which is 8.93% of total layoffs. 

GERMANY rounds up the top 3, laying off 25,345 employees totaling 4.80% of layoffs.
  
### 3️⃣ Peak Layoff Period
The peak layoff month was January 2023, when 70,935 (13.43%) employees were freed of their jobs. This may have been driven by company restructuring after the holiday season. 

The Year 2023 in general has a total of 212,585 laid off which is 40.26% of the total layoffs making it the year with the highest layoffs in years starting from 2020 to 2024

### 4️⃣ Companies with Maximum Layoffs

Amazon reported the largest layoff count, parting ways with 27,840 employees, representing 5.27% of all layoffs. 

Following closely is Meta, which laid off 21,000 employees, representing 3.98% of the overall total. 

Tesla is next. It also faced substantial reductions, cutting 14,500 jobs, accounting for 2.75% of total layoffs. 

These figures underscore the significant workforce adjustments among leading tech companies during this period.


### 🔑 Key Metrics

**Year-over-Year Layoff Growth**


| Year | Total_Layoffs | Previous_Year_Layoffs | YoY_Increment | YoY_Percentage_Change |
|------|---------------|-----------------------|---------------|-----------------------|
| 2020 | 70755         | NULL                  | NULL          | NULL                  |
| 2021 | 15810         | 70755                 | -54945        | -77.66%               |
| 2022 | 151657        | 15810                 | 135847        | 859.25%               |
| 2023 | 212585        | 151657                | 60928         | 40.17%                |
| 2024 | 77194         | 212585                | -135391       | -63.69%               |










