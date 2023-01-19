What issues will you address by cleaning the data?

1: Putting dates and times into proper datetime formats (had to import them as int): 
    a) sessions.date
    b) analytics.date
    c) analytics.visit_starttime

2: Change unit price
    a) sessions.product_price divided by 1,000,000
    b) analytics.unit_price needs to be divided by 1,000,000
    c) sessions.total_transaction_revenue needs to be divided by 1,000,000
 
3: Do something with null values
    a) sessions.city has two different things for null values: "not available in demo dataset" and "(not set)"

4: De-duplicate from sessions where visit_id is duplicated

5: Get rid of redundant spaces in products.name

6: Assign primary keys

7: Delete columns with all null values?
    a) sessions.product_variant    - all values are "not set". There's no column in any other table that can give us this information, so I'm going to delete it
    b) sessions.search_keyword     - looks all empty
    c) sessions.product_refund_amt	- looks empty, but we might want to keep this for future use?
    d) sessions.product_quantity    - looks empty, but we might want to keep this for future use?
    e) sessions.item_quantity - looks empty
    f) sessions.item_revenue - looks empty
    g) sessions.transaction_revenue - looks empty
    h) sessions.transaction_id - looks empty
    i) sessions.ecommerce_action_option - looks empty
    j) analytics.time_on_site - looks empty 
    k) analytics.revenue - looks empty 
    l) analytics.user_id - looks empty 
    m) analytics.social_engagement_type - looks like all values say "Not Socially Engaged"


Queries:
Below, provide the SQL queries you used to clean your data.

1: Putting dates and times into proper datetime formats

    a) --Apparently you can't convert INT to DATE so first need to convert it to VARCHAR

        ALTER TABLE sessions 
	        ALTER COLUMN date TYPE VARCHAR(100);

        ALTER TABLE sessions 
	        ALTER COLUMN date TYPE DATE USING date::date

        --testing that all the years were input in the correct format (i.e. it's not throwing any weird dates) (just checking that the years are correct, I think that is enough for now)

        SELECT 
	        EXTRACT(year from date) as year
                FROM sessions
            GROUP BY year
        ORDER BY year

        --all data is from 2016 and 2017

    b) --same as (a)
        ALTER TABLE analytics 
	        ALTER COLUMN date TYPE VARCHAR(100);
	    ALTER TABLE analytics 
	        ALTER COLUMN date TYPE DATE USING date::date;
        
        --test as above: all data is from 2016

    c) ALTER TABLE analytics
        ALTER COLUMN visit_starttime TYPE VARCHAR(100);


2: Changing price
    a)   ALTER TABLE sessions
            ADD product_price2 FLOAT(50)
        UPDATE sessions 
            SET product_price2 = product_price/1000000

        or
        UPDATE sessions 
            SET product_price2 = product_price/1000000

    b)  UPDATE analytics
            SET unit_price = unit_price/1000000
    
    c)  UPDATE sessions 
            SET total_transaction_revenue = total_transaction_revenue/1000000

3: Changing data to null if it has other strings denoting this
a)  --sessions.city
    UPDATE sessions
        SET city = null
        WHERE city = 'not available in demo dataset'
    UPDATE sessions
        SET city = null
        WHERE city = '(not set)'

4: de-duplicate from sessions using visit_id (primary key)
    SELECT COUNT(*)
        from sessions 
        GROUP BY visit_id
        HAVING count(visit_id)>1
    --553 rows returned where visit_id is duplicated
    --to delete duplicates, we need to assign each row an id, then delete one of them

    ALTER TABLE sessions
        ADD COLUMN session_id SERIAL;
    ALTER TABLE sessions
        ADD PRIMARY KEY (session_id)
    DELETE FROM sessions a
        USING sessions b
        WHERE a.session_id > b.session_id
        AND a.visit_id = b.visit_id
    --deleted 578 (why not 553??)

5: Get rid of redundant spaces in products.name
     SELECT LTRIM(product_name)
        FROM products

6: Assign primary keys
    a)  analytics:
            There is no natural primary key, since even visit_id is duplicated across multiple rows (even where eg product price is different)
    b)  sessions:
            Primary key generated above as session_id
    c)  sales_report:
             There is no natural primary key. Generate one:
                ALTER TABLE sales_report
                ADD COLUMN sales_pkey SERIAL PRIMARY KEY 
    d)  sales_by_sku: SKUs are unique and should be the primary key
                ALTER TABLE sales_by_sku
                ADD PRIMARY KEY(product_sku)
    e)  products: SKUs are unique and should be the primary key
                ALTER TABLE products
                ADD PRIMARY KEY(product_sku)

7: Deleting columns with all null values
a)  -- sessions.product_variant - all values seem to be "not set"
    -- first, check how many rows there are
        SELECT COUNT(*) from sessions (15134)
        --then, check that all values in product_variant = "not set"
        SELECT COUNT(product_variant)
            from sessions
            WHERE product_variant = '(not set)'    (15094)

        --check what the other values are
        SELECT product_variant
            from sessions
            WHERE product_variant != '(not set)'
        --looks like there is data, so will not delete

    b)  --sessions.search_keyword-looks empty
        --first, check all values are null
        SELECT search_keyword
            from sessions
            WHERE search_keyword is not NULL
        --returns no rows, so delete
        ALTER TABLE sessions
            DROP COLUMN search_keyword

    c)  --sessions.product_refund_amt	
        SELECT	COUNT(*)
            from sessions
            WHERE product_refund_amt is not null
        -- count is zero
        ALTER TABLE sessions
            DROP COLUMN product_refund_amt

    d)  --sessions.product_quantity
        SELECT COUNT(*)
            from sessions
            WHERE product_quantity is not null  
        --returns 53 results. 

    e)  --sessions.item_quantity 
        SELECT COUNT (*)
            from sessions
            WHERE item_quantity is null
        --returns 0
        ALTER TABLE sessions
            DROP COLUMN item_quantity
    
    f) --sessions.item_revenue
        SELECT COUNT(*) 
            from sessions
            WHERE item_revenue is not null
        ALTER TABLE sessions
            DROP COLUMN item_revenue

    g)  --sessions.transaction_revenue 
        SELECT COUNT(*) 
            from sessions
            WHERE transaction_revenue is not null
        --returns 4

    h)  --sessions.transaction_id
        SELECT COUNT(*) 
            from sessions
            WHERE transaction_id is not null
        --returns 9

    i)  --sessions.ecommerce_action_option
        SELECT COUNT(*) 
            from sessions
            WHERE ecommerce_action_option is not null
        --returns 8
      
    j)  --analytics.time_on_site
        SELECT	COUNT(*)
            from analytics
            WHERE time_on_site is not null
        --returns 3823657 results, do not delete

    k)  --analytics.revenue
        SELECT	revenue
            from analytics
            WHERE revenue is not null
        --returns 15355 results, do not delete

    il)  --analytics.user_id
        SELECT	user_id
            from analytics
            WHERE user_id is not null
        --returns 0 results, so delete
        ALTER TABLE analytics
            DROP COLUMN user_id
    
    m)  --analytics.user_social_engagement
        SELECT COUNT(*)
            from analytics
            WHERE social_engagement_type != 'Not Socially Engaged'
        --returns 0 results
        ALTER TABLE analytics
            DROP COLUMN social_engagement_type

 
