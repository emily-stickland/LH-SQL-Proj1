Question 1: Does the products.sentiment_score have any correlation with the amounts ordered by SKU?

SQL Queries:
    SELECT 	CORR (score, orders) as correlation
    FROM (
        SELECT
            p.product_sku as sku,
            p.product_name, 
            p.sentiment_score as score,
            sbs.total_ordered as orders
    FROM products p

    FULL OUTER JOIN sales_by_sku sbs
    ON  p.product_sku  = sbs.product_sku
        ) as a

Answer: 
    "correlation"   0.021823458853711324
    Very weak correlation, pretty much none


Question 2: What are the top ordered products by category?

SQL Queries:
    SELECT 
        v2_product_category,
	    SUM(sbs.total_ordered) as ordered_amt
    FROM sessions s
    INNER JOIN sales_by_sku sbs
        ON s.product_sku = sbs.product_sku
        WHERE v2_product_category != '(not set)'
    GROUP BY v2_product_category
    ORDER BY ordered_amt DESC
    limit 10

Answer:
    "Home/Shop by Brand/YouTube/"	                31352
    "Home/Nest/Nest-USA/"	                        24110
    "Home/Drinkware/"	                            21901
    "Home/Office/"	                                21027
    "Home/Shop by Brand/Google/"	                10879
    "Home/Bags/"	                                8629
    "Home/Office/Notebooks & Journals/"	            8504
    "Home/Electronics/"	                            6595
    "Home/Drinkware/Water Bottles and Tumblers/"	5556
    "Home/Apparel/Men's/Men's-T-Shirts/"	        5337



Question 3: who spent the most time per page?

SQL Queries:
    SELECT 	full_visitor_id,
		page_views,
		time_on_site,
		(time_on_site / page_views)::numeric (5,2) as avg_time_per_page
    FROM sessions
        WHERE page_views >0 AND time_on_site is not null
    ORDER BY avg_time_per_page DESC
    limit 10

Answer:
"full_visitor_id"	    "page_views"	"time_on_site"	"avg_time_per_page"
"4344524303428134441"	    2	            1718	            859.00
"3897651648778359490"	    2	            1710	            855.00
"1869599090210681270"	    2	            1657	            828.50
"0349198466881148447"	    2	            1650	            825.00
"7011681108778678989"	    2	            1646	            823.00
"0927682010281318434"	    3	            2467	            822.33
"1309382816398247582"	    2	            1621	            810.50
"8401779914861806064"	    2	            1607	            803.50
"3211307282081510146"	    2	            1601	            800.50
"6684417199282789854"	    2	            1596	            798.00



Question 4: Is there a correlation between time on site and number of pages viewed?

SQL Queries:

    SELECT 	CORR(page_views, time_on_site)
        FROM sessions
        WHERE page_views >0 AND time_on_site is not null

Answer:
    0.38462485217869896
    There is a weak positive correlation



Question 5: What percentage of visitors to the site go on to make a purchase?

SQL Queries:
    WITH T AS 
        (SELECT COUNT(DISTINCT full_visitor_id) AS total_visitors,
            COUNT(transactions) AS total_purchases
            FROM sessions
        )
    SELECT total_purchases::FLOAT / total_visitors::FLOAT * 100 AS percent_purchased 
    FROM T

Answer: 

0.56% of visitors make a purchase.

