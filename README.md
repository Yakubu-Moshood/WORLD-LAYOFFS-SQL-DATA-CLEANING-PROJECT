# Covid_&_Post_COVID_Tech_Layoffs_SQL_Analysis

## 🚀 Introduction/Project Overview

The COVID-19 pandemic profoundly impacted the global economy, and the tech industry was no exception. This project examines the layoff trends in the tech sector from 2020 to 2024 using SQL to uncover patterns, highlight industries and companies most affected, and provide actionable insights.

## 🎯 Key Objectives
### 1. Data Cleaning:

- Remove duplicates and inconsistencies if any. 
- Standardize data for accuracy and uniformity.
- Handle null and blank values effectively.
- Eliminate unnecessary columns to reduce redundancy.

### 2. Exploratory Data Analysis (EDA):

- Identify industries, countries, and companies most affected by layoffs.
- Analyze trends over time (monthly, yearly).
- Rank companies and industries by layoff impact.
  
### 3. Insights Delivery:

- Highlight key metrics like maximum layoffs, total layoffs by country, and rolling trends.

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

💡 Insights at a Glance
### 1️⃣ Industries Most Affected

- 1. The RETAIL industry saw the highest layoffs, accounting for 67,368 employees laid off which is 12.76% of the total layoffs.
- 2. This is closely followed by CONSUMER which laid off 63,814 employees, 12.09% of total layoffs. 
- 3. Rounding up the top 3 industries mostly affected is TRANSPORTATION which laid off 57,913, accounting for 10.96% of the total layoffs

### 2️⃣ Country Trends

- 1. 367,830 employees were laid off in The USA. This accounted for 69.66% of layoffs
  2. INDIA is the second on the list, laying off 47,127 workers, amounting to 8.93% of layoffs
  3. GERMANY rounds up the top 3, laying off 25,345 employees which totals 4.80% of layoffs.
  
### 3️⃣ Peak Layoff Period
- 1. Layoffs peaked in January 2023 with 70935 layoffs recorded. This is 13.43% of total layoffs.
  2. Year 2023 in general has a total of 212,585 laid off which is 40.26% of the total layoffs making it the year with the highest layoffs in years starting from 2020 to 2024


### 4️⃣ Companies with Maximum Layoffs

 - 1. Amazon has 27,840 employees laid off. This number amounts to 5.27% of total layoffs.
   2. Meta is next on the list with 21,000 of their employees laid off. This is 3.98% of total layoffs
   3. Making up the  top 3 is Tesla which laid off 14,500 employees which is 2.75 of total layoffs. 
