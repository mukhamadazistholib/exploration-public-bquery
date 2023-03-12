# Exploration Public Bigquery

### Problem
From this public table `data-to-insights.ecommerce.rev_transactions`.
Create an efficient query which derives the total transactions per date and country based on the channel
grouping!

### Documentation

1. Showing the dataset table of `data-to-insights.ecommerce.rev_transactions`.
```sql
SELECT * FROM `data-to-insights.ecommerce.rev_transactions` LIMIT 100
```
![img](asset/Screenshot%20(252).png)

2. Calculating how many unique visitors reached <strong>checkout transactions.</strong>
```sql
SELECT
  COUNT(DISTINCT fullVisitorId) as visitor_count, 
  hits_page_pageTitle
FROM `data-to-insights.ecommerce.rev_transactions`
WHERE hits_page_pageTitle = "Checkout Confirmation"
GROUP BY hits_page_pageTitle
```
![img](asset/Screenshot%20(253).png)

3. Showing the cities with the most transactions with your eCommerce site.
```sql
SELECT
  geoNetwork_city,
  SUM(totals_transactions) AS totals_transactions,
  COUNT( DISTINCT fullVisitorId) AS distinct_visitors
FROM
  `data-to-insights.ecommerce.rev_transactions`
GROUP BY
  geoNetwork_city
ORDER BY 
  distinct_visitors DESC
```
![img](asset/Screenshot%20(254).png)

4. Creating a new calculated field to return the average number of products per order by city.
```sql
SELECT
  geoNetwork_city,
  SUM(totals_transactions) AS total_products_ordered,
  COUNT( DISTINCT fullVisitorId) AS distinct_visitors,
  SUM(totals_transactions) / COUNT( DISTINCT fullVisitorId) AS avg_products_ordered,
FROM
  `data-to-insights.ecommerce.rev_transactions`
GROUP BY
  geoNetwork_city
ORDER BY
  avg_products_ordered DESC
```
![img](asset/Screenshot%20(255).png)

5. Filter aggregated results to only return cities with more than 20 `avg_products_ordered`. <strong>Notes: Alias cannot be used in where clause</strong>
```sql
SELECT
geoNetwork_city,
SUM(totals_transactions) AS total_products_ordered,
COUNT( DISTINCT fullVisitorId) AS distinct_visitors,
SUM(totals_transactions) / COUNT( DISTINCT fullVisitorId) AS avg_products_ordered
FROM
`data-to-insights.ecommerce.rev_transactions`
GROUP BY
geoNetwork_city
HAVING
avg_products_ordered > 20
ORDER BY
avg_products_ordered DESC
```
![img](asset/Screenshot%20(256).png)

6. Finding the top-selling products by filtering with NULL values.
```sql
SELECT
COUNT(DISTINCT hits_product_v2ProductName) as number_of_products,
hits_product_v2ProductCategory
FROM `data-to-insights.ecommerce.rev_transactions`
WHERE hits_product_v2ProductName IS NOT NULL
GROUP BY hits_product_v2ProductCategory
ORDER BY number_of_products DESC
LIMIT 10
```
![img](asset/Screenshot%20(257).png)

7. Identifying which records are duplicated across all columns.
```sql
SELECT COUNT(*) as num_duplicate_rows, * FROM
`data-to-insights.ecommerce.all_sessions_raw`
GROUP BY
fullVisitorId, channelGrouping, time, country, city, totalTransactionRevenue, transactions, timeOnSite, pageviews, sessionQualityDim, date, visitId, type, productRefundAmount, productQuantity, productPrice, productRevenue, productSKU, v2ProductName, v2ProductCategory, productVariant, currencyCode, itemQuantity, itemRevenue, transactionRevenue, transactionId, pageTitle, searchKeyword, pagePathLevel1, eCommerceAction_type, eCommerceAction_step, eCommerceAction_option
HAVING num_duplicate_rows > 1;
```
![img](asset/Screenshot%20(258).png)

8. Confirm that no duplicates exist, this time in the `all_sessions` table.
```sql
SELECT
fullVisitorId, 
visitId, 
date, 
time, 
v2ProductName, 
productSKU,
type, 
eCommerceAction_type,
eCommerceAction_step,
eCommerceAction_option,
  transactionRevenue,
  transactionId,
COUNT(*) as row_count
FROM
`data-to-insights.ecommerce.all_sessions`
GROUP BY 1,2,3 ,4, 5, 6, 7, 8, 9, 10,11,12
HAVING row_count > 1 
```
![img](asset/Screenshot%20(259).png)

9. Calculate the total views by counting product_views and the number of unique visitors by counting `fullVisitorID`.
```sql
SELECT
  COUNT(*) AS product_views,
  COUNT(DISTINCT fullVisitorId) AS unique_visitors
FROM `data-to-insights.ecommerce.all_sessions`;
```
![img](asset/Screenshot%20(260).png)

10. Calculate the total unique visitors `(fullVisitorID)` by the referring site (channelGrouping).
```sql
SELECT
  COUNT(DISTINCT fullVisitorId) AS unique_visitors,
  channelGrouping
FROM `data-to-insights.ecommerce.all_sessions`
GROUP BY channelGrouping
ORDER BY channelGrouping DESC;
```
![img](asset/Screenshot%20(261).png)

11. Show the list  of all the unique product names `(v2ProductName)` alphabetically.
```sql
SELECT
  (v2ProductName) AS ProductName
FROM `data-to-insights.ecommerce.all_sessions`
GROUP BY ProductName
ORDER BY ProductName
```
![img](asset/Screenshot%20(262).png)

13. Show the list of most views product `(product_views)` from all visitors (including people who have viewed the same product more than once).
```sql
SELECT
  COUNT(*) AS product_views,
  (v2ProductName) AS ProductName
FROM `data-to-insights.ecommerce.all_sessions`
WHERE type = 'PAGE'
GROUP BY v2ProductName
ORDER BY product_views DESC
LIMIT 5;
```
![img](asset/Screenshot%20(263).png)

14. Refine the query to no longer double-count product views for visitors who have viewed a product many times.
```sql
WITH unique_product_views_by_person AS (
-- find each unique product viewed by each visitor
SELECT
 fullVisitorId,
 (v2ProductName) AS ProductName
FROM `data-to-insights.ecommerce.all_sessions`
WHERE type = 'PAGE'
GROUP BY fullVisitorId, v2ProductName )
-- aggregate the top viewed products and sort them
SELECT
  COUNT(*) AS unique_view_count,
  ProductName
FROM unique_product_views_by_person
GROUP BY ProductName
ORDER BY unique_view_count DESC
LIMIT 5
```
![img](asset/Screenshot%20(264).png)

15. The query to include the total number of distinct products ordered and the total number of total units ordered (productQuantity).
```sql
SELECT
  COUNT(*) AS product_views,
  COUNT(productQuantity) AS orders,
  SUM(productQuantity) AS quantity_product_ordered,
  v2ProductName
FROM `data-to-insights.ecommerce.all_sessions`
WHERE type = 'PAGE'
GROUP BY v2ProductName
ORDER BY product_views DESC
LIMIT 5;
```
![img](asset/Screenshot%20(265).png)

16. The query to include the average amount of product per order (total number of units ordered/total number of orders, or `SUM(productQuantity)/COUNT(productQuantity))`.
```sql
SELECT
  COUNT(*) AS product_views,
  COUNT(productQuantity) AS orders,
  SUM(productQuantity) AS quantity_product_ordered,
  SUM(productQuantity) / COUNT(productQuantity) AS avg_per_order,
  (v2ProductName) AS ProductName
FROM `data-to-insights.ecommerce.all_sessions`
WHERE type = 'PAGE'
GROUP BY v2ProductName
ORDER BY product_views DESC
LIMIT 5;
```
![img](asset/Screenshot%20(266).png)


#### Contributor
mukhamadazistholib278[at]gmail[dot]com
