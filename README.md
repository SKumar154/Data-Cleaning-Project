📊 Layoffs Data Cleaning (MySQL)

This project focuses on cleaning and preparing a global layoffs dataset using MySQL. The dataset contained duplicates, inconsistencies, formatting issues, and irrelevant data. Through a structured cleaning process, the dataset was transformed into a reliable format, ready for further analysis and visualization.

🔹 Key Cleaning Steps
1. Creating a Working Copy

To preserve the raw data, I created a duplicate table (layoffs_dummy) for all transformations.

CREATE TABLE layoffs_dummy LIKE layoffs;
INSERT layoffs_dummy SELECT * FROM layoffs;

2. Removing Duplicates

Used a window function (ROW_NUMBER()) to identify duplicate rows based on all key columns, and then deleted them.

WITH duplicate_CTE AS (
  SELECT *, 
         ROW_NUMBER() OVER(
           PARTITION BY company, location, industry, total_laid_off, 
                        percentage_laid_off, `date`, stage, country, funds_raised_millions
         ) AS row_num
  FROM layoffs_dummy
)
DELETE FROM layoffs_remove_duplicates
WHERE row_num > 1;

3. Standardizing Data

Removed leading/trailing spaces

UPDATE layoffs_dummy
SET company = TRIM(company);


Unified industry names (e.g., Crypto, Crypto Currency, Cryptocurrency → Crypto)

UPDATE layoffs_dummy
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';


Fixed country name inconsistencies

UPDATE layoffs_dummy
SET country = 'United States'
WHERE country LIKE 'United States%';


Converted date column from text to DATE format

UPDATE layoffs_dummy
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_dummy
MODIFY COLUMN `date` DATE;

4. Handling Null & Blank Values

Converted blanks to NULL for consistency.

UPDATE layoffs_dummy
SET industry = NULL
WHERE industry = '';


Filled missing industries where possible by referencing the same company’s valid records.

UPDATE layoffs_dummy t1
JOIN layoffs_dummy t2
  ON t1.company = t2.company
SET t1.industry = t2.industry
WHERE t1.industry IS NULL
  AND t2.industry IS NOT NULL;


Removed rows with no meaningful layoff data (both total_laid_off and percentage_laid_off were NULL).

DELETE FROM layoffs_dummy
WHERE total_laid_off IS NULL 
  AND percentage_laid_off IS NULL;

5. Removing Irrelevant Columns

Unnecessary helper columns (like duplicate row indexes) were dropped to keep the dataset clean and structured.

🔹 Outcome

✅ Clean, consistent dataset free of duplicates, blanks, and inconsistencies.
✅ Standardized industries, countries, and dates.
✅ Reliable dataset ready for data analysis & visualization (e.g., layoff trends by year, country, or industry).
