What are your risk areas? Identify and describe them.

Duplicated data giving skewed/inaccurate results
Incomplete/missing data for transactions



QA Process:
Describe your QA process and include the SQL queries used to execute it.


1: Check that sales_report.total_ordered  =  sales_by_sku.total_ordered 
    use sales_by_sku.total_ordered as the primary key

    --first, check for duplicated SKUs in each
        SELECT product_sku, count(*)
            from sales_report
        GROUP BY product_sku
        ORDER by count DESC

    --no duplicated SKUs, so use SKU as primary key to check that the ordered amounts in both tables are the same
        SELECT sbs.product_sku AS sbs_sku, 
            sr.product_sku AS sr_sku,
            sbs.total_ordered AS sbs_total_ordered, 
            sr.total_ordered AS sr_total_ordered,
            sbs.total_ordered - sr_total_ordered as comparison
        from sales_by_sku sbs
        JOIN sales_report sr
        ON sbs.product_sku = sr.product_sku
        ORDER BY comparison DESC

    --nothing >0, so total_ordered matches for each SKU in each table

2: Does the product_quantity or product_revenue from the sessions table bear any relation to the sales_by_sku or the sales_report tables?

        SELECT SUM(product_quantity)
            FROM sessions
    --answer = 189
    --this is obviously far lower than the quantities represented in the sales_by_sku and sales_report
        SELECT SUM(total_ordered)
            FROM sales_by_sku
        SELECT SUM(total_ordered)
            FROM sales_report
    --answers are 5524 and 5519 (why are these not the same? Maybe one SKU is represented in one table but not the other?