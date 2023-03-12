# Exploration Public Bigquery

### Problem
From this public table `data-to-insights.ecommerce.rev_transactions`.
Create an efficient query which derives the total transactions per date and country based on the channel
grouping!

### Documentation

1. Showing the dataset table of `data-to-insights.ecommerce.rev_transactions`.
![img](asset/Screenshot%20(252).png)

2. Calculating how many unique visitors reached <strong>checkout transactions.</strong>
![img](asset/Screenshot%20(253).png)

3. Showing the cities with the most transactions with your eCommerce site.
![img](asset/Screenshot%20(254).png)

4. Creating a new calculated field to return the average number of products per order by city.
![img](asset/Screenshot%20(255).png)

5. Filter aggregated results to only return cities with more than 20 avg_products_ordered. <strong>Notes: Alias cannot be used in where clause</strong>
![img](asset/Screenshot%20(256).png)

6. Finding the top-selling products by filtering with NULL values.
![img](asset/Screenshot%20(257).png)

7. Identifying which records are duplicated across all columns.
![img](asset/Screenshot%20(258).png)

8. Confirm that no duplicates exist, this time in the all_sessions table.
![img](asset/Screenshot%20(259).png)

9. Calculate the total views by counting product_views and the number of unique visitors by counting fullVisitorID.
![img](asset/Screenshot%20(260).png)

10. Calculate the total unique visitors(fullVisitorID) by the referring site (channelGrouping).
![img](asset/Screenshot%20(261).png)

11. Show the list  of all the unique product names (v2ProductName) alphabetically.
![img](asset/Screenshot%20(262).png)

13. Show the list of most views product (product_views) from all visitors (including people who have viewed the same product more than once).
![img](asset/Screenshot%20(263).png)

14. Refine the query to no longer double-count product views for visitors who have viewed a product many times.
![img](asset/Screenshot%20(264).png)

15. The query to include the total number of distinct products ordered and the total number of total units ordered (productQuantity).
![img](asset/Screenshot%20(265).png)

16. The query to include the average amount of product per order (total number of units ordered/total number of orders, or SUM(productQuantity)/COUNT(productQuantity)).
![img](asset/Screenshot%20(266).png)
