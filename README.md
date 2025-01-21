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

