**1. How many records are there in the trading.transactions table?**

````sql

SELECT
  COUNT(*) AS "count"
FROM trading.transactions

````

| count |
| ----- |
| 22918 |

<br/>

**2. How many unique transactions are there?**

````sql

SELECT
  COUNT(DISTINCT txn_id) AS "count"
FROM trading.transactions

````

| count |
| ----- |
| 22918 |

<br/>

**3. How many buy and sell transactions are there for Bitcoin?**

````sql

SELECT
  txn_type,
  COUNT(*) AS transaction_count
FROM trading.transactions
WHERE ticker = 'BTC'
GROUP BY txn_type
ORDER BY transaction_count ASC;

````

| txn_type | transaction_count |
| -------- | ----------------- |
| SELL     | 2044              |
| BUY      | 10440             |

<br/>

 **4. For each year, calculate the following buy and sell metrics for Bitcoin:**
 
````sql

SELECT
  DATE_PART('year', txn_date) AS txn_year,
  txn_type,
  COUNT(*) AS transaction_count,
  ROUND(SUM(quantity) :: NUMERIC, 2) AS total_quantity,
  ROUND(AVG(quantity) :: NUMERIC, 2) AS average_quantity
FROM trading.transactions
WHERE ticker = 'BTC'
GROUP BY
  DATE_PART('year', txn_date),
  txn_type
ORDER BY 
  txn_year ASC,
  txn_type ASC;

````

| txn_year | txn_type | transaction_count | total_quantity | average_quantity |
| -------- | -------- | ----------------- | -------------- | ---------------- |
| 2017     | BUY      | 2261              | 12069.58       | 5.34             |
| 2017     | SELL     | 419               | 2160.22        | 5.16             |
| 2018     | BUY      | 2204              | 11156.06       | 5.06             |
| 2018     | SELL     | 433               | 2145.05        | 4.95             |
| 2019     | BUY      | 2192              | 11114.43       | 5.07             |
| 2019     | SELL     | 443               | 2316.24        | 5.23             |
| 2020     | BUY      | 2350              | 11748.76       | 5.00             |
| 2020     | SELL     | 456               | 2301.98        | 5.05             |
| 2021     | BUY      | 1433              | 7161.32        | 5.00             |
| 2021     | SELL     | 293               | 1478.00        | 5.04             |

<br/>

**5. What was the monthly total quantity purchased and sold for Ethereum in 2020?**

````sql

````
