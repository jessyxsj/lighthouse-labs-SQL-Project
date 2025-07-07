What are your risk areas? Identify and describe them.
Values are missing from the city and country columns in the all_sessions table.
Currency amounts are incorrectly presented in the millions in the all_sessions and analytics tables.
Product SKUs have different format. Some are numeric only, others are characters combined with numbers. 
The time on site column didn't say what unit it was in

QA Process:
Describe your QA process and include the SQL queries used to execute it.
Getting a general sense of the data, tables 


```
SELECT  * FROM all_sessions; --getting a general sense of the table

SELECT COUNT(*),
	COUNT (DISTINCT fullvisitorid)
FROM all_sessions; -- looking at whether fullvisitorid is unique in all_sessions table

SELECT 
	MIN(timeonsite),
	MAX(timeonsite),
	AVG(timeonsite)
FROM all_sessions; --looking at the timeonsite value,  and trying to determine the whether it is in seconds
```
The all_sessions table contains website activity data, and has many many columns to describe website visits. There are 15134 rows but only 14223 lines containing unique values in the fullvisitorid column. Columns for totaltransactionrevenue, transactions, productrefundamount, searchkeyword and productrevenue, etc, contain many NULL values. For the country and city columns, many lines don't have a city or country name, which will raise problems for observations on data based country and city. For the timeonsite column, the Minimum is 1, maximum is 4661, and average is 224, so assume that it is in seconds, which makes sense.

```
SELECT * FROM analytics; 
-- notice that userid is null, and also has fullvisitorid

SELECT * FROM analytics
WHERE userid is NOT NULL; --shows no userid is stored

SELECT 
	COUNT (*),
	COUNT (DISTINCT fullvisitorid),
	COUNT (DISTINCT visitid)
FROM analytics; --shows that fullvisitorid is not unique for analytics table


```

The table analytics is a very large table, gives data from site activity. It seems that neither the fullvisitorid nor the visitid is unique. The channelgrouping column shows how visitors found the site (i.e. referred by another site, from a search or directly typing in the URL, etc). 

```
SELECT COUNT(*), 
	COUNT(DISTINCT productsku)
FROM sales_by_sku;

SELECT COUNT(*), 
	COUNT(DISTINCT productsku)
FROM sales_report; --shows that productsku is unique in both the sales_by_sku table and sales_report table, but sales_by_sku contains 462 records while sales_report contains 454 records

```
SELECT * FROM sales_by_sku;
SLECT * FROM sales_report;--shows that sales_by_sku is basically the first two columns pulled out from the sales_report table

SELECT * 
FROM sales_by_sku
JOIN sales_report USING (productsku);

SELECT * 
FROM sales_by_sku
FULL OUTER JOIN sales_report USING (productsku); --shows that sales_by_sku has 8 more records than sales_report, and the other 454 records match in two tables
```

