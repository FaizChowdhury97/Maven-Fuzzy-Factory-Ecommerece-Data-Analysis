# Maven-Fuzzy-Factory-Ecommerece-Data-Analysis

# Project Overview 


Maven Fuzzy Factory is an eCommerce business focused on delivering innovative and user-friendly products. With a strong emphasis on data-driven strategies, the company leverages insights from web traffic and performance metrics to optimize its operations and drive growth. Maven Fuzzy Factory requires an analysis of its performance metrics to assess growth.

# Data Analysis Goals

 **1. Quantify Growth:** Use SQL to extract and analyze website traffic and performance data from the Maven Fuzzy Factory database.

 **2. Create a Compelling Story:** Present the data in a clear and engaging way that highlights the achieved growth.

# Data Information
The Maven Fuzzy Factory database consists of six tables, each serving a specific purpose. These tables are as follows:

# Database Structure

## 1. Website Sessions
**Fields**:
- `website_session_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `user_id` (BIGINT UNSIGNED)
- `is_repeat_session` (SMALLINT UNSIGNED)
- `utm_source` (VARCHAR)
- `utm_campaign` (VARCHAR)
- `device_type` (VARCHAR)
- `http_referrer` (VARCHAR)

**Primary Key**: `website_session_id`

---

## 2. Website Pageviews
**Fields**:
- `website_pageview_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `website_session_id` (BIGINT UNSIGNED)
- `pageview_url` (VARCHAR)

**Primary Key**: `website_pageview_id`

---

## 3. Products
**Fields**:
- `product_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `user_id` (BIGINT UNSIGNED)
- `product_name` (VARCHAR)

**Primary Key**: `product_id`

---

## 4. Orders
**Fields**:
- `order_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `website_session_id` (BIGINT UNSIGNED)
- `user_id` (BIGINT UNSIGNED)
- `primary_product_id` (SMALLINT UNSIGNED)
- `items_purchased` (SMALLINT UNSIGNED)
- `price_usd` (DECIMAL)
- `cogs_usd` (DECIMAL)

**Primary Key**: `order_id`

---

## 5. Order Items
**Fields**:
- `order_item_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `order_id` (BIGINT UNSIGNED)
- `product_id` (BIGINT UNSIGNED)
- `is_primary` (SMALLINT UNSIGNED)
- `price_usd` (DECIMAL)

**Primary Key**: `order_item_id`


## 6. Order_Item_Refunds

**Fields:**

- `order_item_refund_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `order_item_id` (BIGINT UNSIGNED)
- `order_id` (BIGINT UNSIGNED)
- `refund_amount_usd` (DECIMAL)

**Primary Key:** order_item_refund_id

# Queries 

## Problem 1
Pull monthly trends for Google Search sessions and orders to highlight growth in this channel.

# SQL Query 


```sql
SELECT 
    Year(a.created_at) AS year, 
    Month(a.created_at) AS month,
    Count(DISTINCT a.website_session_id) AS sessions,
    Count(DISTINCT b.order_id) AS orders,
    Round(
        Count(DISTINCT b.order_id) * 1.0 / Count(DISTINCT a.website_session_id) * 100, 2
    ) AS conv_rate
FROM 
    website_sessions a
LEFT JOIN 
    orders b
ON 
    b.website_session_id = a.website_session_id
WHERE 
    a.created_at < '2012-11-27'
    AND a.utm_source = 'gsearch'
GROUP BY 
    1, 
    2;







## Query Explanation

This SQL query calculates key metrics related to website performance, including the number of sessions, orders, and the conversion rate, grouped by year and month. The query uses data from two tables: `website_sessions` and `orders`.

### Key Components:
- **Year and Month**: The query extracts the year and month from the `created_at` column in the `website_sessions` table.
- **Sessions Count**: Counts distinct website sessions.
- **Orders Count**: Counts distinct orders.
- **Conversion Rate**: The ratio of orders to sessions, multiplied by 100 to get the percentage, rounded to two decimal places.
- **Filters**:
  - Limits data to sessions created before `'2012-11-27'`.
  - Filters by the UTM source `'gsearch'`.

### Purpose:
This query helps in tracking website session activity and analyzing conversion performance over time.

  
