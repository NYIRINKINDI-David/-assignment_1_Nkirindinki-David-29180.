Sunrise Supermarket — Assignment 1

Name: NYIRINKINDI David Student ID: 29180 Group: C

Database Management System Used

Oracle Database (run using Oracle SQL Developer).

Business Scenario

Sunrise Supermarket needs a database to track its customers, products, and the orders customers place. Each order can contain multiple products (order items), and products are grouped into categories (Beverages, Snacks, Dairy, Bakery). This assignment builds that schema, populates it with sample data, and writes queries that answer business questions using JOINs, a CTE, and window functions.

Files in This Repository
sunrise_supermarket.sql — full schema, sample data, and all queries
screenshots/ — screenshot of each query's result in SQL Developer
README.md — this file
How to Run
Open Oracle SQL Developer and connect to your database using your username/password.
Open a new SQL worksheet.
Run the schema + sample data section of sunrise_supermarket.sql first (creates and populates the customers, products, orders, and order_items tables).
Run each query in the second half of the file individually to see its result.

Note: Oracle does not support DROP TABLE IF EXISTS. If you are re-running the script and the tables already exist, drop them manually first, e.g.: DROP TABLE order_items CASCADE CONSTRAINTS; (repeat for the other 3 tables, in reverse dependency order), then run the CREATE TABLE statements again.

Schema Overview
Table	Description
customers	6 customers (one, Grace, has no orders — used to demonstrate LEFT JOIN)
products	8 products across 4 categories: Beverages, Snacks, Dairy, Bakery
orders	15 orders placed between Jan–Feb 2026
order_items	25 line items linking orders to products with quantities
Queries
1. INNER JOIN — Orders with customer details

Lists every order alongside the customer's name, city, and the date the order was placed, by joining orders to customers on customer_id.

Business use: lets staff see, at a glance, who placed each order and where they're based — useful for delivery planning or regional sales analysis.

<img width="342" height="213" alt="join1_inner" src="https://github.com/user-attachments/assets/4b688a0b-6678-4068-8752-05f867fb108d" />


2. JOIN — Order items with product details

Lists every individual order line item together with the product's name, category, price, and the quantity ordered, by joining order_items to products.

Business use: shows exactly what was sold in each line item — the basis for calculating revenue per product or per category.

<img width="349" height="219" alt="join2_orderitems_part1" src="https://github.com/user-attachments/assets/fcad7d2f-f74a-4311-bfc5-5f00f545e74e" />


3. LEFT JOIN — All customers, including those with no orders

Lists every customer and any orders they've placed, keeping customers with zero orders in the result (their order columns show as empty) by using a LEFT JOIN instead of an INNER JOIN.

Business use: identifies customers who have never ordered — a target list for a re-engagement or welcome-back marketing campaign.

<img width="482" height="221" alt="join3_leftjoin" src="https://github.com/user-attachments/assets/ef733bc9-0dea-4a6b-9e51-147a4b8aaee2" />


4. CTE — Customers spending above the average

Uses a CTE (customer_totals) to first calculate each customer's total spend (quantity × price, summed across all their orders), then filters to only the customers whose total is above the average spend across all customers.

Business use: flags top-value customers — good candidates for a loyalty program or VIP outreach.

<img width="412" height="90" alt="cte_abovespend" src="https://github.com/user-attachments/assets/3de2ac0e-c62f-4a76-a2ac-a12d29bcd6a8" />


5. Window Function — RANK customers by total spend

Reuses the same total-spend calculation, then applies RANK() OVER (ORDER BY total_spend DESC) to assign each customer a rank from highest spender to lowest.

Business use: produces a leaderboard of customers by value, without needing a separate query per customer.

<img width="445" height="103" alt="window1_rank" src="https://github.com/user-attachments/assets/d02e5661-952c-4971-92c8-34db57976e14" />


6. Window Function — Number each customer's orders in sequence

Uses ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) to number each customer's own orders 1, 2, 3... in the order they were placed.

Business use: helps identify each customer's first order (useful for measuring onboarding or repeat-purchase behavior).

<img width="443" height="214" alt="window2_rownumber" src="https://github.com/user-attachments/assets/eb6961b2-9398-4bb5-9222-1c36e2ffc8d2" />


7. Window Function — Running total of revenue over time

Calculates each order's total revenue, then uses SUM(order_total) OVER (ORDER BY order_date, order_id) to build a running (cumulative) total of revenue as orders come in chronologically.

Business use: lets management track cumulative sales growth over the period at a glance, without exporting to a spreadsheet.

<img width="526" height="227" alt="window3_runningtotal" src="https://github.com/user-attachments/assets/baca5b40-8ca9-4c9f-938b-4c5758a3393a" />


8. Window Function — Days between a customer's consecutive orders

Uses LAG(order_date) OVER (PARTITION BY customer_id ORDER BY order_date) to find each customer's previous order date, then subtracts it from the current order date. Only customers with more than one order appear (first orders, with no previous order, are filtered out).

Business use: measures how frequently each customer reorders — useful for planning when to send a "time to restock?" reminder email.

<img width="512" height="227" alt="window4_lag" src="https://github.com/user-attachments/assets/8fd75634-ba7c-416a-85bd-8050038e696c" />


Challenges & Solutions
[Describe here any Oracle installation or connection issues you ran into, and how you resolved them.]
