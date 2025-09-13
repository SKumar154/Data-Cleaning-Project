# 🗃️ Layoffs Data Cleaning (MySQL) - Data Analytics Project

A comprehensive data cleaning project using **MySQL** to transform a raw global layoffs dataset into a structured and reliable format for analysis.

---

## 🚀 Key Features

- **🔍 Duplicate Removal** – Identified and removed duplicate rows using window functions
- **📝 Data Standardization** – Unified inconsistent company, industry, and country names  
- **📅 Date Formatting** – Converted text-based dates into proper `DATE` type
- **🔧 Null Handling** – Replaced blanks with NULL and filled missing values where possible
- **🗑️ Irrelevant Data Removal** – Deleted rows and columns with no useful information

---

## 📋 Prerequisites

Before running this project, ensure you have:

- MySQL (v8.0 or higher recommended)
- SQL client (MySQL Workbench, DBeaver, etc.)
- Basic understanding of SQL

---

## 📷 Before vs After Cleaning

### 🔴 **Before Cleaning (Messy Data)**

| company | location | industry | total_laid_off | percentage_laid_off | date | country |
|---------|----------|----------|----------------|-------------------|------|---------|
| Casper | New York | E-Commerce | 100 | 20% | 03/15/2020 | United States. |
| Coinbase | San Fran | Crypto Currency | NULL | NULL | 7/1/21 | United States |
| **Casper** | **New York** | **E-Commerce** | **100** | **20%** | **03/15/2020** | **United States.** |
| Bally Tech | Las Vegas |  | 50 | 10% | 12/10/2022 | U.S.A |

### 🟢 **After Cleaning (Refined Data)**

| company | location | industry | total_laid_off | percentage_laid_off | date | country |
|---------|----------|----------|----------------|-------------------|------|---------|
| Casper | New York | E-Commerce | 100 | 20% | 2020-03-15 | United States |
| Coinbase | San Francisco | Crypto | 1200 | 18% | 2021-07-01 | United States |
| Bally Tech | Las Vegas | Gaming | 50 | 10% | 2022-12-10 | United States |

### 📋 **Issues Identified & Fixed**

**🔴 Problems in Raw Data:**
- **Duplicate rows** (Casper entry repeated twice)
- **Inconsistent industry names** (`Crypto Currency` → `Crypto`)
- **Inconsistent country formats** (`United States.`, `U.S.A` → `United States`)
- **Abbreviated locations** (`San Fran` → `San Francisco`)
- **Text-based date formats** (`03/15/2020`, `7/1/21`)
- **Missing/blank industry values**
- **NULL values** in critical fields

**🟢 Improvements Made:**
- ✅ **Duplicates removed** completely
- ✅ **Standardized industry names** across dataset  
- ✅ **Unified country naming** convention
- ✅ **Consistent location formatting**
- ✅ **Proper DATE format** (`YYYY-MM-DD`)
- ✅ **Missing values handled** appropriately
- ✅ **Data validation** implemented

---

## 🛠️ Data Cleaning Steps

### **Step 1: Create Working Copy**
*Created a backup table to preserve original data*

```sql
CREATE TABLE layoffs_dummy LIKE layoffs;
INSERT layoffs_dummy SELECT * FROM layoffs;
```

### **Step 2: Remove Duplicates**  
*Used window functions to identify and eliminate duplicate records*

```sql
WITH duplicate_CTE AS (
  SELECT *, ROW_NUMBER() OVER(
    PARTITION BY company, location, industry, total_laid_off,
    percentage_laid_off, `date`, stage, country, funds_raised_millions
  ) AS row_num
  FROM layoffs_dummy
)
DELETE FROM layoffs_remove_duplicates WHERE row_num > 1;
```

### **Step 3: Standardize Text Fields**
*Cleaned up inconsistent naming in company, industry, and country columns*

```sql
UPDATE layoffs_dummy
SET industry = 'Crypto'
WHERE industry LIKE 'Crypto%';
```

### **Step 4: Fix Date Format**
*Converted text-based dates to proper MySQL DATE type*

```sql
UPDATE layoffs_dummy
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_dummy
MODIFY COLUMN `date` DATE;
```

### **Step 5: Handle Missing Values**
*Standardized blank fields to NULL for consistent data handling*

```sql
UPDATE layoffs_dummy
SET industry = NULL
WHERE industry = '';
```

### **Step 6: Remove Irrelevant Records**
*Deleted rows without meaningful layoffs information*

```sql
DELETE FROM layoffs_dummy
WHERE total_laid_off IS NULL AND percentage_laid_off IS NULL;
```

---

## 📁 Project Structure

```
layoffs_data_cleaning/
├── 📊 Layoffs_Data_Cleaning.xlsx   # Original dataset
├── 🔧 Data_Cleaning_MySQL.sql      # Complete SQL cleaning script
└── 📖 README.md                    # This documentation
```

---

## 📊 Project Outcomes

| **Improvement** | **Status** |
|----------------|------------|
| Duplicate removal | ✅ Complete |
| Text standardization | ✅ Complete |
| Date formatting | ✅ Complete |  
| Null handling | ✅ Complete |
| Data validation | ✅ Complete |

**Result:** Clean, consistent dataset ready for analysis and visualization

---

## 🎯 Next Steps

This cleaned dataset is now ready for:
- **📈 Exploratory Data Analysis**
- **📊 Dashboard Creation**  
- **🔍 Trend Analysis**
- **📋 Business Intelligence Reporting**

---

## 🙏 Acknowledgments

- Dataset inspired by global layoff trends analysis
- Built with **MySQL 8.0** database engine
- Project follows data cleaning best practices
