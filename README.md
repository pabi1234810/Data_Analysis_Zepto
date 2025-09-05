# Zepto Grocery Store Data Analysis

## Project Overview

This project demonstrates a complete SQL-based analysis workflow for grocery product data from Zepto. Follow this step-by-step guide to perform comprehensive data exploration, cleaning, and business intelligence analysis.

## 🚀 Quick Start Guide

### Step 1: Database Setup and Table Creation

```sql
-- Create the main table structure
DROP TABLE IF EXISTS zepto;

CREATE TABLE zepto (
    sku_id SERIAL PRIMARY KEY,
    category VARCHAR(120),
    name VARCHAR(150) NOT NULL,
    mrp NUMERIC(8,2),
    discountPercent NUMERIC(5,2),
    availableQuantity INTEGER,
    discountedSellingPrice NUMERIC(8,2),
    weightInGms INTEGER,
    outOfStock BOOLEAN,
    quantity INTEGER
);

-- Verify table creation
\d zepto;
```

**Expected Output:**
```
Table "public.zepto"
Column               | Type           | Collation | Nullable | Default
---------------------|----------------|-----------|----------|--------
sku_id              | integer        |           | not null | nextval('zepto_sku_id_seq'::regclass)
category            | varchar(120)   |           |          |
name                | varchar(150)   |           | not null |
```

---

## 📊 Phase 1: Data Exploration Workflow

### Step 2: Initial Data Assessment

```sql
-- Check total number of records
SELECT COUNT(*) AS total_products FROM zepto;
```
```
total_products
--------------
15847
```

```sql
-- Preview first 10 records to understand data structure
SELECT * FROM zepto LIMIT 10;
```

### Step 3: Data Quality Validation

```sql
-- Identify any null values across all columns
SELECT 
    SUM(CASE WHEN name IS NULL THEN 1 ELSE 0 END) AS null_names,
    SUM(CASE WHEN category IS NULL THEN 1 ELSE 0 END) AS null_categories,
    SUM(CASE WHEN mrp IS NULL THEN 1 ELSE 0 END) AS null_mrp,
    SUM(CASE WHEN discountPercent IS NULL THEN 1 ELSE 0 END) AS null_discounts,
    SUM(CASE WHEN availableQuantity IS NULL THEN 1 ELSE 0 END) AS null_quantity
FROM zepto;
```

### Step 4: Explore Product Categories

```sql
-- Get all unique categories and their product counts
SELECT 
    category,
    COUNT(*) AS product_count
FROM zepto
GROUP BY category
ORDER BY product_count DESC;
```

**Example Output:**
```
category                | product_count
------------------------|-------------
Baby Care              | 2845
Personal Care          | 1967
Household Items        | 1654
Fruits & Vegetables    | 1432
Dairy & Eggs          | 1289
```

### Step 5: Stock Status Analysis

```sql
-- Analyze in-stock vs out-of-stock distribution
SELECT 
    CASE 
        WHEN outOfStock = TRUE THEN 'Out of Stock'
        ELSE 'In Stock'
    END AS stock_status,
    COUNT(sku_id) AS product_count,
    ROUND((COUNT(sku_id) * 100.0 / (SELECT COUNT(*) FROM zepto)), 2) AS percentage
FROM zepto
GROUP BY outOfStock
ORDER BY product_count DESC;
```

---

## 🧹 Phase 2: Data Cleaning Process

### Step 6: Identify and Remove Invalid Data

```sql
-- Find products with zero pricing (invalid data)
SELECT COUNT(*) AS zero_price_products
FROM zepto
WHERE mrp = 0 OR discountedSellingPrice = 0;
```

```sql
-- Remove invalid pricing records
DELETE FROM zepto WHERE mrp = 0;

-- Verify deletion
SELECT COUNT(*) AS remaining_products FROM zepto;
```

### Step 7: Currency Conversion (Paise to Rupees)

```sql
-- Check current price format (assuming data is in paise)
SELECT name, mrp, discountedSellingPrice 
FROM zepto 
WHERE mrp > 1000  -- High values indicate paise format
LIMIT 5;
```

```sql
-- Convert from paise to rupees
UPDATE zepto
SET mrp = mrp / 100.0,
    discountedSellingPrice = discountedSellingPrice / 100.0;

-- Verify conversion
SELECT name, mrp, discountedSellingPrice 
FROM zepto 
LIMIT 5;
```

**After Conversion:**
```
name                    | mrp    | discountedSellingPrice
------------------------|--------|----------------------
Organic Apples 1kg     | 299.00 | 249.00
Premium Rice 5kg       | 850.00 | 680.00
```

---

## 📈 Phase 3: Business Intelligence Analysis

### Step 8: Top Value Products (Highest Discounts)

```sql
-- Find top 10 products with maximum discount percentages
SELECT DISTINCT 
    name, 
    mrp,
    discountedSellingPrice,
    discountPercent,
    (mrp - discountedSellingPrice) AS savings_amount
FROM zepto
ORDER BY discountPercent DESC
LIMIT 10;
```

**Business Insight:** *Identify products offering maximum customer value*

### Step 9: High-Value Out-of-Stock Analysis

```sql
-- Premium products currently unavailable (potential revenue loss)
SELECT DISTINCT 
    name,
    category,
    mrp,
    (mrp * availableQuantity) AS potential_revenue_loss
FROM zepto
WHERE outOfStock = TRUE 
  AND mrp > 300
ORDER BY potential_revenue_loss DESC
LIMIT 15;
```

**Business Insight:** *Prioritize restocking high-value items*

### Step 10: Category-wise Revenue Potential

```sql
-- Calculate estimated revenue per category
SELECT 
    category,
    COUNT(*) AS total_products,
    SUM(availableQuantity) AS total_stock,
    ROUND(SUM(discountedSellingPrice * availableQuantity), 2) AS potential_revenue,
    ROUND(AVG(discountedSellingPrice), 2) AS avg_selling_price
FROM zepto
WHERE outOfStock = FALSE
GROUP BY category
ORDER BY potential_revenue DESC;
```

**Example Output:**
```
category           | total_products | total_stock | potential_revenue | avg_selling_price
-------------------|---------------|-------------|-------------------|------------------
Baby Care         | 2456          | 45678       | 1,245,890.50     | 156.75
Personal Care     | 1834          | 38924       | 987,654.25       | 89.50
```

### Step 11: Premium Products with Low Discounts

```sql
-- High-value products with minimal discounts (pricing strategy analysis)
SELECT DISTINCT 
    name, 
    category,
    mrp, 
    discountPercent,
    discountedSellingPrice,
    CASE 
        WHEN discountPercent < 5 THEN 'Premium Pricing'
        WHEN discountPercent < 10 THEN 'Conservative Discount'
        ELSE 'Standard Discount'
    END AS pricing_strategy
FROM zepto
WHERE mrp > 500 
  AND discountPercent < 10
ORDER BY mrp DESC, discountPercent ASC;
```

### Step 12: Category Discount Performance

```sql
-- Top 5 categories by average discount percentage
SELECT 
    category,
    COUNT(*) AS product_count,
    ROUND(AVG(discountPercent), 2) AS avg_discount,
    ROUND(AVG(mrp), 2) AS avg_mrp,
    ROUND(AVG(discountedSellingPrice), 2) AS avg_selling_price
FROM zepto
GROUP BY category
HAVING COUNT(*) >= 50  -- Categories with significant product count
ORDER BY avg_discount DESC
LIMIT 5;
```

### Step 13: Value-for-Money Analysis (Price per Gram)

```sql
-- Best value products based on price per gram (products > 100g only)
SELECT DISTINCT 
    name, 
    category,
    weightInGms,
    discountedSellingPrice,
    ROUND(discountedSellingPrice/weightInGms, 4) AS price_per_gram,
    CASE 
        WHEN discountedSellingPrice/weightInGms < 0.1 THEN 'Excellent Value'
        WHEN discountedSellingPrice/weightInGms < 0.5 THEN 'Good Value'
        WHEN discountedSellingPrice/weightInGms < 1.0 THEN 'Average Value'
        ELSE 'Premium Pricing'
    END AS value_category
FROM zepto
WHERE weightInGms >= 100 
  AND weightInGms IS NOT NULL
  AND discountedSellingPrice > 0
ORDER BY price_per_gram ASC
LIMIT 20;
```

### Step 14: Product Weight Segmentation

```sql
-- Categorize products by weight for inventory management
SELECT 
    CASE 
        WHEN weightInGms < 100 THEN 'Mini (< 100g)'
        WHEN weightInGms < 500 THEN 'Small (100-500g)'
        WHEN weightInGms < 1000 THEN 'Medium (500g-1kg)'
        WHEN weightInGms < 5000 THEN 'Large (1-5kg)'
        ELSE 'Bulk (> 5kg)'
    END AS weight_category,
    COUNT(*) AS product_count,
    ROUND(AVG(discountedSellingPrice), 2) AS avg_price,
    ROUND(AVG(discountPercent), 2) AS avg_discount
FROM zepto
WHERE weightInGms IS NOT NULL
GROUP BY 
    CASE 
        WHEN weightInGms < 100 THEN 'Mini (< 100g)'
        WHEN weightInGms < 500 THEN 'Small (100-500g)'
        WHEN weightInGms < 1000 THEN 'Medium (500g-1kg)'
        WHEN weightInGms < 5000 THEN 'Large (1-5kg)'
        ELSE 'Bulk (> 5kg)'
    END
ORDER BY 
    CASE 
        WHEN weight_category = 'Mini (< 100g)' THEN 1
        WHEN weight_category = 'Small (100-500g)' THEN 2
        WHEN weight_category = 'Medium (500g-1kg)' THEN 3
        WHEN weight_category = 'Large (1-5kg)' THEN 4
        ELSE 5
    END;
```

### Step 15: Inventory Weight Distribution

```sql
-- Total inventory weight per category (logistics planning)
SELECT 
    category,
    COUNT(*) AS product_types,
    SUM(availableQuantity) AS total_units,
    ROUND(SUM(weightInGms * availableQuantity) / 1000.0, 2) AS total_weight_kg,
    ROUND(AVG(weightInGms), 2) AS avg_weight_per_product
FROM zepto
WHERE weightInGms IS NOT NULL 
  AND availableQuantity > 0
GROUP BY category
ORDER BY total_weight_kg DESC;
```

---

## 🔍 Advanced Analytics

### Step 16: Comprehensive Performance Dashboard

```sql
-- Create a comprehensive business summary
WITH category_stats AS (
    SELECT 
        category,
        COUNT(*) as total_products,
        SUM(CASE WHEN outOfStock = FALSE THEN 1 ELSE 0 END) as in_stock_products,
        ROUND(AVG(discountPercent), 2) as avg_discount,
        ROUND(SUM(discountedSellingPrice * availableQuantity), 2) as potential_revenue
    FROM zepto
    GROUP BY category
),
overall_stats AS (
    SELECT 
        COUNT(*) as total_products,
        ROUND(AVG(discountPercent), 2) as overall_avg_discount,
        ROUND(SUM(discountedSellingPrice * availableQuantity), 2) as total_potential_revenue
    FROM zepto
)
SELECT 
    cs.category,
    cs.total_products,
    cs.in_stock_products,
    ROUND((cs.in_stock_products * 100.0 / cs.total_products), 2) as stock_availability_percent,
    cs.avg_discount,
    cs.potential_revenue,
    ROUND((cs.potential_revenue * 100.0 / os.total_potential_revenue), 2) as revenue_contribution_percent
FROM category_stats cs
CROSS JOIN overall_stats os
ORDER BY cs.potential_revenue DESC;
```

---

## 💡 Key Business Insights Generated

### Inventory Management
- **Stock Availability**: Track out-of-stock percentages by category
- **High-Value Items**: Identify premium products needing immediate restocking
- **Weight Distribution**: Optimize storage and logistics planning

### Pricing Strategy
- **Discount Effectiveness**: Analyze which discount ranges drive sales
- **Value Positioning**: Identify products with best price-to-weight ratios
- **Premium Products**: Track high-value, low-discount items

### Revenue Optimization
- **Category Performance**: Revenue potential by product category
- **Product Mix**: Balance between volume and value products
- **Promotional Opportunities**: Products suitable for discount campaigns

---

## 🎯 Next Steps After Analysis

### 1. Create Automated Reports
```sql
-- Save frequently used queries as views
CREATE VIEW top_discounted_products AS
SELECT DISTINCT name, category, discountPercent, discountedSellingPrice
FROM zepto
ORDER BY discountPercent DESC
LIMIT 50;
```

### 2. Set Up Monitoring Queries
```sql
-- Weekly out-of-stock monitoring
CREATE VIEW weekly_stock_alert AS
SELECT category, name, mrp
FROM zepto
WHERE outOfStock = TRUE AND mrp > 200
ORDER BY mrp DESC;
```

### 3. Export Results for Visualization
```sql
-- Export category performance for charts
\copy (SELECT category, SUM(discountedSellingPrice * availableQuantity) as revenue FROM zepto GROUP BY category ORDER BY revenue DESC) TO 'category_revenue.csv' CSV HEADER;
```

---

## 🛠️ Technical Requirements

- **Database**: PostgreSQL 12+ (or any SQL-compatible database)
- **Tools**: pgAdmin, DBeaver, or command-line interface
- **Skills**: Basic SQL knowledge, data analysis concepts

## 📋 Project Checklist

- [ ] Database setup and table creation
- [ ] Data quality validation completed
- [ ] Data cleaning operations executed
- [ ] All 8 business intelligence queries run
- [ ] Advanced analytics performed
- [ ] Results exported for stakeholder review
- [ ] Automated monitoring queries established

---

## 📝 Contributing

When extending this analysis:
1. Follow the same step-by-step approach
2. Document business reasoning for new queries
3. Include expected output examples
4. Test with sample data first


---

*This README provides a complete workflow for grocery retail data analysis. Each step builds upon the previous one, creating a comprehensive business intelligence solution.*
