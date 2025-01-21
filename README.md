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
