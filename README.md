# Zepto Grocery Store Data Analysis

## Project Overview

This project contains a comprehensive SQL-based analysis of grocery product data from Zepto, focusing on pricing strategies, inventory management, and product categorization. The analysis provides insights into product performance, pricing optimization, and inventory valuation across different categories.

## Database Schema

### Table Structure: `zepto`

| Column | Data Type | Description |
|--------|-----------|-------------|
| `sku_id` | SERIAL PRIMARY KEY | Unique identifier for each product SKU |
| `category` | VARCHAR(120) | Product category classification |
| `name` | VARCHAR(150) NOT NULL | Product name |
| `mrp` | NUMERIC(8,2) | Maximum Retail Price |
| `discountPercent` | NUMERIC(5,2) | Discount percentage offered |
| `availableQuantity` | INTEGER | Current stock quantity |
| `discountedSellingPrice` | NUMERIC(8,2) | Final selling price after discount |
| `weightInGms` | INTEGER | Product weight in grams |
| `outOfStock` | BOOLEAN | Stock availability status |
| `quantity` | INTEGER | Product quantity specification |

## Analysis Workflow

### 1. Data Exploration
- **Row Count Verification**: Total records assessment
- **Sample Data Review**: Initial 10 records examination
- **Data Quality Check**: Null value identification across all columns
- **Category Analysis**: Distinct product category enumeration
- **Stock Status Overview**: In-stock vs out-of-stock distribution
- **Product Variation Analysis**: Identification of products with multiple SKUs

### 2. Data Cleaning Operations
- **Zero Price Removal**: Elimination of products with invalid pricing (₹0)
- **Currency Conversion**: Transformation from paise to rupees (division by 100)
- **Data Validation**: Post-cleaning price verification

### 3. Business Intelligence Queries

#### Q1: Top Value Products Analysis
Identifies the 10 products offering maximum discount percentages for customer value optimization.

#### Q2: High-Value Out-of-Stock Analysis
Examines premium products (MRP > ₹300) currently unavailable, highlighting potential revenue loss.

#### Q3: Category Revenue Estimation
Calculates projected revenue per category based on current inventory and selling prices.

#### Q4: Premium Low-Discount Products
Filters high-value products (MRP > ₹500) with minimal discounts (<10%) for pricing strategy insights.

#### Q5: Category Discount Performance
Ranks top 5 categories by average discount percentage to identify promotional patterns.

#### Q6: Price-per-Gram Value Analysis
Evaluates cost efficiency for products above 100g weight, sorted by best value proposition.

#### Q7: Weight-Based Product Segmentation
Categorizes products into Low (<1kg), Medium (1-5kg), and Bulk (>5kg) segments.

#### Q8: Inventory Weight Distribution
Calculates total inventory weight per category for logistics and storage planning.

## Key Features

- **Comprehensive Data Validation**: Multi-layered approach to identify and resolve data quality issues
- **Financial Analysis**: Revenue calculations and pricing strategy evaluation
- **Inventory Management**: Stock level analysis and weight distribution insights
- **Customer Value Assessment**: Discount and value proposition analysis
- **Category Performance**: Cross-category comparison and ranking

## Technical Implementation

- **Database**: PostgreSQL (or compatible SQL database)
- **Primary Keys**: Auto-incrementing SKU identification system
- **Data Types**: Optimized for retail data handling with appropriate precision
- **Indexing**: Primary key indexing for efficient query performance

## Usage Instructions

1. **Database Setup**: Execute the table creation script
2. **Data Import**: Load your grocery product dataset
3. **Exploration Phase**: Run data exploration queries to understand dataset characteristics
4. **Cleaning Phase**: Execute data cleaning operations to ensure data integrity
5. **Analysis Phase**: Run business intelligence queries based on specific requirements

## Business Applications

- **Inventory Optimization**: Identify slow-moving and high-value products
- **Pricing Strategy**: Analyze discount patterns and value propositions
- **Category Management**: Understand category-wise performance metrics
- **Revenue Forecasting**: Estimate potential revenue based on current inventory
- **Supply Chain Planning**: Weight-based inventory management insights

## Future Enhancements

- Time-series analysis for seasonal trends
- Customer segment analysis integration
- Automated alert system for out-of-stock high-value items
- Performance dashboard creation
- Predictive analytics for demand forecasting

## Contributing

When contributing to this analysis:
1. Maintain data privacy and security standards
2. Document any new queries with business justification
3. Test all modifications on sample data first
4. Follow consistent naming conventions


---

**Note**: This analysis provides a foundation for grocery retail data insights. Adapt queries based on specific business requirements and data characteristics.
