What issues will you address by cleaning the data?
Data cleaning focussed on the all_sessions and the analytics tables. The tables all_sessions_clean and analytics_clean were combined into table allsession_analytics_combined to facilitate analysis, retaining the columns to be used in later analysis.

Issues identified and corrected in the all_sessions table:

Country and City columns are missing a significant number of named country/city. These were standardized to 'N/A'.
The columns totaltransactionrevenue, productprice,productrevenue are given incorrectly in the millions. These columns are reduced by dividing by 1,000,000  to the appropriate amount.

Issues identified and corrected in the analytics table:

The units_sold column contains at least one negative value. This is treated as a typo, the negative removed.
The unit_price column is, like revenue above, incorrectly stored as millions. 


Queries:
Below, provide the SQL queries you used to clean your data.
-- Create table views of cleaned data for future use.
-- This query cleans the all_sessions table.
-- standardize missing data as 'N/A'
-- money amounts are incorrectly encoded in the millions. These are recalculated

```CREATE OR REPLACE VIEW all_sessions_clean AS 
	SELECT 
		fullvisitorid,
		time,
		CASE 
			WHEN country IN ('(not set)') THEN 'N/A'
			ELSE country END,
		CASE 
			WHEN city IN ('not available in demo dataset', '(not set)') THEN 'N/A'
			ELSE city END,
		totaltransactionrevenue/1000000 AS totaltransactionrevenue,
		transactions, 
		timeonsite,
		pageviews,
		date,
		visitid,
		type, 
		productrefundamount,
		productquantity,
		productprice/1000000 AS productprice,
		productrevenue/1000000 AS productrevenue,
		productsku,
		v2productname,
		v2productcategory,
		productvariant,
		itemquantity,
		itemrevenue/1000000 AS itemrevenue,
		transactionrevenue/1000000 AS transactionrevenue,
		transactionid,
		pagetitle
	FROM all_sessions;```

-- This query cleans the analytics table.

```CREATE OR REPLACE VIEW analytics_clean AS
	SELECT visitnumber,
		visitid,
		visitstarttime,
		date,
		fullvisitorid,
		userid,
		channelgrouping,
		socialengagementtype,
		ABS(units_sold) AS units_sold,
		pageviews,
		timeonsite,
		bounces,
		revenue/1000000 AS revenue,
		unit_price/1000000 AS unit_price
	FROM analytics```
	






