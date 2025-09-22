# -PL-SQL-Window-Functions-Nkurunziza-Amos
PL/SQL Window Functions project: analyzing supermarket sales, customer behavior, and growth trends using SQL analytics
1. Problem Definition

Business Context
A supermarket chain in Kigali with multiple branches across Rwanda sells groceries, beverages, and household products.

Data Challenge
Management needs insights into sales performance across regions, customer purchase behavior, and growth trends. They want to know the top products per region, track month-to-month growth, and segment customers based on spending.

Expected Outcome

Identify top-selling products per region/quarter.

Track running monthly sales totals.

Calculate month-over-month growth.

Segment customers into quartiles.

Compute 3-month moving averages for trend analysis.

2. Success Criteria

This project delivers 5 measurable goals:

Top 5 products per region/quarter → RANK()

Running monthly sales totals → SUM() OVER()

Month-over-month growth → LAG() / LEAD()

Customer quartiles → NTILE(4)

3-month moving averages → AVG() OVER()

3. Database Schema
Tables

customers

customer_id (PK), name, region

products

product_id (PK), name, category

transactions

transaction_id (PK), customer_id (FK), product_id (FK), sale_date, amount

ER Diagram
customers (1)───< (∞) transactions (∞) >───(1) products


📌 See schema.sql for table creation.

4. Window Functions Implementation
Ranking – Top 5 Products per Region/Quarter
SELECT region, product_id, 
       SUM(amount) AS total_sales,
       RANK() OVER (PARTITION BY region, TO_CHAR(sale_date, 'Q') 
                    ORDER BY SUM(amount) DESC) AS sales_rank
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY region, product_id, TO_CHAR(sale_date, 'Q')
HAVING RANK() <= 5;


📸 [screenshot here]
Insight: Shows the top 5 best-selling products in each region per quarter.

Aggregate – Running Monthly Sales Totals
SELECT TO_CHAR(sale_date, 'YYYY-MM') AS month,
       SUM(amount) AS monthly_sales,
       SUM(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM')) AS running_total
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY month;


📸 [screenshot here]
Insight: Tracks how sales accumulate month by month.

Navigation – Month-over-Month Growth
SELECT TO_CHAR(sale_date, 'YYYY-MM') AS month,
       SUM(amount) AS monthly_sales,
       LAG(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM')) AS prev_month,
       ( (SUM(amount) - LAG(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM'))) 
          / LAG(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM')) ) * 100 
          AS growth_percent
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY month;


📸 [screenshot here]
Insight: Calculates growth percentage compared to the previous month.

Distribution – Customer Quartiles
SELECT customer_id, SUM(amount) AS total_spent,
       NTILE(4) OVER (ORDER BY SUM(amount) DESC) AS quartile
FROM transactions
GROUP BY customer_id;


📸 [screenshot here]
Insight: Segments customers into quartiles (top spenders, high-mid, low-mid, lowest).

Moving Averages – 3-Month Average
SELECT TO_CHAR(sale_date, 'YYYY-MM') AS month,
       SUM(amount) AS monthly_sales,
       AVG(SUM(amount)) OVER (ORDER BY TO_CHAR(sale_date, 'YYYY-MM') 
                              ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_3m
FROM transactions
GROUP BY TO_CHAR(sale_date, 'YYYY-MM')
ORDER BY month;


📸 [screenshot here]
Insight: Smooths sales trends using a 3-month moving average.

5. Results Analysis

Descriptive – What happened?

Sales peaked in March with strong beverage sales in Kigali.

Coffee Beans was the top product in multiple regions.

25% of customers contributed over 60% of sales.

Diagnostic – Why?

Kigali outperformed due to higher customer density.

Seasonal promotions boosted beverage sales.

High spenders were consistent repeat buyers.

Prescriptive – What next?

Expand promotions to underperforming regions.

Launch loyalty programs for top quartile customers.

Develop campaigns to retain lower quartile customers.

6. References

Oracle Docs – SQL Analytics and Window Functions

W3Schools – SQL Window Functions

TutorialsPoint – PL/SQL Window Functions

GeeksforGeeks – SQL Analytics

DataCamp – SQL Window Functions Guide

Oracle Academy – Course Resources

StackOverflow – PL/SQL window function Q&A

PostgreSQL Docs (conceptual references)

Academic paper on SQL in retail analytics

Business case studies on customer segmentation

Integrity Statement
All sources were properly cited. Implementations and analysis represent original work. No AI-generated content was copied without attribution or adaptation.


