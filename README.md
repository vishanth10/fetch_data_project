# Fetch Data Analysis Summary

## 1. Data Quality Issues and Cleaning

### Issues Identified
1. **Missing Data**:
   - `BARCODE` (Transactions): 11.52% missing (5,762 missing out of 50,000 records).
   - `CATEGORY_4`, `MANUFACTURER`, `BRAND` (Products): Significant missing values, e.g., `CATEGORY_4` is 92.02% missing.
   - There isn’t a proper hierarchy between `CATEGORY_1`, `CATEGORY_2`, and `CATEGORY_3`.
   - `BIRTH_DATE` (Users): 3.68% missing (3,675 missing out of 100,000 records).

2. **Inconsistent Data Types**:
   - `FINAL_QUANTITY` (Transactions) contains non-numeric values such as `"zero"`.
   - Dates like `CREATED_DATE` and `PURCHASE_DATE` are stored as strings instead of datetime.
   - Primary keys and foreign keys like `BARCODE` have many NaN values, affecting data integrity.

3. **Ambiguous Values**:
   - `FINAL_QUANTITY`: It is unclear if `"zero"` indicates an actual quantity of 0 or an error.

### Cleaning Steps
1. Converted date columns (`CREATED_DATE`, `PURCHASE_DATE`, `SCAN_DATE`) to datetime format.
2. Replaced ambiguous values in `FINAL_QUANTITY`:
   - Non-numeric values (e.g., `"zero"`) were converted to `0`.
3. Filled missing values:
   - Replaced missing `CATEGORY_1` with `"Others"`.
   - Replaced missing `CATEGORY_2` and `CATEGORY_3` with `"Other Category"`.
   - Replaced missing `BRAND` and `MANUFACTURER` with `"Unknown"`.
4. Dropped rows with missing `BARCODE` in the `TRANSACTIONS` table, but marked them as `"000"` for general analysis purposes.

---

## Rest refer the summary Document for graphs 

## 2. Analysis



### Top 5 Brands by Receipts Scanned Among Users 21 and Over
**SQL Query**:
```sql
SELECT  
    p.BRAND,  
    COUNT(DISTINCT t.RECEIPT_ID) AS receipt_count 
FROM  
    USERS u 
JOIN  
    TRANSACTIONS t ON u.ID = t.USER_ID 
JOIN  
    PRODUCTS p ON t.BARCODE = p.BARCODE 
WHERE  
    (YEAR(CURDATE()) - YEAR(u.BIRTH_DATE)) >= 21 
GROUP BY  
    p.BRAND 
ORDER BY  
    receipt_count DESC 
LIMIT 5;

 ### Top 5 Brands by Sales Among Users with Accounts 6+ Months
**SQL Query**:
```sql
SELECT 
    p.BRAND, 
    SUM(t.FINAL_SALE) AS total_sales
FROM 
    USERS u
JOIN 
    TRANSACTIONS t ON u.ID = t.USER_ID
JOIN 
    PRODUCTS p ON t.BARCODE = p.BARCODE
WHERE 
    TIMESTAMPDIFF(MONTH, u.CREATED_DATE, CURDATE()) >= 6
GROUP BY 
    p.BRAND
ORDER BY 
    total_sales DESC
LIMIT 5;

### Percentage of Sales in Health & Wellness Category by Generation
**SQL Query**:
```sql
WITH GENERATION_SALES AS (
    SELECT 
        CASE
            WHEN YEAR(BIRTH_DATE) <= 1945 THEN 'Silent'
            WHEN YEAR(BIRTH_DATE) BETWEEN 1946 AND 1964 THEN 'Boomers'
            WHEN YEAR(BIRTH_DATE) BETWEEN 1965 AND 1980 THEN 'Gen X'
            WHEN YEAR(BIRTH_DATE) BETWEEN 1981 AND 1996 THEN 'Millennials'
            WHEN YEAR(BIRTH_DATE) >= 1997 THEN 'Gen Z'
            ELSE 'Unknown'
        END AS generation,
        SUM(t.FINAL_SALE) AS health_sales
    FROM 
        USERS u
    JOIN 
        TRANSACTIONS t ON u.ID = t.USER_ID
    JOIN 
        PRODUCTS p ON t.BARCODE = p.BARCODE
    WHERE 
        p.CATEGORY_1 = 'Health & Wellness'
    GROUP BY 
        generation
), 
TOTAL_SALES AS (
    SELECT 
        CASE
            WHEN YEAR(BIRTH_DATE) <= 1945 THEN 'Silent'
            WHEN YEAR(BIRTH_DATE) BETWEEN 1946 AND 1964 THEN 'Boomers'
            WHEN YEAR(BIRTH_DATE) BETWEEN 1965 AND 1980 THEN 'Gen X'
            WHEN YEAR(BIRTH_DATE) BETWEEN 1981 AND 1996 THEN 'Millennials'
            WHEN YEAR(BIRTH_DATE) >= 1997 THEN 'Gen Z'
            ELSE 'Unknown'
        END AS generation,
        SUM(t.FINAL_SALE) AS total_sales
    FROM 
        USERS u
    JOIN 
        TRANSACTIONS t ON u.ID = t.USER_ID
    GROUP BY 
        generation
)
SELECT 
    g.generation, 
    (g.health_sales / t.total_sales) * 100 AS percentage_of_sales
FROM 
    GENERATION_SALES g
JOIN 
    TOTAL_SALES t ON g.generation = t.generation;

