# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
The goal of this project was to clean and analyze the ecommerce data. Key objectives were to clean the raw data, create reusable views, answering the business questions like top selling products in each country and city, total revenue generated from each city and country, product category preference, etc. The goal is to provide insight into future marketing strategies.
## Process
1. Load five CSV files into Postgresql database tables. 
2. Get a general sense of the data, what goes where, and what aspects of the data may need rectification.
3. Clean data and combine most important columns into a single table to facilitate analysis. 
4. Answer set questions about the data. 
5. Develop additional questions about the data, and provide answers with appropriate queries.
6. Present general method and results, with commentary.

## Results
USA has the highest level of transaction revenue;
San Francisco leads as the city revenue;
The USA leads Average Units Sold by country;
San Bruno leads Average Units Sold by city;
There are different product category preference in different city and countries;
Each country has a unique top-selling product, with no repetitions across countries.
There isn't a single product that dominates sales across multiple countries or cities.
Organic Search is a major driver of traffic for the site, and Referral traffic is generating the highest revenue.

From these, we could tailor marketing strategies towards different country and city, and inventory management in different time.


## Challenges 
1. No documentation or context
2. Missing or null values
3. Duplicate data in the csv files
4. Scaling amount values by dividing them by 1000000
5. Partial matches in the joins


## Future Goals
1. find some documentation on the dataset to guide its interpretation. For now, a lot of the analysis is guesswork.
2. There are a lot of missing data and Null value. It would be helpful to come up with better strategies for filling in gaps in the data. 
3. come out with more meaningful questions to help tailor the marketing strategies.
