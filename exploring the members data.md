**1. Show only the top 5 rows from the trading.members table**

````sql

SELECT * 
FROM trading.members
LIMIT 5;

````

| member_id | first_name | region        |
| --------- | ---------- | ------------- |
| c4ca42    | Danny      | Australia     |
| c81e72    | Vipul      | United States |
| eccbc8    | Charlie    | United States |
| a87ff6    | Nandita    | United States |
| e4da3b    | Rowan      | United States |

<br/>

**2. Sort all the rows in the table by first_name in alphabetical order and show the top 3 rows**

````sql

SELECT * 
FROM trading.members
ORDER BY first_name ASC
LIMIT 3;

````

| member_id | first_name | region        |
| --------- | ---------- | ------------- |
| c9f0f8    | Abe        | United States |
| 8f14e4    | Alex       | United States |
| 167909    | Ayush      | United States |

<br/>

**3. Which records from trading.members are from the United States region?**

````sql

SELECT * 
FROM trading.members
WHERE region = 'United States';

````

| member_id | first_name | region        |
| --------- | ---------- | ------------- |
| c81e72    | Vipul      | United States |
| eccbc8    | Charlie    | United States |
| a87ff6    | Nandita    | United States |
| e4da3b    | Rowan      | United States |
| 167909    | Ayush      | United States |
| 8f14e4    | Alex       | United States |
| c9f0f8    | Abe        | United States |

<br/>

**4. Select only the member_id and first_name columns for members who are not from Australia**

````sql

SELECT
  member_id,
  first_name
FROM trading.members
WHERE region <> 'Australia';

````

| member_id | first_name |
| --------- | ---------- |
| c81e72    | Vipul      |
| eccbc8    | Charlie    |
| a87ff6    | Nandita    |
| e4da3b    | Rowan      |
| 167909    | Ayush      |
| 8f14e4    | Alex       |
| c9f0f8    | Abe        |
| d3d944    | Enoch      |
| 6512bd    | Vikram     |
| c20ad4    | Leah       |

<br/>

**5. Return the unique region values from the trading.members table and sort the output by reverse alphabetical order**

````sql

SELECT
  DISTINCT region
FROM trading.members
ORDER BY region DESC;    

````

| region        |
| ------------- |
| United States |
| India         |
| Australia     |
| Asia          |
| Africa        |

<br/>

**6. How many mentors are there from Australia or the United States?**

````sql

SELECT
  COUNT(DISTINCT member_id) AS mentor_count
FROM trading.members
WHERE region = 'Australia' OR region = 'United States';

````

| mentor_count |
| ------------ |
| 11           |

<br/>

**7. How many mentors are not from Australia or the United States?**

````sql

SELECT
  COUNT(DISTINCT member_id) AS mentor_count
FROM trading.members
WHERE region NOT IN ('Australia', 'United States')

````

| mentor_count |
| ------------ |
| 3            |

<br/>

**8. How many mentors are there per region? Sort the output by regions with the most mentors to the least**

````sql

SELECT
  region,
  COUNT(DISTINCT member_id) AS mentor_count
FROM trading.members
GROUP BY region
ORDER BY mentor_count DESC;

````

| region        | mentor_count |
| ------------- | ------------ |
| United States | 7            |
| Australia     | 4            |
| Africa        | 1            |
| Asia          | 1            |
| India         | 1            |

<br/>

**9. How many US mentors and non US mentors are there?**

````sql

WITH mentors_cte AS
(

SELECT
  member_id,
  MAX(CASE WHEN region = 'United States' THEN 1 ELSE 0 END) AS us,
  MAX(CASE WHEN region <> 'United States' THEN 1 ELSE 0 END) AS non_us
FROM trading.members
GROUP BY member_id

)

SELECT
  SUM(us) AS us_mentors,
  SUM(non_us) AS "non-us-mentors"
FROM mentors_cte;

````

| us_mentors | non-us-mentors |
| ---------- | -------------- |
| 7          | 7              |

<br/>

**10. How many mentors have a first name starting with a letter before 'E'?**

````sql

SELECT 
  COUNT(DISTINCT member_id) AS mentor_count
FROM trading.members
WHERE SUBSTRING(first_name, 1, 1) < 'E';

````

| mentor_count |
| ------------ |
| 6            |
