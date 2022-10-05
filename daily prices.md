**1. How many total records do we have in the trading.prices table?**

````sql

SELECT
  COUNT(*) AS total_records
FROM trading.prices

````

| total_records |
| ------------- |
| 3404          |

<br/>

**2. How many records are there per ticker value?**

````sql

SELECT
  COUNT(*) AS record_count
FROM trading.prices
GROUP BY ticker;

````

| record_count |
| ------------ |
| 1702         |
| 1702         |

<br/>

**3. What is the minimum and maximum market_date values?**

````sql

SELECT
  TO_CHAR(MIN(market_date) :: DATE, 'yyyy-mm-dd') AS min_date,
  TO_CHAR(MAX(market_date) :: DATE, 'yyyy-mm-dd') AS max_date
FROM trading.prices;

````

| min_date   | max_date   |
| ---------- | ---------- |
| 2017-01-01 | 2021-08-29 |

<br/>

**4. Are there differences in the minimum and maximum market_date values for each ticker?**

````sql

SELECT
  ticker,
  TO_CHAR(MIN(market_date) :: DATE, 'yyyy-mm-dd') AS min_date,
  TO_CHAR(MAX(market_date) :: DATE, 'yyyy-mm-dd') AS max_date
FROM trading.prices
GROUP BY ticker;

````

| ticker | min_date   | max_date   |
| ------ | ---------- | ---------- |
| BTC    | 2017-01-01 | 2021-08-29 |
| ETH    | 2017-01-01 | 2021-08-29 |

<br/>

**5. What is the average of the price column for Bitcoin records during the year 2020?**

````sql

SELECT
  AVG(price) AS "avg"
FROM trading.prices
WHERE ticker = 'BTC' AND DATE_PART('year', market_date) = '2020';

````

| avg                |
| ------------------ |
| 11111.631147540977 |

<br/>

**6. What is the monthly average of the price column for Ethereum in 2020? Sort the output in chronological order and also round the average price value to 2 decimal places**

````sql

SELECT
  DATE_TRUNC('month', market_date) AS month_start,
  ROUND(AVG(price) :: NUMERIC, 2) AS average_eth_price
FROM trading.prices
WHERE ticker = 'ETH' AND DATE_PART('year', market_date) = '2020'
GROUP BY DATE_TRUNC('month', market_date);

````

| month_start              | average_eth_price |
| ------------------------ | ----------------- |
| 2020-01-01T00:00:00.000Z | 156.65            |
| 2020-02-01T00:00:00.000Z | 238.76            |
| 2020-03-01T00:00:00.000Z | 160.18            |
| 2020-04-01T00:00:00.000Z | 171.29            |
| 2020-05-01T00:00:00.000Z | 207.45            |
| 2020-06-01T00:00:00.000Z | 235.92            |
| 2020-07-01T00:00:00.000Z | 259.57            |
| 2020-08-01T00:00:00.000Z | 401.73            |
| 2020-09-01T00:00:00.000Z | 367.77            |
| 2020-10-01T00:00:00.000Z | 375.79            |
| 2020-11-01T00:00:00.000Z | 486.73            |
| 2020-12-01T00:00:00.000Z | 622.35            |

<br/>

**7. Are there any duplicate market_date values for any ticker value in our table?**

````sql

SELECT 
  ticker,
  COUNT(market_date) AS total_count,
  COUNT(DISTINCT market_date) AS unique_count
FROM trading.prices
GROUP BY ticker; 

````

| ticker | total_count | unique_count |
| ------ | ----------- | ------------ |
| BTC    | 1702        | 1702         |
| ETH    | 1702        | 1702         |
