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

SELECT
  TO_CHAR(DATE_TRUNC('month', txn_date) :: DATE, 'yyyy-mm-dd') AS calendar_month,
  SUM(quantity) FILTER (WHERE txn_type = 'BUY') AS buy_quantity,
  SUM(quantity) FILTER (WHERE txn_type = 'SELL') AS sell_quantity
FROM trading.transactions
WHERE ticker = 'ETH' AND DATE_PART('year', txn_date) = '2020'
GROUP BY TO_CHAR(DATE_TRUNC('month', txn_date) :: DATE, 'yyyy-mm-dd')
ORDER BY calendar_month	ASC;

````

| calendar_month | buy_quantity      | sell_quantity      |
| -------------- | ----------------- | ------------------ |
| 2020-01-01     | 801.0541163041565 | 158.1272716986775  |
| 2020-02-01     | 687.8912804600265 | 160.06533517839912 |
| 2020-03-01     | 804.2368342042604 | 182.1895644691428  |
| 2020-04-01     | 761.87446914631   | 203.16695745059786 |
| 2020-05-01     | 787.4238801914097 | 149.08328330131854 |
| 2020-06-01     | 787.4659503521506 | 208.3362898912813  |
| 2020-07-01     | 890.7807530272569 | 117.01628097387932 |
| 2020-08-01     | 800.6004484214079 | 178.5423079909115  |
| 2020-09-01     | 767.654783160818  | 118.86826373014458 |
| 2020-10-01     | 744.7913667867248 | 174.269279883162   |
| 2020-11-01     | 698.0915637008526 | 163.74629299419385 |
| 2020-12-01     | 752.4121935735661 | 212.77643601396653 |

<br/>

**6. Summarise all buy and sell transactions for each member_id by generating 1 row for each member with the following additional columns:**

* Bitcoin buy quantity
* Bitcoin sell quantity
* Ethereum buy quantity
* Ethereum sell quantity

````sql

SELECT
  member_id,
  SUM(quantity) FILTER (WHERE ticker = 'BTC' AND txn_type = 'BUY') AS btc_buy_qty,
  SUM(quantity) FILTER (WHERE ticker = 'BTC' AND txn_type = 'SELL') AS btc_sell_qty,
  SUM(quantity) FILTER (WHERE ticker = 'ETH' AND txn_type = 'BUY') AS eth_buy_qty,
  SUM(quantity) FILTER (WHERE ticker = 'ETH' AND txn_type = 'SELL') AS eth_sell_qty
FROM trading.transactions
GROUP BY member_id;

````

| member_id | btc_buy_qty        | btc_sell_qty       | eth_buy_qty        | eth_sell_qty       |
| --------- | ------------------ | ------------------ | ------------------ | ------------------ |
| c20ad4    | 4975.750641191644  | 929.659744519077   | 2187.1154401373137 | 610.0470610814083  |
| c51ce4    | 2580.4064599247727 | 1028.7200828179675 | 2394.7300314796344 | 1076.700582569547  |
| c4ca42    | 4380.44293157246   | 1075.5626055691553 | 4516.597248410067  | 1011.5619313973583 |
| aab323    | 3491.8873912094978 | 916.3032786678012  | 4373.762101490244  | 811.3775177368207  |
| c9f0f8    | 4572.880084238887  | 852.3638794847988  | 2343.469079013971  | 254.26718075171567 |
| eccbc8    | 2844.6515509972596 | 305.34548935523316 | 2573.7547576415836 | 794.0567116424365  |
| a87ff6    | 5023.705687783489  | 863.4858182768505  | 3822.0371970017663 | 318.1506358514526  |
| e4da3b    | 3567.3882471515076 | 998.3785353595931  | 2053.983325296016  | 581.7120621124521  |
| 45c48c    | 3814.2424689381746 | 198.13102225001106 | 4442.136854227907  | 216.4978255494174  |
| 6512bd    | 4031.6925788360772 | 574.7827987664845  | 2941.2223099752014 | 966.1617396549941  |
| c81e72    | 2600.9308762349483 | 974.0950235235426  | 4852.521571017203  | 729.4207172459708  |
| d3d944    | 4270.857382342532  | 735.872222173432   | 1744.6542567360968 | 1057.4159633448544 |
| 8f14e4    | 2647.0768334782097 | 445.74386254752034 | 3233.476850395781  | 663.7834260899181  |
| 167909    | 4448.238806248943  | 503.0407229884322  | 1119.7353314008792 | 707.0007288859948  |

<br/>

**7. What was the final quantity holding of Bitcoin for each member? Sort the output from the highest BTC holding to lowest**

````sql

SELECT
  member_id,
  SUM(quantity) FILTER (WHERE ticker = 'BTC' AND txn_type = 'BUY') - SUM(quantity) FILTER (WHERE ticker = 'BTC' AND txn_type = 'SELL') AS final_btc_holding
FROM trading.transactions
GROUP BY member_id
ORDER BY final_btc_holding DESC;

````

| member_id | final_btc_holding  |
| --------- | ------------------ |
| a87ff6    | 4160.219869506639  |
| c20ad4    | 4046.090896672567  |
| 167909    | 3945.1980832605104 |
| c9f0f8    | 3720.5162047540884 |
| 45c48c    | 3616.1114466881636 |
| d3d944    | 3534.9851601691003 |
| 6512bd    | 3456.9097800695927 |
| c4ca42    | 3304.8803260033046 |
| aab323    | 2575.5841125416964 |
| e4da3b    | 2569.0097117919145 |
| eccbc8    | 2539.3060616420266 |
| 8f14e4    | 2201.3329709306895 |
| c81e72    | 1626.8358527114056 |
| c51ce4    | 1551.6863771068051 |

<br/>

**8. Which members have sold less than 500 Bitcoin? Sort the output from the most BTC sold to least**

````sql

SELECT
  member_id,
  SUM(quantity) FILTER (WHERE ticker = 'BTC' AND txn_type = 'SELL') AS btc_sold_quantity
FROM trading.transactions
GROUP BY member_id
HAVING SUM(quantity) FILTER (WHERE ticker = 'BTC' AND txn_type = 'SELL') < 500.00
ORDER BY btc_sold_quantity DESC;

````

| member_id | btc_sold_quantity  |
| --------- | ------------------ |
| 8f14e4    | 445.74386254752034 |
| eccbc8    | 305.34548935523316 |
| 45c48c    | 198.13102225001106 |

<br/>

**9. What is the total Bitcoin quantity for each member_id owns after adding all of the BUY and SELL transactions from the transactions table? Sort the output by descending total quantity**


**10. Which member_id has the highest buy to sell ratio by quantity?**

````sql

SELECT
  member_id,
  SUM(quantity) FILTER (WHERE txn_type = 'BUY') / SUM(quantity) FILTER (WHERE txn_type = 'SELL') AS buy_to_sell_ratio
FROM trading.transactions
GROUP BY member_id
ORDER BY buy_to_sell_ratio DESC;

````

| member_id | buy_to_sell_ratio  |
| --------- | ------------------ |
| 45c48c    | 19.912698711113308 |
| a87ff6    | 7.486010484765202  |
| c9f0f8    | 6.249914187095622  |
| 8f14e4    | 5.300053224554441  |
| eccbc8    | 4.928502329467622  |
| c20ad4    | 4.652097435222711  |
| 167909    | 4.6014738825886505 |
| aab323    | 4.552721491764275  |
| 6512bd    | 4.525091406569532  |
| c81e72    | 4.375335236929055  |
| c4ca42    | 4.262821897975362  |
| e4da3b    | 3.557626114250054  |
| d3d944    | 3.354458969649687  |
| c51ce4    | 2.3630130420937574 |

<br/>

**11. For each member_id - which month had the highest total Ethereum quantity sold?**

````sql

WITH ranking_cte AS
(

SELECT
  member_id,
  TO_CHAR(DATE_TRUNC('month', txn_date) :: DATE, 'yyyy-mm-dd') AS calendar_month,
  SUM(quantity) AS total_ethereum_quantity_sold,
  DENSE_RANK() OVER(PARTITION BY member_id ORDER BY SUM(quantity) DESC) AS ranking
FROM trading.transactions
WHERE ticker = 'ETH' AND txn_type = 'SELL' 
GROUP BY 
  member_id,
  TO_CHAR(DATE_TRUNC('month', txn_date) :: DATE, 'yyyy-mm-dd')
  
)

SELECT
  member_id,
  calendar_month,
  total_ethereum_quantity_sold
FROM ranking_cte
WHERE ranking = 1
ORDER BY total_ethereum_quantity_sold DESC;

````

| member_id | calendar_month | total_ethereum_quantity_sold |
| --------- | -------------- | ---------------------------- |
| c51ce4    | 2017-05-01     | 66.09244042953502            |
| d3d944    | 2020-04-01     | 60.41736997398335            |
| 6512bd    | 2018-05-01     | 47.932857149515904           |
| 167909    | 2020-12-01     | 45.92423664055218            |
| c81e72    | 2018-08-01     | 41.26728177476413            |
| aab323    | 2018-09-01     | 41.175076337098375           |
| c4ca42    | 2021-04-01     | 40.11347472402258            |
| c20ad4    | 2017-12-01     | 37.71908498970638            |
| eccbc8    | 2021-03-01     | 36.485934922948275           |
| 8f14e4    | 2017-07-01     | 36.17383743681231            |
| e4da3b    | 2019-01-01     | 30.484426410776322           |
| a87ff6    | 2020-07-01     | 27.28919836842423            |
| 45c48c    | 2020-01-01     | 20.2152340642537             |
| c9f0f8    | 2020-11-01     | 15.931855129247868           |
