# Maven-Fuzzy-Factory-Ecommerece-Data-Analysis

# Project Overview 


Maven Fuzzy Factory is an eCommerce business focused on delivering innovative and user-friendly products. With a strong emphasis on data-driven strategies, the company leverages insights from web traffic and performance metrics to optimize its operations and drive growth. Maven Fuzzy Factory requires an analysis of its performance metrics to assess growth.

# Data Analysis Goals

 **1. Quantify Growth:** Use SQL to extract and analyze website traffic and performance data from the Maven Fuzzy Factory database.

 **2. Create a Compelling Story:** Present the data in a clear and engaging way that highlights the achieved growth.

# Data Information
The Maven Fuzzy Factory database consists of six tables, each serving a specific purpose. These tables are as follows:

# Database Structure

### 1. Website Sessions

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

### 2. Website Pageviews

**Fields**:
- `website_pageview_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `website_session_id` (BIGINT UNSIGNED)
- `pageview_url` (VARCHAR)

**Primary Key**: `website_pageview_id`

---

### 3. Products

**Fields**:
- `product_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `user_id` (BIGINT UNSIGNED)
- `product_name` (VARCHAR)

**Primary Key**: `product_id`

---

### 4. Orders

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

### 5. Order Item

**Fields**:
- `order_item_id` (BIGINT UNSIGNED)
- `created_at` (DATETIME)
- `order_id` (BIGINT UNSIGNED)
- `product_id` (BIGINT UNSIGNED)
- `is_primary` (SMALLINT UNSIGNED)
- `price_usd` (DECIMAL)

**Primary Key**: `order_item_id`

---

### 6. Order_Item_Refunds

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

### SQL Query 


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
```


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

### Query Result

The query returns the following metrics for sessions and orders, grouped by year and month:

| Year | Month | Sessions | Orders | Conversion Rate (CVR) |
|------|-------|----------|--------|-----------------------|
| 2012 | 3     | 1860     | 60     | 3.23%                 |
| 2012 | 4     | 3574     | 92     | 2.57%                 |
| 2012 | 5     | 3410     | 97     | 2.84%                 |
| 2012 | 6     | 3578     | 121    | 3.38%                 |
| 2012 | 7     | 3811     | 145    | 3.80%                 |
| 2012 | 8     | 4877     | 184    | 3.77%                 |
| 2012 | 9     | 4491     | 188    | 4.19%                 |
| 2012 | 10    | 5534     | 234    | 4.23%                 |
| 2012 | 11    | 8889     | 373    | 4.20%                 |

The conversion rate (CVR) is calculated as the percentage of orders per session.

### Answer Interpretation

The table provides a monthly breakdown of website sessions and orders, specifically from Gsearch traffic. Key observations include:

- **Consistent Growth**: Both the number of sessions and orders have shown a steady increase each month.
- **Positive Conversion Rate**: The conversion rate (CVR), which measures the percentage of orders relative to sessions, has also been steadily growing, indicating an improving efficiency in turning sessions into orders.

This trend highlights the significant role that Gsearch plays in driving the company's growth, positioning it as a valuable marketing channel. The data can be effectively used to demonstrate the company's growth trajectory to the board, as requested.


## Problem 2

A monthly trend analysis of Gsearch is required, with a separate breakdown of non-brand and brand campaigns. The goal is to evaluate whether the brand campaign is gaining traction, as this could be a positive narrative to present.

### SQL Query

```sql
select year(a.created_at) as year, month(a.created_at) as month,
count(DISTINCT case when a.utm_campaign = 'nonbrand' then a.website_session_id else null end) as non_brand_sessions,
count(DISTINCT case when a.utm_campaign = 'nonbrand' then b.order_id else null end) as non_brand_orders,
count(DISTINCT case when a.utm_campaign = 'brand' then a.website_session_id else null end) as brand_sessions,
count(DISTINCT case when a.utm_campaign = 'brand' then b.order_id else null end) as brand_orders
from website_sessions a
left join orders b on b.website_session_id = a.website_session_id
where a.created_at < '2012-11-27'
AND a.utm_source = 'gsearch'
GROUP BY 1,2;
```
### Query Explanation

In this SQL query:

- We select the year and month of the `created_at` column to group the data on a monthly basis.
- We use conditional statements to count nonbrand sessions, nonbrand orders, brand sessions, and brand orders separately. We determine this based on the `utm_campaign` value.
- We perform a `LEFT JOIN` between the `website_sessions` and `orders` tables using the `website_session_id` to ensure all Gsearch sessions are included, whether they have orders or not.
- The data is filtered to include records created before November 27, 2012, and where the `utm_source` is 'gsearch'. The results are grouped by year and month.

---

### Answer

The query returns the following result:

| Year | Month | Nonbrand Sessions | Nonbrand Orders | Brand Sessions | Brand Orders |
|------|-------|-------------------|-----------------|----------------|--------------|
| 2012 | 3     | 1852              | 60              | 8              | 0            |
| 2012 | 4     | 3509              | 86              | 65             | 6            |
| 2012 | 5     | 3295              | 91              | 115            | 6            |
| 2012 | 6     | 3439              | 114             | 139            | 7            |
| 2012 | 7     | 3660              | 136             | 151            | 9            |
| 2012 | 8     | 4673              | 174             | 204            | 10           |
| 2012 | 9     | 4227              | 172             | 264            | 16           |
| 2012 | 10    | 5197              | 219             | 337            | 15           |
| 2012 | 11    | 8506              | 356             | 383            | 17           |

---

### Answer Interpretation

The table provides a monthly breakdown of Gsearch sessions and orders, categorizing them into nonbrand and brand campaigns. This data analysis shows that both nonbrand and brand campaigns have seen growth over the eight-month period.

For brand campaigns, although the numbers are smaller compared to nonbrand campaigns, they have also been steadily increasing, indicating positive traction. This trend could be used as a compelling story to showcase the success and growth of the brand campaign during the presentation to the board.



## Problem 3

Dive into nonbrand traffic and pull monthly sessions and orders, split by device type. This will showcase the understanding of traffic sources and provide insights into device-specific performance.

### SQL Query

```sql
SELECT 
    YEAR(a.created_at) AS year, 
    MONTH(a.created_at) AS month,
    COUNT(DISTINCT a.website_session_id) AS sessions,
    COUNT(DISTINCT b.order_id) AS orders,
    COUNT(DISTINCT CASE WHEN a.device_type = 'mobile' THEN a.website_session_id ELSE NULL END) AS mobile_sessions,
    COUNT(DISTINCT CASE WHEN a.device_type = 'desktop' THEN a.website_session_id ELSE NULL END) AS desktop_sessions,
    COUNT(DISTINCT CASE WHEN a.device_type = 'mobile' THEN b.order_id ELSE NULL END) AS mobile_orders,
    COUNT(DISTINCT CASE WHEN a.device_type = 'desktop' THEN b.order_id ELSE NULL END) AS desktop_orders
FROM website_sessions a
LEFT JOIN orders b ON b.website_session_id = a.website_session_id
WHERE a.created_at < '2012-11-27'
AND a.utm_source = 'gsearch'
AND a.utm_campaign = 'nonbrand'
GROUP BY 1,2;
```

### Query Explanation

In this SQL query:

- We select the year and month of the `created_at` column to group the data on a monthly basis.
- We count the distinct website sessions and orders.
- We use conditional statements to count sessions and orders separately for mobile and desktop devices based on the `device_type` value.
- We perform a `LEFT JOIN` between the `website_sessions` and `orders` tables using the `website_session_id` to ensure all Gsearch sessions are included, whether they have orders or not.
- The data is filtered to include records created before November 27, 2012, where the `utm_source` is 'gsearch,' and the `utm_campaign` is 'nonbrand.' The results are grouped by year and month.

---

### Answer

The query returns the following result:

| Year | Month | Sessions | Orders | Mobile Sessions | Desktop Sessions | Mobile Orders | Desktop Orders |
|------|-------|----------|--------|-----------------|------------------|---------------|----------------|
| 2012 | 3     | 1852     | 60     | 724             | 1128             | 10            | 50             |
| 2012 | 4     | 3509     | 86     | 1370            | 2139             | 11            | 75             |
| 2012 | 5     | 3295     | 91     | 1019            | 2276             | 8             | 83             |
| 2012 | 6     | 3439     | 114    | 766             | 2673             | 8             | 106            |
| 2012 | 7     | 3660     | 136    | 886             | 2774             | 14            | 122            |
| 2012 | 8     | 4673     | 174    | 1158            | 3515             | 9             | 165            |
| 2012 | 9     | 4227     | 172    | 1056            | 3171             | 17            | 155            |
| 2012 | 10    | 5197     | 219    | 1263            | 3934             | 18            | 201            |
| 2012 | 11    | 8506     | 356    | 2049            | 6457             | 33            | 323            |

---

### Answer Interpretation

The table presents a monthly breakdown of Gsearch sessions and orders for the nonbrand campaign, categorized by mobile and desktop devices. This analysis highlights the company's in-depth understanding of its traffic sources, showcasing the traffic patterns for nonbrand Gsearch sessions on different devices.

The data demonstrates the growth and composition of nonbrand Gsearch sessions and orders, allowing the board to appreciate the company's analytical capabilities and its ability to track and optimize traffic sources effectively. This information can be used to impress the board during the presentation.

## Problem 4

Concerns may arise from one of the more pessimistic board members about the large percentage of traffic from Gsearch. Pull monthly trends for Gsearch, alongside monthly trends for each of the other traffic channels.

### SQL Query
```sql

-- First, find the various `utm_source` and `http_referer` values to see the traffic being received:

SELECT DISTINCT utm_source, utm_campaign, http_referer
FROM website_sessions
WHERE created_at < '2012-11-27';

-- Now, pull monthly trends for Gsearch and other channels:

SELECT 
    YEAR(a.created_at) AS year, 
    MONTH(a.created_at) AS month,
    COUNT(DISTINCT CASE WHEN a.utm_source = 'gsearch' THEN a.website_session_id ELSE NULL END) AS gsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN a.utm_source = 'bsearch' THEN a.website_session_id ELSE NULL END) AS bsearch_paid_sessions,
    COUNT(DISTINCT CASE WHEN a.utm_source IS NULL AND a.http_referer IS NOT NULL THEN a.website_session_id ELSE NULL END) AS organic_search_sessions,
    COUNT(DISTINCT CASE WHEN a.utm_source IS NULL AND a.http_referer IS NULL THEN a.website_session_id ELSE NULL END) AS direct_type_in_sessions
FROM website_sessions a
LEFT JOIN orders b ON b.website_session_id = a.website_session_id
WHERE a.created_at < '2012-11-27'
GROUP BY 1,2;
```

### Query Explanation

In this SQL query:

- The year and month of the `created_at` column are selected to group the data on a monthly basis.
- Conditional statements are used to count sessions for Gsearch, Bsearch, organic search, and direct type-ins.
- A `LEFT JOIN` is performed between the `website_sessions` and `orders` tables using `website_session_id` to ensure all sessions are included, whether they have orders or not.
- The data is filtered to include records created before November 27, 2012.
- The results are grouped by year and month.

---

### Answer (Part 1)

The result shows distinct UTM sources, UTM campaigns, and HTTP referrers:

| utm_source | utm_campaign | http_referer           |
|------------|--------------|------------------------|
| gsearch    | nonbrand     | https://www.gsearch.com |
| na         | na           | na                     |
| gsearch    | brand        | https://www.gsearch.com |
| na         | na           | https://www.gsearch.com |
| bsearch    | brand        | https://www.bsearch.com |
| na         | na           | https://www.bsearch.com |
| bsearch    | nonbrand     | https://www.bsearch.com |

---

### Answer (Part 2)

The query returns the following result:

| year | month | gsearch_paid_sessions | bsearch_paid_sessions | organic_search_sessions | direct_type_in_sessions |
|------|-------|-----------------------|-----------------------|-------------------------|-------------------------|
| 2012 | 3     | 1860                  | 2                     | 8                       | 9                       |
| 2012 | 4     | 3574                  | 11                    | 78                      | 71                      |
| 2012 | 5     | 3410                  | 25                    | 150                     | 151                     |
| 2012 | 6     | 3578                  | 25                    | 190                     | 170                     |
| 2012 | 7     | 3811                  | 44                    | 207                     | 187                     |
| 2012 | 8     | 4877                  | 705                   | 265                     | 250                     |
| 2012 | 9     | 4491                  | 1439                  | 331                     | 285                     |
| 2012 | 10    | 5534                  | 1781                  | 428                     | 440                     |
| 2012 | 11    | 8889                  | 2840                  | 536                     | 485                     |

---

### Answer Interpretation

The table provides a monthly breakdown of sessions for different traffic sources, including Gsearch, Bsearch, organic search, and direct type-ins. By presenting data from multiple channels, it showcases the diversity of traffic sources, which can help alleviate concerns about a high percentage of traffic from Gsearch.

This analysis allows the company to demonstrate a comprehensive understanding of its traffic sources, reinforcing the board's confidence in the company's ability to manage and diversify its marketing channels effectively. This information can be used to address concerns during the board meeting.

## Problem 5

A request is made to tell the story of website performance improvements over the first 8 months. The goal is to pull the session-to-order conversion rates, grouped by month.

### SQL Query

```sql
SELECT 
    YEAR(a.created_at) AS year, 
    MONTH(a.created_at) AS month,
    COUNT(DISTINCT a.website_session_id) AS sessions,
    COUNT(DISTINCT b.order_id) AS orders,
    ROUND(COUNT(DISTINCT b.order_id) / COUNT(DISTINCT a.website_session_id) * 100, 2) AS conv_rate
FROM 
    website_sessions a
LEFT JOIN 
    orders b 
    ON b.website_session_id = a.website_session_id
WHERE 
    a.created_at < '2012-11-27'
GROUP BY 
    1, 2;
```
### Query Explanation

In this SQL query:

- The year and month of the `created_at` column are selected to group the data on a monthly basis.
- The distinct website sessions and orders are counted.
- The session-to-order conversion rate is calculated by dividing the count of orders by the count of sessions and multiplying by 100 to express it as a percentage.
- The data is filtered to include records created before November 27, 2012.
- The results are grouped by year and month.

---

### Answer

The query returns the following result:

| year | month | sessions | orders | conv_rate |
|------|-------|----------|--------|-----------|
| 2012 | 3     | 1879     | 60     | 3.19%     |
| 2012 | 4     | 3734     | 99     | 2.65%     |
| 2012 | 5     | 3736     | 108    | 2.89%     |
| 2012 | 6     | 3963     | 140    | 3.53%     |
| 2012 | 7     | 4249     | 169    | 3.98%     |
| 2012 | 8     | 6097     | 228    | 3.74%     |
| 2012 | 9     | 6546     | 287    | 4.38%     |
| 2012 | 10    | 8183     | 371    | 4.53%     |
| 2012 | 11    | 12750    | 561    | 4.40%     |

---

### Answer Interpretation

The table provides a monthly overview of website performance improvements over the course of the first 8 months. It includes the number of sessions, the number of orders, and the session-to-order conversion rate expressed as a percentage.

The data shows an increase in both sessions and orders over the period, with the conversion rate gradually improving. This information allows the company to tell a positive story about its website's performance and growth. The board can gain confidence in the company's ability to optimize its website and convert more sessions into orders.

## Problem 6

For the Gsearch lander test, the goal is to estimate the revenue generated by the test. The approach is to look at the increase in the Conversion Rate (CVR) during the test period (June 19 – July 28), and use non-brand sessions and revenue since then to calculate the incremental value.

---

### SQL Query

```sql
-- first entry id of lander-1
SELECT MIN(website_pageview_id) AS first_test_pv
FROM website_pageviews
WHERE pageview_url = '/lander-1';
-- 23504 is the answer

WITH
cte1 AS (
    SELECT
        a.website_session_id, 
        MIN(a.website_pageview_id) AS min_pageview_id
    FROM website_pageviews a
    INNER JOIN website_sessions b
        ON b.website_session_id = a.website_session_id
        AND b.created_at < '2012-07-28' -- prescribed by the assignment
        AND a.website_pageview_id >= 23504 -- first pageview
        AND utm_source = 'gsearch'
        AND utm_campaign = 'nonbrand'
    GROUP BY a.website_session_id
),
cte2 AS (
    SELECT 
        a.website_session_id, 
        b.pageview_url AS landing_page
    FROM cte1 a
    LEFT JOIN website_pageviews b
        ON b.website_pageview_id = a.min_pageview_id
    WHERE b.pageview_url IN ('/home','/lander-1')
),
cte3 AS (
    SELECT
        a.website_session_id, 
        a.landing_page, 
        b.order_id AS order_id
    FROM cte2 a
    LEFT JOIN orders b
        ON b.website_session_id = a.website_session_id
)
SELECT landing_page, 
    COUNT(DISTINCT website_session_id) AS sessions, 
    COUNT(DISTINCT order_id) AS orders,
    ROUND((COUNT(DISTINCT order_id) / COUNT(DISTINCT website_session_id) * 100), 2) AS conv_rate
FROM cte3
GROUP BY 1;

-- finding the most recent pageview for Gsearch nonbrand where the traffic was sent to /home
SELECT 
    MAX(a.website_session_id) AS most_recent_gsearch_nonbrand_home_pageview 
FROM website_sessions a
LEFT JOIN website_pageviews b
    ON b.website_session_id = a.website_session_id
WHERE utm_source = 'gsearch'
    AND utm_campaign = 'nonbrand'
    AND pageview_url = '/home'
    AND a.created_at < '2012-11-27';
-- 17145

SELECT 
    COUNT(website_session_id) AS sessions_since_test
FROM website_sessions
WHERE created_at < '2012-11-27'
    AND website_session_id > 17145 -- last /home session
    AND utm_source = 'gsearch'
    AND utm_campaign = 'nonbrand';    
-- 22,972 website sessions since the test

-- X .0087 incremental conversion = 202 incremental orders since 7/29
-- roughly 4 months, so roughly 50 extra orders per month. Not bad!
```
### Query Explanation

In this SQL query:

- The entry ID for the "gsearch lander test" page (`/lander-1`) is first identified.
- Common Table Expressions (CTEs) are used to gather data for the test, which includes sessions, landing pages, and orders.
- The data is filtered to include sessions and orders within the specified date range and UTM conditions.
- The result provides the sessions, orders, and conversion rates for the "/home" and "/lander-1" pages.

---

### Answer (Part 1)

The query returns the following result:

| Landing Page | Sessions | Orders | Conversion Rate |
|--------------|----------|--------|-----------------|
| /home        | 2261     | 72     | 3.18%           |
| /lander-1    | 2316     | 94     | 4.06%           |

---

### Answer (Part 2)

The query returns the following result:

| Sessions Since Test |
|---------------------|
| 22,972              |

---

### Answer Interpretation

To estimate the incremental value from the "gsearch lander test," the conversion rates for the "/home" and "/lander-1" pages are compared. The "/lander-1" page had a higher conversion rate, indicating the effectiveness of the test.

To estimate revenue, the number of sessions since the test that meet the "gsearch nonbrand" and "/home" landing page criteria is considered, resulting in 22,972 sessions. Incremental orders are estimated by multiplying this count by the incremental conversion rate. The estimated incremental orders are roughly 202 since July 29, which equates to approximately 50 extra orders per month over a four-month period.

This analysis demonstrates the positive impact of the "gsearch lander test" in terms of conversion rate improvement and potential revenue increase, showcasing the value of such tests.

## Problem 7

For the landing page test previously analyzed, generate a full conversion funnel from each of the two landing pages to orders. The analysis uses the time period from **June 19 – July 28**.

---


```sql

-- CREATE TEMPORARY TABLE funnel

WITH cte1 AS (
    SELECT
        a.website_session_id, 
        b.pageview_url, 
        CASE WHEN pageview_url = '/home' THEN 1 ELSE 0 END AS homepage,
        CASE WHEN pageview_url = '/lander-1' THEN 1 ELSE 0 END AS custom_lander,
        CASE WHEN pageview_url = '/products' THEN 1 ELSE 0 END AS products_page,
        CASE WHEN pageview_url = '/the-original-mr-fuzzy' THEN 1 ELSE 0 END AS mrfuzzy_page, 
        CASE WHEN pageview_url = '/cart' THEN 1 ELSE 0 END AS cart_page,
        CASE WHEN pageview_url = '/shipping' THEN 1 ELSE 0 END AS shipping_page,
        CASE WHEN pageview_url = '/billing' THEN 1 ELSE 0 END AS billing_page,
        CASE WHEN pageview_url = '/thank-you-for-your-order' THEN 1 ELSE 0 END AS thankyou_page
    FROM website_sessions a
    LEFT JOIN website_pageviews b ON a.website_session_id = b.website_session_id
    WHERE a.utm_source = 'gsearch' 
        AND a.utm_campaign = 'nonbrand' 
        AND a.created_at < '2012-07-28'
        AND a.created_at > '2012-06-19'
    ORDER BY 
        a.website_session_id,
        b.created_at
)
SELECT
    website_session_id, 
    MAX(homepage) AS saw_homepage, 
    MAX(custom_lander) AS saw_custom_lander,
    MAX(products_page) AS product_made_it, 
    MAX(mrfuzzy_page) AS mrfuzzy_made_it, 
    MAX(cart_page) AS cart_made_it,
    MAX(shipping_page) AS shipping_made_it,
    MAX(billing_page) AS billing_made_it,
    MAX(thankyou_page) AS thankyou_made_it
FROM cte1 
GROUP BY 1;
```



```sql

-- Overall Numbers

SELECT
    CASE 
        WHEN saw_homepage = 1 THEN 'saw_homepage'
        WHEN saw_custom_lander = 1 THEN 'saw_custom_lander'
        ELSE 'ERROR 404!' 
    END AS segment, 
    COUNT(DISTINCT website_session_id) AS sessions,
    COUNT(DISTINCT CASE WHEN product_made_it = 1 THEN website_session_id ELSE NULL END) AS to_products,
    COUNT(DISTINCT CASE WHEN mrfuzzy_made_it = 1 THEN website_session_id ELSE NULL END) AS to_mrfuzzy,
    COUNT(DISTINCT CASE WHEN cart_made_it = 1 THEN website_session_id ELSE NULL END) AS to_cart,
    COUNT(DISTINCT CASE WHEN shipping_made_it = 1 THEN website_session_id ELSE NULL END) AS to_shipping,
    COUNT(DISTINCT CASE WHEN billing_made_it = 1 THEN website_session_id ELSE NULL END) AS to_billing,
    COUNT(DISTINCT CASE WHEN thankyou_made_it = 1 THEN website_session_id ELSE NULL END) AS to_thankyou
FROM funnel
GROUP BY 1;
```



```sql

-- Clickthrough Rates

SELECT
    CASE 
        WHEN saw_homepage = 1 THEN 'saw_homepage'
        WHEN saw_custom_lander = 1 THEN 'saw_custom_lander'
        ELSE 'ERROR 404!' 
    END AS segment, 
    COUNT(DISTINCT CASE WHEN product_made_it = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT website_session_id) AS lander_click_rt,
    COUNT(DISTINCT CASE WHEN mrfuzzy_made_it = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN product_made_it = 1 THEN website_session_id ELSE NULL END) AS products_click_rt,
    COUNT(DISTINCT CASE WHEN cart_made_it = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN mrfuzzy_made_it = 1 THEN website_session_id ELSE NULL END) AS mrfuzzy_click_rt,
    COUNT(DISTINCT CASE WHEN shipping_made_it = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN cart_made_it = 1 THEN website_session_id ELSE NULL END) AS cart_click_rt,
    COUNT(DISTINCT CASE WHEN billing_made_it = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN shipping_made_it = 1 THEN website_session_id ELSE NULL END) AS shipping_click_rt,
    COUNT(DISTINCT CASE WHEN thankyou_made_it = 1 THEN website_session_id ELSE NULL END) / COUNT(DISTINCT CASE WHEN billing_made_it = 1 THEN website_session_id ELSE NULL END) AS billing_click_rt
FROM funnel
GROUP BY 1;
```

### Query Explanation

In this SQL query:

1. **Funnel Creation**:
   - A temporary table "funnel" is created using Common Table Expressions (CTEs) to track users' progression through various pages, including:
     - Homepage
     - Custom Lander
     - Products Page
     - Mr. Fuzzy Page
     - Cart Page
     - Shipping Page
     - Billing Page
     - Thank-You Page.

2. **Overall Numbers**:
   - Calculates the number of sessions and the count of users who advance to different steps in the funnel.

3. **Clickthrough Rates**:
   - Calculates conversion rates at each stage of the funnel to identify drop-off points.

---

### Answer

### Overall Numbers:

| Segment            | Sessions | to Products | to Mr. Fuzzy | to Cart | to Shipping | to Billing | to Thank You |
|---------------------|----------|-------------|--------------|---------|-------------|------------|--------------|
| Saw Custom Lander   | 2,316    | 1,083       | 772          | 348     | 231         | 197        | 94           |
| Saw Homepage        | 2,261    | 942         | 684          | 296     | 200         | 168        | 72           |

### Clickthrough Rates:

| Segment            | Lander Click RT | Products Click RT | Mr. Fuzzy Click RT | Cart Click RT | Shipping Click RT | Billing Click RT |
|---------------------|-----------------|-------------------|---------------------|---------------|--------------------|------------------|
| Saw Custom Lander   | 0.4676          | 0.7128            | 0.4508              | 0.6638        | 0.8528             | 0.4772           |
| Saw Homepage        | 0.4166          | 0.7261            | 0.4327              | 0.6757        | 0.8400             | 0.4286           |

---

### Answer Interpretation

1. **Progression Analysis**:
   - The tables illustrate how users move through the funnel from two landing pages: `/home` (homepage) and `/lander-1` (custom lander).
   - The **Saw Custom Lander** segment has higher session counts and conversion rates at most stages compared to **Saw Homepage**.

2. **Key Insights**:
   - Users who land on **/lander-1** are more likely to progress through the funnel, particularly to the Products and Mr. Fuzzy pages.
   - The custom lander is likely more effective at engaging users and guiding them through the purchasing process.

3. **Optimization Opportunities**:
   - Leverage insights to refine the homepage design to match or exceed the custom lander's performance.
   - Focus on improving drop-off points identified in the funnel analysis to increase conversions further.

# Problem 8

**Analyze the lift generated from the billing test (Sep 10 – Nov 10), in terms of revenue per billing page session, and calculate the impact for the past month's billing page sessions.**

```sql
-- Quantify the impact of the billing test
WITH 
cte1 AS (
  SELECT 
    a.website_session_id, 
    a.pageview_url AS billing_version_seen, 
    b.order_id, 
    b.price_usd
  FROM website_pageviews a
  LEFT JOIN orders b
    ON b.website_session_id = a.website_session_id
  WHERE a.created_at > '2012-09-10' -- prescribed in assignment
    AND a.created_at < '2012-11-10' -- prescribed in assignment
    AND a.pageview_url IN ('/billing','/billing-2')
)
SELECT
  billing_version_seen, 
  COUNT(DISTINCT website_session_id) AS sessions, 
  ROUND(SUM(price_usd) / COUNT(DISTINCT website_session_id), 2) AS revenue_per_billing_page_seen
FROM cte1
GROUP BY 1;

-- Results:
-- $22.83 revenue per billing page seen for the old version
-- $31.34 revenue per billing page seen for the new version
-- LIFT: $8.51 per billing page view

-- Calculate billing sessions for the past month
SELECT 
  COUNT(website_session_id) AS billing_sessions_past_month
FROM website_pageviews 
WHERE pageview_url IN ('/billing','/billing-2') 
  AND created_at BETWEEN '2012-10-27' AND '2012-11-27'; -- past month

-- Results:
-- 1,194 billing sessions past month
-- LIFT: $8.51 per billing session
-- VALUE OF BILLING TEST: $10,160 over the past month
```
### Query Explanation

In this SQL query:

- **Revenue per billing page session** is calculated for two billing page versions (`/billing` and `/billing-2`) during the test period by joining the `website_pageviews` and `orders` tables.
- The **number of billing page sessions in the past month** (October 27, 2012, to November 27, 2012) is counted.

### Answer

#### Revenue per Billing Page Session During the Test:

| Billing Version | Sessions | Revenue per Billing Page Session |
|------------------|----------|----------------------------------|
| /billing         | 657      | $22.83                          |
| /billing-2       | 654      | $31.34                          |

#### Lift Generated by the Test:

- Old version (`/billing`): $22.83  
- New version (`/billing-2`): $31.34  
- Lift: $8.51 per billing page session

#### Number of Billing Page Sessions in the Past Month: 

- 1,193 sessions

#### Estimated Total Value: 

- $10,160 over the past month



### Answer Interpretation

The analysis highlights the impact of the billing test during the test period. The new billing page version (`/billing-2`) outperformed the old version (`/billing`) in terms of revenue per billing page session. With 1,193 billing page sessions in the past month, the lift of $8.51 per session results in an estimated additional value of **$10,160**.

---

# Conclusion

This analysis provides actionable insights into website performance, marketing campaigns, and testing strategies, showcasing their effectiveness and value. Below is a summary of the findings:

## Gsearch Performance

- **Problem 1**: Monthly trends in Gsearch sessions and orders showed consistent growth from March to November 2012, with conversion rates steady at around 4%.  
- **Problem 2**: Segmentation of Gsearch performance into non-brand and brand campaigns revealed success in both categories, with an upward trend in sessions and orders.  
- **Problem 3**: Non-brand Gsearch performance by device type showed consistent growth in mobile sessions and orders, emphasizing the importance of mobile optimization.

## Traffic Channels

- **Problem 4**: Analysis of traffic channels (Gsearch, Bsearch, organic, and direct) identified Gsearch as the primary driver of sessions, with significant growth from July to November.

## Website Performance

- **Problem 5**: Session-to-order conversion rates for the first eight months showed overall improvement, reflecting the success of performance enhancements.

## A/B Testing

- **Problem 6**: The Gsearch lander test generated approximately 50 extra orders per month, showcasing the positive impact on revenue.  
- **Problem 7**: The billing test demonstrated the effectiveness of the new billing page version, resulting in a lift of $8.51 per session and an estimated $10,160 additional revenue in the past month.

## Key Takeaways

- Gsearch has been a major driver of website traffic and orders, with both non-brand and brand campaigns contributing to growth.  
- Mobile traffic is a key factor, highlighting the need for mobile-optimized strategies.  
- A/B tests, such as the lander and billing tests, have shown measurable revenue improvements and enhanced user experience.  
- Conversion funnels provide valuable insights into user behavior and are effective for tracking website performance.  
- The website's overall performance improvements have resulted in higher conversion rates over time.

Regular monitoring and analysis of these metrics will be essential for sustaining and enhancing performance. These findings should guide future strategies and decision-making processes.
