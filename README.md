# WORLD-LAYOFFS-SQL-DATA-CLEANING-PROJECT

## Table of Contents

1. [Introduction](#introduction)
2. [Objectives](#objectives)
3. [Project Structure](#project-structure)
    - [Remove Duplicates](#remove-duplicates)
    - [Standardize the Data](#standardize-the-data)
    - [Handle Null or Blank Values](#handle-null-or-blank-values)
    - [Remove Unnecessary Columns](#remove-unnecessary-columns)
4. [SQL Queries](#sql-queries)
    - [Removing Duplicates](#removing-duplicates)
    - [Standardizing Data](#standardizing-data)
    - [Handling Null Values](#handling-null-values)
    - [Removing Columns](#removing-columns)
5. [Final Output](#final-output)

## Introduction

This repository contains a detailed guide for cleaning and preparing data using SQL. The project focuses on ensuring data quality by addressing duplicates, standardizing data, handling missing values, and streamlining the dataset for analysis. All operations were performed on a staging table to preserve the original data.

## Objectives 

1. Preserve the original dataset by working on a duplicate staging table.
2. Remove duplicate records to streamline the dataset.
3. Standardize column values for consistency, including trimming whitespace and formatting dates.
4. Address missing or blank values by propagating or replacing them with appropriate data.
5. Eliminate unnecessary columns to make the dataset focused and efficient.

## Project Structure

### Remove Duplicates

- **Create a Duplicate Table**: Ensures the original data remains intact.

    ```sql
    CREATE TABLE layoffs_staging LIKE layoffs;
    INSERT INTO layoffs_staging SELECT * FROM layoffs;
    ```

- **Identify Duplicates**: Use a Common Table Expression (CTE) to assign row numbers to duplicate entries.

    ```sql
    WITH duplicate_cte AS (
        SELECT *, ROW_NUMBER() OVER (
            PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
        ) AS row_num
        FROM layoffs_staging
    )
    SELECT * FROM duplicate_cte WHERE row_num > 1;
    ```

- **Remove Duplicates**: Delete duplicate rows while keeping one unique record per entry.

    ```sql
    DELETE FROM layoffs_staging WHERE row_num > 1;
    ```

### Standardize the Data

- **Trim Whitespace**: Clean up unwanted spaces in the `company` column.

    ```sql
    UPDATE layoffs_staging2 SET company = TRIM(company);
    ```

- **Normalize Industry Names**: Ensure consistent naming conventions for industries.

    ```sql
    UPDATE layoffs_staging2 SET industry = 'Crypto' WHERE industry LIKE 'Crypto%';
    ```

- **Standardize Country Names**: Remove trailing periods from country names.

    ```sql
    UPDATE layoffs_staging2 SET country = TRIM(TRAILING '.' FROM country);
    ```

- **Format Dates**: Convert date strings into a uniform `DATE` format.

    ```sql
    UPDATE layoffs_staging2 SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
    ALTER TABLE layoffs_staging2 MODIFY COLUMN `date` DATE;
    ```

### Handle Null or Blank Values

- **Identify Missing Values**: Locate rows with null or blank values in key columns.

    ```sql
    SELECT * FROM layoffs_staging2 WHERE total_laid_off IS NULL OR industry = '';
    ```

- **Replace Blank Values with NULL**: Standardize blank values by setting them to `NULL`.

    ```sql
    UPDATE layoffs_staging2 SET industry = NULL WHERE industry = '';
    ```

- **Propagate Non-Null Values**: Fill missing `industry` values using related rows.

    ```sql
    UPDATE layoffs_staging2 t1
    JOIN layoffs_staging2 t2
        ON t1.company = t2.company
    SET t1.industry = t2.industry
    WHERE t1.industry IS NULL AND t2.industry IS NOT NULL;
    ```

### Remove Unnecessary Columns

- Drop temporary columns that are no longer needed.

    ```sql
    ALTER TABLE layoffs_staging2 DROP COLUMN row_num;
    ```


## SQL Queries

### Removing Duplicates

```sql
CREATE TABLE layoffs_staging LIKE layoffs;
INSERT INTO layoffs_staging SELECT * FROM layoffs;

WITH duplicate_cte AS (
    SELECT *, ROW_NUMBER() OVER (
        PARTITION BY company, location, industry, total_laid_off, percentage_laid_off, `date`, stage, country, funds_raised_millions
    ) AS row_num
    FROM layoffs_staging
)
DELETE FROM layoffs_staging WHERE row_num > 1;

### Standardizing Data

```sql
UPDATE layoffs_staging2 SET company = TRIM(company);
UPDATE layoffs_staging2 SET industry = 'Crypto' WHERE industry LIKE 'Crypto%';
UPDATE layoffs_staging2 SET country = TRIM(TRAILING '.' FROM country);
UPDATE layoffs_staging2 SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');
ALTER TABLE layoffs_staging2 MODIFY COLUMN `date` DATE;

### Handling Null Values

```sql
UPDATE layoffs_staging2
SET industry = NULL
WHERE industry = '';

UPDATE layoffs_staging2 t1
JOIN layoffs_staging2 t2
    ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL AND t2.industry IS NOT NULL;

### removing columns

```sql
ALTER TABLE layoffs_staging2 DROP COLUMN row_num;

