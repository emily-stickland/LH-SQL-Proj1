Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**

SQL Queries:

    SELECT country, SUM(total_transaction_revenue::numeric(10, 2)) as revenue
        FROM sessions
        WHERE transactions >0
    GROUP BY country
    ORDER BY revenue DESC

Answer:
    1:  "United States"	`   13030.17
    2:  "Israel"	        602.00
    3:  "Australia"	        358.00
    4:  "Canada"	        150.15
    5:  "Switzerland"	    16.99

SQL Queries:
    SELECT city, SUM(total_transaction_revenue::numeric(10, 2)) as revenue
        FROM sessions
        WHERE transactions >0
        AND city is not null
    GROUP BY city
    ORDER BY revenue DESC
    LIMIT 5

Answer:

    1:  "San Francisco"	    1564.32
    2:  "Sunnyvale"	        992.23
    3:  "Atlanta"	        854.44
    4:  "Palo Alto"	        608.00
    5:  "Tel Aviv-Yafo"	    602.00



**Question 2: What is the average number of products ordered from visitors in each city and country?**
SQL Queries:
  SELECT country, 
            city, 
            ROUND(AVG(product_quantity), 1) AS avg
        FROM sessions
        WHERE city is not null 
            AND product_quantity >0
    GROUP BY country, city
    ORDER BY avg DESC

Answer:
    "Spain"	        "Madrid"	    10.0
    "United States"	"Salem"	        8.0
    "United States"	"Atlanta"	    4.0
    "United States"	"Houston"	    2.0
    "United States"	"New York"	    1.2
    "United States"	"Dallas"	    1.0
    "United States"	"Detroit"	    1.0
    "United States"	"Los Angeles"	1.0
    "United States"	"Mountain View"	1.0
    "United States"	"Palo Alto"	    1.0
    "United States"	"San Francisco"	1.0
    "United States"	"San Jose"	    1.0
    "United States"	"Seattle"	    1.0
    "India"	        "Bengaluru"    	1.0
    "United States"	"Sunnyvale"	    1.0
    "Ireland"	    "Dublin"	    1.0
    "United States"	"Ann Arbor"	    1.0
    "United States"	"Chicago"	    1.0
    "United States"	"Columbus"	    1.0

**Question 2b: What is the average number of transactions made from visitors in each city and country?**

SQL Queries:
    SELECT 	country, 
        AVG(transactions)
    FROM sessions
        WHERE transactions >0
    GROUP BY country

    Answer: Average number of transactions is 1.


**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**

SQL Queries:
    SELECT  v2_product_category, 
            country, 
            COUNT(v2_product_category) as number_purchases
        FROM sessions
        WHERE transactions >0
	GROUP BY  country, v2_product_category
 	ORDER BY number_purchases DESC

Answer:
    The only instances where more than 2 transactions were made from the same product category from the same country were:
        "Home/Nest/Nest-USA/"	"United States"	                    17
        "Nest-USA"	"United States"	                                7
        "Apparel"	"United States"	                                6
        "Home/Apparel/Men's/Men's-T-Shirts/"	"United States"	    5
        "Home/Apparel/Kid's/Kid's-Infant/"	"United States"	        3
        "Home/Apparel/Women's/Women's-T-Shirts/"	"United States"	3

    Seems like the USA is buying a lot of "nest" but the data is too limited (only 80 transactions) to discern anything of value

SQL Queries:
    SELECT v2_product_category, city, COUNT(v2_product_category) as number_purchases
        FROM sessions
        WHERE transactions >0
        AND city is not null
    GROUP BY  city, v2_product_category
    ORDER BY number_purchases DESC

Answer:
    "Home/Nest/Nest-USA/"	"San Francisco"	3
    "Apparel"	"Mountain View"	2
    "Home/Nest/Nest-USA/"	"Palo Alto"	2
    "Home/Shop by Brand/Google/"	"Austin"	1
    "Home/Office/Writing Instruments/"	"Chicago"	1

    Most of the USA purchases are in California and, again, are Nest.

**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**


SQL Queries:  
    SELECT  v2_product_name, 
        country, 
        COUNT(v2_product_name) as number_purchases
   FROM sessions
   WHERE transactions >0
    GROUP BY v2_product_name, country
    ORDER BY number_purchases DESC
    limit 5


Answer:

    1. "Nest® Learning Thermostat 3rd Gen-USA - Stainless Steel"	        "United States"	    7
    2. "Nest® Cam Outdoor Security Camera - USA"	                        "United States" 	5
    3. "Nest® Learning Thermostat 3rd Gen-USA"	                            "United States"	    3
    4. "Nest® Cam Indoor Security Camera - USA"	                            "United States"	    2
    5. "Google Men's 100% Cotton Short Sleeve Hero Tee Navy"	            "United States"	    2

    16."Google Men's  Zip Hoodie"	                                        "Canada"	`       1

    Nest is popular in the US. There are very few sales outside the US. Nest is not popular in any other countries.

**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:

    WITH T AS 
            (SELECT country, SUM(total_transaction_revenue::numeric(10,2)) as revenue
            FROM sessions
            WHERE transactions >0
        GROUP BY country
        ORDER BY revenue DESC
        ) 
    SELECT  country, 
        t.revenue / SUM(t.revenue) OVER() *100 AS percent_rev 
    FROM T


Answer:
    "United States"     92.03845928357858943500
    "Israel"            4.25222023110322511800
    "Australia"         2.52872897464278171500
    "Canada"            1.06058283671121138100
    "Switzerland"       0.12000867396419235000







