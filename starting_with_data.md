Question 1: How many unique visitors (fullvisitorid) are from each group of channel?

SQL Queries:

```
SELECT 
	channelgrouping,
	COUNT(DISTINCT fullvisitorid)
FROM all_sessions
GROUP BY channelgrouping;

```

Answer: 

|channelgrouping|count|
|---------------|-----|
|(Other)        |5    |
|Affiliates     |245  |
|Direct         |2805 |
|Display        |119  |
|Organic Search |8207 |
|Paid Search    |470  |
|Referral       |2419 |

 Organic Search is the leading channel with 8207 unique visitors. Display ads brought in only 119 visitors. It looks like Organic Search is a major driver of traffic for the site. 


Question 2: What channel group brought the highest revenue?

SQL Queries:

```
SELECT 
	channelgrouping,
	SUM(totaltransactionrevenue) AS totalrevenue
FROM all_sessions_clean
GROUP BY channelgrouping
ORDER BY totalrevenue DESC;

```

Answer:

|channelgrouping|totalrevenue      |
|---------------|------------------|
|Affiliates     |NULL              |
|Display        |NULL              |
|(Other)        |NULL              |
|Referral       |6018.679999999999 |
|Direct         |4704.1900000000005|
|Organic Search |3163.6699999999996|
|Paid Search    |394.77            |

It looks like Referral traffic is generating the highest revenue (6018 USD), followed by Direct and Organic Search. And No revenue data available (NULL) showed for Affiliates, Display, and (Other). This could indicate that visitors coming from referral links are more likely to convert into paying customers.

Combined with the first question, despite having fewer visitors, Referral traffic has a higher conversion rate and revenue per visitor compared to other channels. This insight can help  prioritize and optimize referral strategies to maximize revenue.


Question 3: The top 10 countries with the highest number of visitors

SQL Queries:

```
SELECT 
	country,
	COUNT(fullvisitorid) AS numvisitors
FROM all_sessions_clean
GROUP BY country
ORDER BY numvisitors DESC
LIMIT 10
```

Answer:

|country       |numvisitors|
|--------------|-----------|
|United States |8727       |
|India         |719        |
|United Kingdom|668        |
|Canada        |642        |
|Germany       |336        |
|Japan         |241        |
|Australia     |225        |
|France        |218        |
|Taiwan        |174        |
|Netherlands   |158        |

The United States is clearly the largest source of traffic, followed by India and the United Kingdom. This information can help tailor marketing strategies to target these key regions more effectively.

Question 4: How many visitors are there in each month for different product categories

SQL Queries:

```
SELECT 
	v2productcategory,
	COUNT(DISTINCT fullvisitorid ) AS numvisitors,
	EXTRACT(MONTH FROM date) AS month
FROM all_sessions
GROUP BY v2productcategory, month
ORDER BY v2productcategory, numvisitors DESC;

```
Answer:

This will give insight about the monthly visitor trends for different product categories. Here I only take the Home/Apparel category as an example:

|v2productcategory|numvisitors|month|
|-----------------|-----------|-----|
|Home/Apparel/    |142        |8    |
|Home/Apparel/    |128        |9    |
|Home/Apparel/    |80         |10   |
|Home/Apparel/    |65         |7    |
|Home/Apparel/    |63         |11   |
|Home/Apparel/    |47         |12   |
|Home/Apparel/    |47         |6    |
|Home/Apparel/    |47         |1    |
|Home/Apparel/    |46         |4    |
|Home/Apparel/    |43         |5    |
|Home/Apparel/    |41         |3    |
|Home/Apparel/    |38         |2    |

It looks like the Home/Apparel category sees the highest traffic in August and September. This could be due to seasonal trends, back-to-school shopping, or other factors.

Question 5: what products are the most negatively commented ?

SQL Queries:

```
SELECT MIN (sentimentscore),
	MAX(sentimentscore),
	MIN(sentimentmagnitude),
	MAX(sentimentmagnitude)
FROM products; -- range of the scentimentscore and sentimentmagnitude (0.1-2)

SELECT 
	sku, 
	name,
	sentimentscore,
	sentimentmagnitude
FROM products
WHERE sentimentscore < 0
	AND sentimentmagnitude > 1;


```

Answer:

A sentiment score is a numerical rating that represents the overall sentiment or emotion expressed in a piece of text.
Sentiment magnitude measures the overall strength or intensity of emotion expressed in a piece of text.
A negative score with a high magnitude indicates a strong negative sentiment

|sku           |name                                               |sentimentscore|sentimentmagnitude|
|--------------|---------------------------------------------------|--------------|------------------|
|GGOEGAAX0592  | Men's Airflow 1/4 Zip Pullover Black              |-0.2          |1.5               |
|GGOEGBMC056599|Waterproof Gear Bag                                |-0.1          |1.2               |
|GGOEGAAX0282  |Android Women's Short Sleeve Badge Tee Dark Heather|-0.4          |1.2               |
|GGOEGAAX0168  | Pet Feeding Mat                                   |-0.4          |1.2               |
|GGOEGAAX0351  | Men's Vintage Henley                              |-0.5          |1.4               |
|GGOEGEHQ024099|Clip-on Compact Charger                            |-0.3          |1.4               |
|GGOEGAAX0344  | Women's Vintage Hero Tee Platinum                 |-0.5          |1.1               |
|GGOEGCNB021099|Seat Pack Organizer                                |-0.4          |1.3               |
|GGOEGAAX0607  | Women's Convertible Vest-Jacket Sea Foam Green    |-0.5          |1.8               |
|GGOEGAAX0606  | Women's Convertible Vest-Jacket Black             |-0.3          |1.8               |
|GGOEGAAX0334  |Android Men's Long Sleeve Badge Crew Tee Heather   |-0.1          |1.8               |

Women's Convertible Vest-Jacket Sea Foam Green and Women's Convertible Vest-Jacket Black have the highest sentiment magnitude (1.8) with sentiment scores of -0.5 and -0.3 respectively. This suggests strong negative feelings towards these products.

This data provide insight into product quality and customer feedback.
