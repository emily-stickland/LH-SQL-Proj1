# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
As per assignment

## Process
1: clean data:  check various synonyms of null and set to null, 
                check for and delete redundant columns, 
                change dates to proper date format
                change prices by dividing by 1000000

2: analyse data: see 'starting_with-questions' and 'starting_with data_ for info

3: transform data: assign primary keys, produce ERD

## Results
-the data can tell us users' behaviour on the site and how this might lead to transactions. It tells us that less than 1% of people visiting the site went on to make purchases, even those who looked at multiple pages.

-the data contains a product sentiment score, but this seems entirely unconnected from the amounts of each product ordered - I'm not sure how it was calculated.

-the data can tell us how long people spend on the site and how many pages they view. Perhaps unsurprisingly, there is a positive correlation between time spent on site and pages viewed. If this were not the case, we might determine that there was a problem with the data.

## Challenges 
-There were insufficient transactions (80) to run proper analysis on the transactions, especially geographically
-The sessions spreadsheet seemed disconnected from sales_by_sku and sales_report, in that it had far fewer items ordered. However, the sales_by_sku and the sales_report were consistent, in that they contained the same number of products ordered by the same sku

## Future Goals
-find out at what stage the users leave the site, i.e. why are so few users making purchases? At what stage of the transaction do they leave the site?
-with more transactions, we could discern way more about the sales of this company
