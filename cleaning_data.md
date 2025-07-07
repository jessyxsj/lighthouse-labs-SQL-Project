# Data Cleaning

## What issues will you address by cleaning the data?

Data cleaning focused on the `all_sessions` and the `analytics` tables. The tables `all_sessions_clean` and `analytics_clean` were combined into table `allsession_analytics_combined` to facilitate analysis, retaining the columns to be used in later analysis.

## Issues Identified and Corrected

### Issues in the all_sessions table:

- **Country and City columns**: Missing a significant number of named country/city values. These were standardized to 'N/A'.
- **Revenue columns**: The columns `totaltransactionrevenue`, `productprice`, and `productrevenue` are given incorrectly in the millions. These columns are corrected by dividing by 1,000,000 to get the appropriate amount.

### Issues in the analytics table:

- **Negative values**: The `units_sold` column contains at least one negative value. This is treated as a data entry error, with the negative sign removed using the ABS() function.
- **Unit price scaling**: The `unit_price` column is, like revenue columns above, incorrectly stored as millions and needs to be divided by 1,000,000. 

## SQL Queries Used for Data Cleaning

### Cleaning the all_sessions table

The following query creates a cleaned view of the `all_sessions` table, standardizing missing data as 'N/A' and correcting money amounts that were incorrectly encoded in the millions:

```sql
CREATE OR REPLACE VIEW all_sessions_clean AS 
    SELECT 
        fullvisitorid,
        time,
        CASE 
            WHEN country IN ('(not set)') THEN 'N/A'
            ELSE country 
        END AS country,
        CASE 
            WHEN city IN ('not available in demo dataset', '(not set)') THEN 'N/A'
            ELSE city 
        END AS city,
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
    FROM all_sessions;
```

### Cleaning the analytics table

The following query creates a cleaned view of the `analytics` table, removing negative values from `units_sold` and correcting the scaling of price and revenue columns:

```sql
CREATE OR REPLACE VIEW analytics_clean AS
    SELECT 
        visitnumber,
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
    FROM analytics;
```
