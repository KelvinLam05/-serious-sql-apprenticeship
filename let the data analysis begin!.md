**1. What is the earliest and latest date of transactions for all members?**

````sql

SELECT
  TO_CHAR(MIN(txn_date) :: DATE, 'yyyy-mm-dd') AS earliest_date,
  TO_CHAR(MAX(txn_date) :: DATE, 'yyyy-mm-dd') AS latest_date
FROM trading.transactions;

 ````
 
| earliest_date | latest_date |
| ------------- | ----------- |
| 2017-01-01    | 2021-08-27  |

<br/>

**2. What is the range of market_date values available in the prices data?**

````sql

SELECT
  TO_CHAR(MIN(market_date) :: DATE, 'yyyy-mm-dd') AS earliest_date,
  TO_CHAR(MAX(market_date) :: DATE, 'yyyy-mm-dd') AS latest_date
FROM trading.prices;

````

| earliest_date | latest_date |
| ------------- | ----------- |
| 2017-01-01    | 2021-08-29  |

<br/>

**3. Which top 3 mentors have the most Bitcoin quantity as of the 29th of August?**

````sql

SELECT
  first_name,
  SUM(CASE WHEN txn_type = 'BUY' THEN quantity ELSE 0 END) - SUM(CASE WHEN txn_type = 'SELL' THEN quantity ELSE 0 END) AS total_quantity
FROM trading.prices p
JOIN trading.transactions t
  ON p.ticker = t.ticker AND p.market_date = t.txn_date
JOIN trading.members m USING(member_id)
WHERE t.ticker = 'BTC'
GROUP BY 
  member_id,
  first_name
ORDER BY total_quantity DESC
LIMIT 3;

````

| first_name | total_quantity     |
| ---------- | ------------------ |
| Nandita    | 4160.219869506642  |
| Leah       | 4046.0908966725733 |
| Ayush      | 3945.198083260503  |

<br/>

**4. What is total value of all Ethereum portfolios for each region at the end date of our analysis? Order the output by descending portfolio value**

