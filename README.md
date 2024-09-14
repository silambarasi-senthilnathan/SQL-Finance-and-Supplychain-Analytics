# SQL-Finance-and-Supplychain-Analytics

This project involved working with a real dataset containing over 1 million records. The aim was to simulate industry-style data analysis and optimization tasks.

## Key Skills & Concepts Covered:

- **SQL Basics**: Mastered foundational commands like `SELECT`, `WHERE`, `BETWEEN`, `GROUP BY`, `ORDER BY` for data querying and filtering.
- **Joins**: Implemented various types of joins (e.g., `LEFT`, `RIGHT`, `INNER`, `FULL`, `CROSS`) to combine data from multiple tables efficiently.
- **Subqueries & CTEs**: Utilized **subqueries** and **Common Table Expressions (CTEs)** for modular and readable query designs.
- **Views & Temporary Tables**: Created **views** and managed **temporary tables** for reusable and transient data storage.
- **Data Warehousing**: Gained insights into **ETL processes**, **Data Warehouses**, and both **OLAP** and **OLTP** systems for efficient data processing.
- **User-Defined Functions (UDFs) & Stored Procedures**: Wrote custom **UDFs** and **stored procedures** to handle complex data manipulations.
- **Window Functions**: Used window functions like `ROW_NUMBER()`, `RANK()`, and `DENSE_RANK()` for advanced data ranking and partitioning.

## SQL Code Examples

### Gross Sales Report

This SQL query generates a gross sales report by joining sales, product, and gross price data.

```sql
SELECT 
    s.date, 
    s.product_code, 
    p.product, 
    p.variant, 
    s.sold_quantity, 
    g.gross_price,
    ROUND(g.gross_price * s.sold_quantity, 2) AS gross_price_total
FROM fact_sales_monthly s
JOIN dim_product p ON p.product_code = s.product_code
JOIN fact_gross_price g ON g.product_code = s.product_code 
    AND g.fiscal_year = GET_FISCAL_YEAR(s.date)
WHERE customer_code = 90002002 
    AND GET_FISCAL_YEAR(date) = 2021
ORDER BY date ASC
LIMIT 1000000;
```
### CTE Query: Forecast Error Table

This query calculates forecast errors and accuracy metrics and joins the results with customer information.
```sql
WITH forecast_err_table AS (
    SELECT 
        customer_code,
        SUM(CAST(forecast_quantity AS SIGNED) - CAST(sold_quantity AS SIGNED)) AS net_error,
        SUM(CAST(forecast_quantity AS SIGNED) - CAST(sold_quantity AS SIGNED)) * 100 / SUM(forecast_quantity) AS net_error_pct,
        SUM(ABS(CAST(forecast_quantity AS SIGNED) - CAST(sold_quantity AS SIGNED))) AS abs_error,
        SUM(ABS(CAST(forecast_quantity AS SIGNED) - CAST(sold_quantity AS SIGNED))) * 100 / SUM(forecast_quantity) AS abs_error_pct
    FROM gdb0041.act_est e
    WHERE e.fiscal_year = 2018
    GROUP BY customer_code
)
SELECT 
    c.customer,
    c.market,
    f.sold_quantity,
    f.forecast_quantity,
    f.net_error_pct,
    f.abs_error,
    f.abs_error_pct,
    IF(f.abs_error_pct > 100, 0, 100 - f.abs_error_pct) AS forecast_accuracy
FROM forecast_err_table f
JOIN dim_customer c ON f.customer_code = c.customer_code
ORDER BY forecast_accuracy ASC;
```

This project sharpened my SQL skills by simulating real-world data analytics challenges, providing me with the ability to work with large datasets in a professional setting.
