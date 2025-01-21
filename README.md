# Fetch Data Analysis Summary

## SLACK / EMAIL MESSAGE

Subject: Data Insights and Key Findings

Hi

Here’s a quick summary of my findings:

**Key Data Quality Issues**
- **Missing Data:** 11.52% of BARCODE and 92.02% of CATEGORY_4 are missing.
- **Ambiguities:** FINAL_QUANTITY contains non-numeric values like "zero".
- **Inconsistent Types:** Dates are stored as strings, impacting time-based analyses.
Insights
- **Growth**: Fetch saw peak user growth in 2017 (+820%), but signups declined in 2023 (-42%) and 2024 (-24%).
Top Brands: Brand A leads in receipts scanned; Brand X dominates sales for long-term users.
- **Generations:** Millennials drive 45% of Health & Wellness sales, followed by Gen X at 30%.
- **Request for Action**
Clarify ambiguous values (e.g., "zero" in FINAL_QUANTITY) and product hierarchy.
Address gaps in BARCODE and BIRTH_DATE data.
Provide marketing input on 2023-24 decline.

Let me know when we can discuss!

---------END---------------

# Little bit more Insights
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

