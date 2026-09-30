# Internal-Hackathon
# 🛒 E-commerce Data Cleaning & Wrangling using SQL

## 📌 Project Overview

This project focuses on **data cleaning, validation, transformation, and preparation of an E-commerce dataset using MySQL**.

The raw dataset was analyzed and transformed into a cleaned table named `orders_clean`. Various data-quality checks were performed, including duplicate detection, NULL-value analysis, categorical-value standardization, numerical validation, date validation, return-data validation, outlier identification, and creation of derived analytical columns.

---

## 📊 Dataset Overview

| Attribute        |      Value |
| ---------------- | ---------: |
| Total Rows       | **34,500** |
| Total Columns    |     **19** |
| Unique Orders    | **34,500** |
| Unique Customers |  **7,903** |
| Unique Products  | **24,912** |
| Categories       |      **7** |

The original table used for the analysis was:

```sql
orders_raw
```

A cleaned analytical table was created as:

```sql
orders_clean
```

---

## 🛠️ Tools & Technologies

* **MySQL**
* **SQL**
* **Excel**
* **Power BI**
* **Looker Studio**
* Data Cleaning & Data Wrangling
* Data Validation
* Data Visualization

---

## 🔄 Data Cleaning Workflow

The following workflow was followed:

```text
Raw E-commerce Data
        ↓
Data Inspection
        ↓
Create Clean Table
        ↓
Duplicate Check
        ↓
NULL Value Analysis
        ↓
Categorical Data Validation
        ↓
Remove Extra Spaces
        ↓
Standardize Categories
        ↓
Numerical Validation
        ↓
Date Validation
        ↓
Return Data Validation
        ↓
Amount Validation
        ↓
Outlier Detection
        ↓
Derived Columns
        ↓
Final Data Quality Check
        ↓
Clean Dataset
```

---

## 1️⃣ Dataset Inspection

The dataset contains **34,500 rows and 19 columns**.

```sql
SELECT COUNT(*) 
FROM orders_raw;

SHOW COLUMNS FROM orders_raw;
```

---

## 2️⃣ Creating the Clean Table

A separate table was created to preserve the raw dataset.

```sql
DROP TABLE IF EXISTS orders_clean;

CREATE TABLE orders_clean AS
SELECT *
FROM orders_raw;
```

---

## 3️⃣ Duplicate Check

Duplicate `order_id` values were checked using:

```sql
SELECT 
    order_id,
    COUNT(*) AS duplicate_count
FROM orders_clean
GROUP BY order_id
HAVING COUNT(*) > 1;
```

### Result

No duplicate `order_id` values were found.

---

## 4️⃣ NULL Value Analysis

NULL values were checked across all important columns.

```sql
SELECT
    SUM(order_id IS NULL) AS order_id_null,
    SUM(customer_id IS NULL) AS customer_id_null,
    SUM(product_id IS NULL) AS product_id_null,
    SUM(category IS NULL) AS category_null,
    SUM(price IS NULL) AS price_null,
    SUM(discount IS NULL) AS discount_null,
    SUM(quantity IS NULL) AS quantity_null,
    SUM(payment_method IS NULL) AS payment_method_null,
    SUM(order_date IS NULL) AS order_date_null,
    SUM(delivered_date IS NULL) AS delivered_date_null,
    SUM(region IS NULL) AS region_null,
    SUM(returned IS NULL) AS returned_null,
    SUM(request_date IS NULL) AS request_date_null,
    SUM(return_reason IS NULL) AS return_reason_null,
    SUM(total_amount IS NULL) AS total_amount_null,
    SUM(shipping_cost IS NULL) AS shipping_cost_null,
    SUM(profit_margin IS NULL) AS profit_margin_null,
    SUM(customer_age IS NULL) AS customer_age_null,
    SUM(customer_gender IS NULL) AS customer_gender_null
FROM orders_clean;
```

### Finding

The main NULL values were found in:

* `request_date`
* `return_reason`

There were **32,597 NULL values** associated with these return-related fields.

---

## 5️⃣ Categorical Data Validation

### Categories

The dataset contains **7 product categories**:

* Beauty
* Electronics
* Fashion
* Grocery
* Home
* Sports
* Toys

```sql
SELECT DISTINCT category
FROM orders_clean
ORDER BY category;
```

### Payment Methods

* COD
* Credit Card
* Debit Card
* PayPal
* UPI
* Wallet

### Regions

* Central
* East
* North
* South
* West

### Customer Gender

* Female
* Male
* Other

### Return Status

* Yes
* No

---

## 6️⃣ Removing Extra Spaces

Extra spaces were removed from categorical and identifier columns using `TRIM()`.

```sql
UPDATE orders_clean
SET
    order_id = TRIM(order_id),
    customer_id = TRIM(customer_id),
    product_id = TRIM(product_id),
    category = TRIM(category),
    payment_method = TRIM(payment_method),
    region = TRIM(region),
    returned = TRIM(returned),
    return_reason = TRIM(return_reason),
    customer_gender = TRIM(customer_gender);
```

---

## 7️⃣ Standardizing Categorical Values

Categorical values were standardized using `LOWER()` and `TRIM()`.

```sql
UPDATE orders_clean
SET category = LOWER(TRIM(category));

UPDATE orders_clean
SET payment_method = LOWER(TRIM(payment_method));

UPDATE orders_clean
SET region = LOWER(TRIM(region));

UPDATE orders_clean
SET returned = LOWER(TRIM(returned));

UPDATE orders_clean
SET customer_gender = LOWER(TRIM(customer_gender));
```

### Category Distribution

| Category    | Orders |
| ----------- | -----: |
| Fashion     |  6,254 |
| Electronics |  6,180 |
| Home        |  5,487 |
| Toys        |  4,247 |
| Sports      |  4,171 |
| Beauty      |  4,103 |
| Grocery     |  4,058 |

---

## 8️⃣ Price Validation

Price statistics were checked using:

```sql
SELECT
    MIN(price) AS min_price,
    MAX(price) AS max_price,
    AVG(price) AS avg_price
FROM orders_clean;
```

| Metric        |      Value |
| ------------- | ---------: |
| Minimum Price |       1.01 |
| Maximum Price |    2930.47 |
| Average Price | 119.391632 |

Invalid prices were checked:

```sql
SELECT *
FROM orders_clean
WHERE price <= 0;
```

**Result: 0 invalid rows**

---

## 9️⃣ Quantity Validation

```sql
SELECT
    MIN(quantity) AS min_quantity,
    MAX(quantity) AS max_quantity,
    AVG(quantity) AS avg_quantity
FROM orders_clean;
```

| Metric           |  Value |
| ---------------- | -----: |
| Minimum Quantity |      1 |
| Maximum Quantity |      5 |
| Average Quantity | 1.4907 |

No invalid quantity values were found.

---

## 🔟 Discount Validation

```sql
SELECT
    MIN(discount) AS min_discount,
    MAX(discount) AS max_discount
FROM orders_clean;
```

| Metric           | Value |
| ---------------- | ----: |
| Minimum Discount |    0% |
| Maximum Discount |   30% |

Invalid discounts were checked using:

```sql
SELECT *
FROM orders_clean
WHERE discount < 0
   OR discount > 1;
```

**Result: 0 invalid rows**

---

## 1️⃣1️⃣ Customer Age Validation

```sql
SELECT
    MIN(customer_age) AS minimum_age,
    MAX(customer_age) AS maximum_age,
    AVG(customer_age) AS average_age
FROM orders_clean;
```

| Metric      |   Value |
| ----------- | ------: |
| Minimum Age |      18 |
| Maximum Age |      69 |
| Average Age | 43.4744 |

No suspicious age values were found.

---

## 1️⃣2️⃣ Order & Delivery Date Validation

Orders were checked to ensure that delivery did not occur before the order date.

```sql
SELECT *
FROM orders_clean
WHERE delivered_date < order_date;
```

**Result: 0 invalid records**

---

## 1️⃣3️⃣ Return Data Validation

Returned orders were checked to ensure that they had a return request date.

```sql
SELECT *
FROM orders_clean
WHERE returned = 'yes'
AND request_date IS NULL;
```

**Result: 0 invalid records**

Non-returned orders were also checked for unexpected return information.

```sql
SELECT *
FROM orders_clean
WHERE returned = 'no'
AND (
    request_date IS NOT NULL
    OR return_reason IS NOT NULL
);
```

**Result: 0 invalid records**

### Return Reasons

| Return Reason      | Orders |
| ------------------ | -----: |
| Not as described   |    490 |
| No longer needed   |    481 |
| Defective          |    465 |
| Missing/Wrong item |    439 |
| Slow delivery      |     28 |

---

## 1️⃣4️⃣ Total Amount Validation

The expected order amount was calculated using:

```text
Price × Quantity × (1 − Discount) + Shipping Cost
```

SQL:

```sql
SELECT
    order_id,
    price,
    quantity,
    discount,
    shipping_cost,
    total_amount,
    (price * quantity * (1 - discount) + shipping_cost)
        AS calculated_amount
FROM orders_clean
LIMIT 20;
```

Differences were identified using:

```sql
SELECT
    order_id,
    total_amount,
    (price * quantity * (1 - discount) + shipping_cost)
        AS calculated_amount
FROM orders_clean
WHERE ABS(
    total_amount -
    (price * quantity * (1 - discount) + shipping_cost)
) > 0.01;
```

---

## 1️⃣5️⃣ Shipping Cost Validation

```sql
SELECT
    MIN(shipping_cost) AS min_shipping,
    MAX(shipping_cost) AS max_shipping,
    AVG(shipping_cost) AS avg_shipping
FROM orders_clean;
```

| Metric                |    Value |
| --------------------- | -------: |
| Minimum Shipping Cost |     0.00 |
| Maximum Shipping Cost |    15.65 |
| Average Shipping Cost | 6.152120 |

No negative shipping costs were found.

---

## 1️⃣6️⃣ Profit Validation

```sql
SELECT
    MIN(profit_margin) AS min_profit,
    MAX(profit_margin) AS max_profit,
    AVG(profit_margin) AS avg_profit
FROM orders_clean;
```

| Metric         |     Value |
| -------------- | --------: |
| Minimum Profit |     -6.20 |
| Maximum Profit |   1536.17 |
| Average Profit | 28.116505 |

There were **6,104 records with negative profit margins**.

```sql
SELECT *
FROM orders_clean
WHERE profit_margin < 0;
```

---

## 1️⃣7️⃣ Outlier Detection

Potential outliers were investigated by sorting the dataset according to important numerical fields.

### Highest-priced orders

```sql
SELECT *
FROM orders_clean
ORDER BY price DESC
LIMIT 20;
```

### Highest total-value orders

```sql
SELECT *
FROM orders_clean
ORDER BY total_amount DESC
LIMIT 20;
```

### Highest-profit orders

```sql
SELECT *
FROM orders_clean
ORDER BY profit_margin DESC
LIMIT 20;
```

---

## 1️⃣8️⃣ Unique ID Validation

### Unique Customers

**7,903 customers**

```sql
SELECT COUNT(DISTINCT customer_id) AS unique_customers
FROM orders_clean;
```

### Unique Products

**24,912 products**

```sql
SELECT COUNT(DISTINCT product_id) AS unique_products
FROM orders_clean;
```

### Unique Categories

**7 categories**

```sql
SELECT COUNT(DISTINCT category) AS unique_categories
FROM orders_clean;
```

---

## 1️⃣9️⃣ Derived Column — Delivery Days

A new analytical column was created to calculate delivery time.

```sql
ALTER TABLE orders_clean
ADD COLUMN delivery_days INT;
```

```sql
UPDATE orders_clean
SET delivery_days =
    DATEDIFF(delivered_date, order_date);
```

### Delivery Time Statistics

| Metric  |   Days |
| ------- | -----: |
| Minimum |      3 |
| Maximum |     13 |
| Average | 4.8142 |

---

## 2️⃣0️⃣ Derived Column — Return Status

A cleaner analytical field was created using a `CASE` statement.

```sql
ALTER TABLE orders_clean
ADD COLUMN return_status VARCHAR(20);
```

```sql
UPDATE orders_clean
SET return_status =
    CASE
        WHEN returned = 'yes' THEN 'Returned'
        WHEN returned = 'no' THEN 'Not Returned'
        ELSE 'Unknown'
    END;
```

### Return Status Distribution

| Return Status | Orders |
| ------------- | -----: |
| Not Returned  | 32,597 |
| Returned      |  1,903 |

---

# ✅ Final Data Quality Check

After the cleaning and validation process:

```sql
SELECT
    COUNT(*) AS total_rows,
    COUNT(DISTINCT order_id) AS unique_orders,
    COUNT(DISTINCT customer_id) AS unique_customers,
    COUNT(DISTINCT product_id) AS unique_products,
    COUNT(DISTINCT category) AS categories
FROM orders_clean;
```

### Final Dataset Summary

| Metric           |     Result |
| ---------------- | ---------: |
| Total Rows       | **34,500** |
| Unique Orders    | **34,500** |
| Unique Customers |  **7,903** |
| Unique Products  | **24,912** |
| Categories       |      **7** |

---

# 📈 Analytics & Visualization

After SQL-based cleaning and validation, the cleaned dataset can be used for further analysis and visualization using:

### Excel

* Pivot Tables
* Data Analysis
* Charts
* KPI calculations

### Power BI

* Interactive dashboards
* KPI cards
* Sales analysis
* Category analysis
* Customer analysis
* Return analysis
* Profit analysis

### Looker Studio

* Interactive reports
* KPI scorecards
* Time-series analysis
* Category and regional analysis
* Filters and date controls

---

# 🎯 Key Data Quality Findings

* Dataset contains **34,500 records**.
* No duplicate `order_id` values were identified.
* No invalid price values were found.
* No invalid quantity values were found.
* Discount values remained within the expected range.
* Customer ages were within the validated range.
* No orders had delivery dates before order dates.
* Return information passed consistency checks.
* **6,104 records had negative profit margins.**
* Average delivery time was approximately **4.81 days**.
* There were **1,903 returned orders**.
* The cleaned dataset contains **7,903 unique customers** and **24,912 unique products**.

---

# 🚀 Project Outcome

The raw E-commerce dataset was transformed into a **validated and analysis-ready dataset** using SQL.

The project demonstrates practical skills in:

* Data Cleaning
* Data Wrangling
* SQL Querying
* Data Validation
* Data Quality Analysis
* Data Transformation
* Derived Column Creation
* Outlier Detection
* Business Data Analysis
* Dashboard Preparation

---

# 👨‍💻 Author

**Madhav**

B.Tech CSE | Data Analytics Enthusiast

### Skills Used

`SQL` `MySQL` `Excel` `Power BI` `Looker Studio` `Data Cleaning` `Data Wrangling` `Data Visualization`

---

## ⭐ If you found this project useful

Feel free to ⭐ star the repository and explore the SQL queries and analysis.
