# Tech Layoffs Post-COVID: A Comprehensive SQL Analysis (2020-2024)


## 🎯 Objectives
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










## Project Structure

### Remove Duplicates

- **Create a Duplicate Table**: Ensures the original data remains intact.

    ```sql
    CREATE TABLE layoffs_duplicate LIKE layoffs;
    INSERT INTO layoffs_duplicate SELECT * FROM layoffs;
    ```

- **Identify Duplicates**: Use a Common Table Expression (CTE) to assign row numbers to duplicate entries.

    ```sql
    WITH duplicate_cte AS
( SELECT *, 
ROW_NUMBER() OVER (
	PARTITION BY Company, Location_HQ, Industry, Laid_Off_Count, `Date`, `Source`, Funds_Raised, Stage, 
    Date_Added, Country, Percentage, List_of_Employees_Laid_Off
    ) AS row_num
    FROM layoffs_duplicate)
SELECT * 
FROM duplicate_cte
WHERE row_num =1;
    ```

- **Remove Duplicates**: Delete duplicate rows while keeping one unique record per entry.

    ```sql
    DELETE FROM layoffs_duplicate WHERE row_num > 1;
    ```

### Standardize the Data

- **Trim Whitespace**: Clean up unwanted spaces in all the columns and update them.
  
```sql
SELECT company, TRIM(company)
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET company = TRIM(company);
```

```sql
SELECT DISTINCT Location_HQ
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET Location_HQ = 'Dusseldorf'
WHERE Location_HQ LIKE 'DÃ¼sseldorf';
 ```

```sql
UPDATE layoffs_duplicate
SET Location_HQ = 'Florianopolis'
WHERE Location_HQ LIKE 'FlorianÃ³polis';
```

```sql
UPDATE layoffs_duplicate
SET Location_HQ = 'Farde'
WHERE Location_HQ LIKE 'FÃ¸rde';
```

```sql
UPDATE layoffs_duplicate
SET Location_HQ = 'Malmo'
WHERE Location_HQ LIKE 'MalmÃ¶';
```

```sql
SELECT Location_HQ, TRIM(Location_HQ)
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET Location_HQ = TRIM(Location_HQ);
```

```sql
SELECT Industry, TRIM(industry)
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET industry = TRIM(industry);
```

```sql
SELECT Laid_Off_Count, TRIM(Laid_Off_Count)
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET Laid_Off_Count = TRIM(Laid_Off_Count);
```

```sql
SELECT STAGE, TRIM(stage)
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET Stage = TRIM(stage);
```

```sql
SELECT DISTINCT Country, TRIM(country)
FROM layoffs_duplicate;

UPDATE layoffs_duplicate
SET country = TRIM(country);
```

- **Conversion of data type**
```sql 
UPDATE layoffs_duplicate
SET Laid_Off_Count = NULL
WHERE Laid_Off_Count = '';

ALTER TABLE layoffs_duplicate
CHANGE COLUMN Laid_Off_Count Laid_Off_Count INT;
```

```sql
ALTER TABLE layoffs_duplicate
CHANGE COLUMN `date` `date` DATE;
```

```sql
UPDATE layoffs_duplicate
SET percentage = NULL
WHERE percentage = '';

ALTER TABLE layoffs_duplicate
CHANGE COLUMN percentage percentage DECIMAL(5,2);
```

- **Identifying and Handling Null and Blank Values**

```sql
SELECT *
FROM layoffs_duplicate
WHERE Percentage = ''
and Laid_Off_Count = '';
```

- Then, we delete them to reduce redundancy 

```sql
DELETE 
FROM layoffs_duplicate
WHERE Percentage = ''
AND Laid_Off_Count = ''
```

- Eliminate unnecessary columns to reduce redundancy.
```sql
ALTER TABLE layoffs_duplicate
DROP COLUMN `Source`,
DROP COLUMN List_of_Employees_Laid_Off;
```

### 2. Exploratory Data Analysis (EDA):

- Identify Companies, Industries, and countries, most affected by layoffs.
  
```sql
SELECT Company, SUM(Laid_Off_Count)
FROM layoffs_duplicate
GROUP BY Company
ORDER BY SUM(Laid_Off_Count) DESC;
```
```sql
SELECT industry, SUM(Laid_Off_Count)
FROM layoffs_duplicate
GROUP BY Industry
ORDER BY SUM(Laid_Off_Count) DESC;
```

```sql
SELECT Country, SUM(Laid_Off_Count)
FROM layoffs_duplicate
GROUP BY Country
ORDER BY SUM(Laid_Off_Count) DESC;
```

- Analyze trends over time (monthly, yearly).

```sql
SELECT substring(`date`,1,7) AS `Month`, SUM(Laid_Off_Count)
FROM layoffs_duplicate
GROUP BY `Month`
ORDER BY `Month` ASC
;
```
```sql
SELECT YEAR(`date`), SUM(Laid_Off_Count)
FROM layoffs_duplicate
GROUP BY YEAR(`date`)
ORDER BY SUM(Laid_Off_Count) DESC;
```

- Rank companies and industries by layoff impact

```sql
WITH Company_Year (Company, industry, Years, Laid_Off_Count) AS
(
	SELECT Company, Industry, YEAR(`date`), SUM(Laid_Off_Count)
	FROM layoffs_duplicate
	GROUP BY Company, industry, YEAR(`date`)
    ), Company_Year_Rank AS
(SELECT *, 
DENSE_RANK() OVER (PARTITION BY years ORDER BY Laid_Off_Count  DESC) AS Ranking
FROM Company_Year
WHERE Years IS NOT NULL
)
SELECT *
FROM Company_Year_Rank
WHERE Ranking <=5
;
```

```sql
SELECT 
	Industry, 
    SUM(Laid_Off_Count) as total_laid_off,
	(SUM(Laid_Off_Count)/(SELECT SUM(Laid_Off_Count) FROM layoffs_duplicate)) * 100 as Percentage_layoff
FROM  layoffs_duplicate
GROUP BY industry
ORDER BY Percentage_layoff ASC;
```

```sql
SELECT 
	Country, 
    SUM(Laid_Off_Count) as total_laid_off,
	(SUM(Laid_Off_Count)/(SELECT SUM(Laid_Off_Count) FROM layoffs_duplicate)) * 100 as Percentage_layoff
FROM  layoffs_duplicate
GROUP BY country
ORDER BY Percentage_layoff DESC;
```

```sql
SELECT 
	Company, 
    SUM(Laid_Off_Count) as total_laid_off,
	(SUM(Laid_Off_Count)/(SELECT SUM(Laid_Off_Count) FROM layoffs_duplicate)) * 100 as Percentage_layoff
FROM  layoffs_duplicate
GROUP BY Company
ORDER BY Percentage_layoff DESC;

```

```sql
SELECT 
	SUBSTRING(`date`, 1, 7) AS `Month`, 
    SUM(Laid_Off_Count) as total_laid_off,
	(SUM(Laid_Off_Count)/(SELECT SUM(Laid_Off_Count) FROM layoffs_duplicate)) * 100 as Percentage_layoff
FROM  layoffs_duplicate
GROUP BY  `Month`
ORDER BY Percentage_layoff DESC;
```

```sql
SELECT 
	YEAR(`date`),
    SUM(Laid_Off_Count) as total_laid_off,
	(SUM(Laid_Off_Count)/(SELECT SUM(Laid_Off_Count) FROM layoffs_duplicate)) * 100 as Percentage_layoff
FROM  layoffs_duplicate
GROUP BY  YEAR(`date`)
ORDER BY Percentage_layoff DESC;
```


