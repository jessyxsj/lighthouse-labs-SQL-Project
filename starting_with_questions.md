Answer the following questions and provide the SQL queries used to find the answer.

    
**Question 1: Which cities and countries have the highest level of transaction revenues on the site?**


SQL Queries:
```
SELECT 
	country,
	SUM(totaltransactionrevenue) AS totaltransactionbycountry
FROM all_sessions_clean
WHERE totaltransactionrevenue IS NOT NULL
	AND country != 'N/A'
GROUP BY country
ORDER BY totaltransactionbycountry DESC
LIMIT 5;

SELECT 
	city,
	SUM(totaltransactionrevenue) AS totaltransactionbycity
FROM all_sessions_clean
WHERE totaltransactionrevenue IS NOT NULL
	AND city != 'N/A'
GROUP BY city
ORDER BY totaltransactionbycity DESC
LIMIT 5;
```

Answer:
USA has the highest level of transaction revenue

|country      |totaltransactionbycountry|
|-------------|-------------------------|
|United States|13154.169999999996       |
|Israel       |602                      |
|Australia    |358                      |
|Canada       |150.14999999999998       |
|Switzerland  |16.99                    |


San Francisco leads as the city revenue

|city         |totaltransactionbycity|
|-------------|----------------------|
|San Francisco|1564.32               |
|Sunnyvale    |992.23                |
|Atlanta      |854.44                |
|Palo Alto    |608                   |
|Tel Aviv-Yafo|602                   |




**Question 2: What is the average number of products ordered from visitors in each city and country?**

SQL Queries:
```
SELECT
	country,
	AVG(units_sold) AS average_num_sold
FROM all_sessions_clean
JOIN analytics_clean USING (fullvisitorid)
WHERE units_sold IS NOT NULL
GROUP BY country
ORDER BY average_num_sold DESC;

-- **Question 2: What is the average number of products ordered from visitors in each city and country?**
SELECT
	city,
	AVG(units_sold) AS average_num_sold
FROM all_sessions_clean
JOIN analytics_clean USING (fullvisitorid)
WHERE units_sold IS NOT NULL
	AND city != 'N/A'
GROUP BY city
ORDER BY average_num_sold DESC;
```


Answer: 
the average number of products ordered from visitors in each  country (attaching the top  10 countries), The USA leads Average Units Sold by country 

|country      |average_num_sold|
|-------------|----------------|
|United States|19.2449215143120960|
|Czechia      |15.1818181818181818|
|Mexico       |1.8333333333333333|
|Canada       |1.5934065934065934|
|Bulgaria     |1.5000000000000000|
|Germany      |1.2500000000000000|
|Hong Kong    |1.2321428571428571|
|Japan        |1.1351351351351351|
|India        |1.1111111111111111|
|Finland      |1.00000000000000000000|


the average number of products ordered from visitors in each  city (attaching the top 10), San Bruno leads Average Units Sold by city

|city         |average_num_sold|
|-------------|----------------|
|San Bruno    |52.6666666666666667|
|Mountain View|16.1651376146788991|
|San Jose     |8.5652173913043478|
|Salem        |7.5454545454545455|
|New York     |6.8953488372093023|
|Chicago      |6.1941176470588235|
|Nashville    |5.3333333333333333|
|Jersey City  |5.2727272727272727|
|Charlotte    |4.6467065868263473|
|Sunnyvale    |4.0688073394495413|




**Question 3: Is there any pattern in the types (product categories) of products ordered from visitors in each city and country?**


SQL Queries:
```
WITH country_productcategory AS (
    SELECT
        country,
        v2productcategory,
        SUM(units_sold) AS total_units_sold
    FROM all_sessions_clean
    JOIN analytics_clean USING(fullvisitorid)
    WHERE units_sold IS NOT NULL
        AND country != 'N/A'
    GROUP BY country, v2productcategory
),
ranked_productcategories AS (
    SELECT
        country,
        v2productcategory,
        total_units_sold,
        RANK() OVER (PARTITION BY country ORDER BY total_units_sold ASC) AS rank_asc,
        RANK() OVER (PARTITION BY country ORDER BY total_units_sold DESC) AS rank_desc
    FROM country_productcategory
)
SELECT
    country,
    v2productcategory,
    total_units_sold
FROM ranked_productcategories
WHERE rank_asc = 1 OR rank_desc = 1;

```

```
WITH city_productcategory AS (
    SELECT
        city,
        v2productcategory,
        SUM(units_sold) AS total_units_sold
    FROM all_sessions_clean
    JOIN analytics_clean USING(fullvisitorid)
    WHERE units_sold IS NOT NULL
        AND city != 'N/A'
    GROUP BY city, v2productcategory
),
ranked_productcategories AS (
    SELECT
        city,
        v2productcategory,
        total_units_sold,
        RANK() OVER (PARTITION BY city ORDER BY total_units_sold ASC) AS rank_asc,
        RANK() OVER (PARTITION BY city ORDER BY total_units_sold DESC) AS rank_desc
    FROM city_productcategory
)
SELECT
    city,
    v2productcategory,
    total_units_sold
FROM ranked_productcategories
WHERE rank_asc = 1 OR rank_desc = 1;
```

Answer: 
This finds out the most sold and least sold product types in each country(only listing a couple of countries as example):In the USA, the most sold product is from home/accessories/pet; and in Canana, the most sold is Home/Shop by Brand/YouTube/

|country      |v2productcategory|total_units_sold|
|-------------|-----------------|----------------|
|Canada       |Home/Drinkware/Water Bottles and Tumblers/|1               |
|Canada       |Home/Electronics/|1               |
|Canada       |Home/Shop by Brand/YouTube/|45              |
|Czechia      |(not set)        |167             |
|United Kingdom|Home/Shop by Brand/YouTube/|1               |
|United Kingdom|Home/Bags/       |6               |
|United Kingdom|(not set)        |6               |
|United States|Home/Accessories/Drinkware/|1               |
|United States|Home/Apparel/Kid's/Kids-Youth/|1               |
|United States|Home/Accessories/Pet/|22665           |

Below is the the most sold and least sold product types in each city (only listing a couple): In Ann Arbor, the most sold product is from Home/Electronics, and least sold is from Home/Apparel/Women's/Women's-T-Shirts

|city         |v2productcategory|total_units_sold|
|-------------|-----------------|----------------|
|Ahmedabad    |(not set)        |2               |
|Ann Arbor    |Home/Apparel/Women's/Women's-T-Shirts/|1               |
|Ann Arbor    |Home/Electronics/|79              |
|Atlanta      |Home/Bags/       |1               |
|Atlanta      |Home/Shop by Brand/Google/|1               |
|Atlanta      |Home/Apparel/Men's/Men's-T-Shirts/|1               |
|Atlanta      |Home/Drinkware/  |25              |
|Austin       |Home/Office/     |1               |
|Austin       |Home/Apparel/Men's/Men's-Performance Wear/|1               |
|Austin       |Home/Office/Notebooks & Journals/|162             |
|Bangkok      |Home/Bags/       |2               |
|Bangkok      |Home/Drinkware/  |4               |


**Question 4: What is the top-selling product from each city/country? Can we find any pattern worthy of noting in the products sold?**

Top-selling products could be described as the number of units sold  by city/country.

SQL Queries:
```
WITH country_product AS (
    SELECT
        country,
        v2productname,
        SUM(units_sold) AS total_units_sold
    FROM all_sessions_clean
    JOIN analytics_clean USING(fullvisitorid)
    WHERE units_sold IS NOT NULL
        AND country != 'N/A'
    GROUP BY country, v2productname
),
ranked_product AS (
    SELECT
        country,
        v2productname,
        total_units_sold,
        RANK() OVER (PARTITION BY country ORDER BY total_units_sold DESC) AS rank
    FROM country_product
)
SELECT
    country,
    v2productname,
    total_units_sold
FROM ranked_product
WHERE rank = 1 ;
```
```
WITH city_product AS (
    SELECT
        city,
        v2productname,
        SUM(units_sold) AS total_units_sold
    FROM all_sessions_clean
    JOIN analytics_clean USING(fullvisitorid)
    WHERE units_sold IS NOT NULL
        AND city != 'N/A'
    GROUP BY city, v2productname
	),
ranked_product AS (
    SELECT
        city,
        v2productname,
        total_units_sold,
        RANK() OVER (PARTITION BY city ORDER BY total_units_sold DESC) AS rank
    FROM city_product
)
SELECT
    city,
    v2productname,
    total_units_sold
FROM ranked_product
WHERE rank = 1 ;
```

Answer: 
the top-selling product from each country is as follow (showing the top 10 countries):

|country      |v2productname|total_units_sold|
|-------------|-------------|----------------|
|United States|Google Collapsible Pet Bowl|11328           |
|United States|7&quot; Dog Frisbee|11328           |
|Czechia      |Google Snapback Hat Black|167             |
|Hong Kong    |Google Device Stand|120             |
|Canada       |YouTube Leatherette Notebook Combo|42              |
|Japan        |Google Lunch Bag|27              |
|Taiwan       |Spiral Notebook and Pen Set|16              |
|Germany      |22 oz YouTube Bottle Infuser|12              |
|Egypt        |Android RFID Journal|10              |
|Mexico       |Nest® Cam Indoor Security Camera - USA|10              |


And the top-selling product from each city is as follow (showing the top cities):

|city         |v2productname                                       |total_units_sold|
|-------------|----------------------------------------------------|----------------|
|Mountain View|Waze Baby on Board Window Decal                     |5807            |
|New York     |Collapsible Shopping Bag                            |1410            |
|San Bruno    |22 oz YouTube Bottle Infuser                        |1397            |
|Charlotte    |Google Lunch Bag                                    |776             |
|Sunnyvale    |Google Hard Cover Journal                           |501             |
|San Jose     |22 oz Android Bottle                                |347             |
|Chicago      |Google Men's 100% Cotton Short Sleeve Hero Tee White|337             |
|Chicago      |Google Alpine Style Backpack                        |337             |
|Salem        |Google Men's  Zip Hoodie                            |246             |
|Los Angeles  |Basecamp Explorer Powerbank Flashlight              |163             |

Each country has a unique top-selling product, with no repetitions across countries.The products range from pet accessories (e.g., Google Collapsible Pet Bowl) to tech gadgets (e.g., Nest® Cam Indoor Security Camera) and stationery items (e.g., YouTube Leatherette Notebook Combo).
Similar to the country data, each city has a unique top-selling product.
The products vary widely, including decals (e.g., Waze Baby on Board Window Decal), bags (e.g., Collapsible Shopping Bag), and apparel (e.g., Google Men's Zip Hoodie).
There isn't a single product that dominates sales across multiple countries or cities.



**Question 5: Can we summarize the impact of revenue generated from each city/country?**

SQL Queries:
```
SELECT  
	country,
	SUM(totaltransactionrevenue) AS total_revenue,
	COUNT(DISTINCT fullvisitorid) AS visitors,
	COUNT(*) AS total_sessions
FROM all_sessions_clean
WHERE totaltransactionrevenue IS NOT NULL
GROUP BY country
ORDER BY total_revenue DESC;

SELECT  
	city,
	SUM(totaltransactionrevenue) AS total_revenue,
	COUNT(DISTINCT fullvisitorid) AS visitors,
	COUNT(*) AS total_sessions
FROM all_sessions_clean
WHERE totaltransactionrevenue IS NOT NULL
	AND city != 'N/A'
GROUP BY city
ORDER BY total_revenue DESC;
```


Answer:
This answers the total revenue of each city and country is from how many visitors and how many sessions

|country      |total_revenue     |visitors|total_sessions|
|-------------|------------------|--------|--------------|
|United States|13154.169999999995|75      |76            |
|Israel       |602               |1       |1             |
|Australia    |358               |1       |1             |
|Canada       |150.14999999999998|2       |2             |
|Switzerland  |16.99             |1       |1             |

Eg. The total revenue of the USA is 13154 USD, and is from 76 sessions of 75 visitors.

|city         |total_revenue     |visitors|total_sessions|
|-------------|------------------|--------|--------------|
|San Francisco|1564.3200000000002|12      |12            |
|Sunnyvale    |992.23            |4       |4             |
|Atlanta      |854.44            |2       |2             |
|Palo Alto    |608               |3       |3             |
|Tel Aviv-Yafo|602               |1       |1             |
|New York     |598.35            |9       |9             |
|Mountain View|483.36            |8       |8             |
|Los Angeles  |479.48            |2       |2             |
|Chicago      |449.52            |3       |3             |
|Sydney       |358               |1       |1             |
|Seattle      |358               |1       |1             |
|San Jose     |262.38            |2       |2             |
|Austin       |157.78            |2       |2             |
|Nashville    |157               |1       |1             |
|San Bruno    |103.77            |1       |1             |
|Toronto      |82.16             |1       |1             |
|Houston      |38.98             |1       |1             |
|Columbus     |21.99             |1       |1             |
|Zurich       |16.99             |1       |1             |



