# Zepto Data Analysis
This is a SQL project where I worked with a messy e-commerce dataset (from Zepto’s product listings) to clean it up, explore it, and get insights. The idea is to practice how data analysts in retail/e-commerce handle real product and inventory data.

 # 📌 What I Did
 
Set up a PostgreSQL database and created a table

Imported a raw CSV file (with encoding fixes)

Performed Exploratory Data Analysis (EDA) to check categories, stock status, duplicates, etc.

Cleaned the data (removed invalid rows, converted prices from paise → rupees)

Wrote SQL queries to answer business questions like:

1.Which products have the highest discounts?

2.Which expensive items are out of stock?

3.What’s the potential revenue by category?

4.Which categories give the best value for money?

 # 📁 Dataset
 
1.Source: Kaggle (Zepto product listings)

2.Each row = one product SKU (same product can appear in different sizes/discounts)

Main columns:

sku_id, name, category, mrp, discountPercent, discountedSellingPrice,
availableQuantity, weightInGms, outOfStock, quantity

# 🔧 Steps

1.Create Table – wrote SQL schema for products

2.Import Data – used \copy in psql / pgAdmin import option

3.Explore Data – count rows, check nulls, categories, stock status

4.Clean Data – fix invalid rows, convert prices, enforce rules

5.Business Insights – queries for discounts, revenue, inventory trends

# ▶️ How to Run

1.Create the table using schema.sql

2.Import the CSV

3.Run eda.sql to explore

4.Run cleaning.sql to fix issues

5.Run insights.sql to see business analysis



'''(

CREATE TABLE zepto (

  sku_id SERIAL PRIMARY KEY,               -- Auto-incrementing ID for each product
  
  category VARCHAR(120),                   -- Product category (up to 120 characters)
  
  name VARCHAR(150) NOT NULL,              -- Product name (required)
  
  mrp NUMERIC(8,2),                        -- Maximum Retail Price with 2 decimals
  
  discountPercent NUMERIC(5,2),            -- Discount percentage (e.g., 10.25%)
  
  availableQuantity INTEGER,               -- Available stock count
  
  discountedSellingPrice NUMERIC(8,2),     -- Final selling price after discount
  
  weightInGms INTEGER,                     -- Weight of the product in grams
  
  outOfStock BOOLEAN,                      -- TRUE/FALSE for stock status
  
  quantity INTEGER                         -- Possibly ordered quantity / unit pack
  
);
)'''





