Lab 10 - SQL
================

# Setup

``` r
# install.packages(c("RSQLite", "DBI"))

library(RSQLite)
```

    ## Warning: package 'RSQLite' was built under R version 4.0.3

``` r
library(DBI)
```

    ## Warning: package 'DBI' was built under R version 4.0.3

``` r
# Initialize a temporary in memory database
con <- dbConnect(SQLite(), ":memory:")

# Download tables
actor <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/actor.csv")
rental <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/rental.csv")
customer <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/customer.csv")
payment <- read.csv("https://raw.githubusercontent.com/ivanceras/sakila/master/csv-sakila-db/payment_p2007_01.csv")

# Copy data.frames to database
dbWriteTable(con, "actor", actor)
dbWriteTable(con, "rental", rental)
dbWriteTable(con, "customer", customer)
dbWriteTable(con, "payment", payment)
```

``` r
dbListTables(con)
```

    ## [1] "actor"    "customer" "payment"  "rental"

TIP: Use can use the following QUERY to see the structure of a table

``` sql
PRAGMA table_info(actor)
```

<div class="knitsql-table">

| cid | name         | type    | notnull | dflt\_value | pk |
| :-- | :----------- | :------ | ------: | :---------- | -: |
| 0   | actor\_id    | INTEGER |       0 | NA          |  0 |
| 1   | first\_name  | TEXT    |       0 | NA          |  0 |
| 2   | last\_name   | TEXT    |       0 | NA          |  0 |
| 3   | last\_update | TEXT    |       0 | NA          |  0 |

4 records

</div>

SQL references:

<https://www.w3schools.com/sql/>

# Exercise 1

Retrive the actor ID, first name and last name for all actors using the
`actor` table. Sort by last name and then by first name.

``` sql
SELECT  actor_id, first_name, last_name
FROM  actor
ORDER by  last_name, first_name
```

<div class="knitsql-table">

| actor\_id | first\_name | last\_name |
| --------: | :---------- | :--------- |
|        58 | CHRISTIAN   | AKROYD     |
|       182 | DEBBIE      | AKROYD     |
|        92 | KIRSTEN     | AKROYD     |
|       118 | CUBA        | ALLEN      |
|       145 | KIM         | ALLEN      |
|       194 | MERYL       | ALLEN      |
|        76 | ANGELINA    | ASTAIRE    |
|       112 | RUSSELL     | BACALL     |
|       190 | AUDREY      | BAILEY     |
|        67 | JESSICA     | BAILEY     |

Displaying records 1 - 10

</div>

# Exercise 2

Retrive the actor ID, first name, and last name for actors whose last
name equals ‘WILLIAMS’ or ‘DAVIS’.

``` sql
SELECT actor_id, first_name, last_name
FROM actor
WHERE last_name IN ('WILLIAMS', 'DAVIS')
```

<div class="knitsql-table">

| actor\_id | first\_name | last\_name |
| --------: | :---------- | :--------- |
|         4 | JENNIFER    | DAVIS      |
|        72 | SEAN        | WILLIAMS   |
|       101 | SUSAN       | DAVIS      |
|       110 | SUSAN       | DAVIS      |
|       137 | MORGAN      | WILLIAMS   |
|       172 | GROUCHO     | WILLIAMS   |

6 records

</div>

# Exercise 3

Write a query against the `rental` table that returns the IDs of the
customers who rented a film on July 5, 2005 (use the rental.rental\_date
column, and you can use the date() function to ignore the time
component). Include a single row for each distinct customer ID.

``` sql
SELECT date(rental_date)
FROM rental
```

<div class="knitsql-table">

| date(rental\_date) |
| :----------------- |
| 2005-05-24         |
| 2005-05-24         |
| 2005-05-24         |
| 2005-05-24         |
| 2005-05-24         |
| 2005-05-24         |
| 2005-05-24         |
| 2005-05-25         |
| 2005-05-25         |
| 2005-05-25         |

Displaying records 1 - 10

</div>

``` sql
SELECT DISTINCT customer_id, rental_date
FROM rental
WHERE date(rental_date) = '2005-07-05'
```

``` r
july5th_ids
```

    ##    customer_id         rental_date
    ## 1          565 2005-07-05 22:49:24
    ## 2          242 2005-07-05 22:51:44
    ## 3           37 2005-07-05 22:56:33
    ## 4           60 2005-07-05 22:57:34
    ## 5          594 2005-07-05 22:59:53
    ## 6            8 2005-07-05 23:01:21
    ## 7          490 2005-07-05 23:02:37
    ## 8          476 2005-07-05 23:05:17
    ## 9          322 2005-07-05 23:05:44
    ## 10         298 2005-07-05 23:08:53
    ## 11         382 2005-07-05 23:11:43
    ## 12         138 2005-07-05 23:13:07
    ## 13         520 2005-07-05 23:13:22
    ## 14         536 2005-07-05 23:13:51
    ## 15         114 2005-07-05 23:23:11
    ## 16         111 2005-07-05 23:25:54
    ## 17         296 2005-07-05 23:29:55
    ## 18         586 2005-07-05 23:30:36
    ## 19         349 2005-07-05 23:32:49
    ## 20         397 2005-07-05 23:33:40
    ## 21         369 2005-07-05 23:37:13
    ## 22         421 2005-07-05 23:41:08
    ## 23         142 2005-07-05 23:44:37
    ## 24         169 2005-07-05 23:46:19
    ## 25         348 2005-07-05 23:47:30
    ## 26         553 2005-07-05 23:50:04
    ## 27         295 2005-07-05 23:59:15

# Exercise 4

## Exercise 4.1

Construct a query that retrives all rows from the `payment` table where
the amount is either 1.99, 7.99, 9.99.

``` sql
SELECT *
FROM payment
WHERE amount IN (1.99, 7.99, 9.99)
order by amount
```

<div class="knitsql-table">

| payment\_id | customer\_id | staff\_id | rental\_id | amount | payment\_date              |
| ----------: | -----------: | --------: | ---------: | -----: | :------------------------- |
|       16050 |          269 |         2 |          7 |   1.99 | 2007-01-24 21:40:19.996577 |
|       16056 |          270 |         1 |        193 |   1.99 | 2007-01-26 05:10:14.996577 |
|       16081 |          282 |         2 |         48 |   1.99 | 2007-01-25 04:49:12.996577 |
|       16103 |          294 |         1 |        595 |   1.99 | 2007-01-28 12:28:20.996577 |
|       16133 |          307 |         1 |        614 |   1.99 | 2007-01-28 14:01:54.996577 |
|       16158 |          316 |         1 |       1065 |   1.99 | 2007-01-31 07:23:22.996577 |
|       16206 |          351 |         1 |       1137 |   1.99 | 2007-01-31 17:48:40.996577 |
|       16210 |          354 |         2 |        158 |   1.99 | 2007-01-25 23:55:37.996577 |
|       16302 |          400 |         2 |        516 |   1.99 | 2007-01-28 01:40:13.996577 |
|       16306 |          401 |         2 |        811 |   1.99 | 2007-01-29 17:59:08.996577 |

Displaying records 1 - 10

</div>

## Exercise 4.2

Construct a query that retrives all rows from the `payment` table where
the amount is greater then 5

``` sql
SELECT *
FROM payment
WHERE amount > 5
order by amount
```

<div class="knitsql-table">

| payment\_id | customer\_id | staff\_id | rental\_id | amount | payment\_date              |
| ----------: | -----------: | --------: | ---------: | -----: | :------------------------- |
|       16068 |          274 |         1 |        394 |   5.99 | 2007-01-27 09:54:37.996577 |
|       16094 |          288 |         2 |        565 |   5.99 | 2007-01-28 07:54:57.996577 |
|       16106 |          296 |         1 |        511 |   5.99 | 2007-01-28 01:32:30.996577 |
|       16112 |          299 |         1 |        332 |   5.99 | 2007-01-27 00:55:36.996577 |
|       16118 |          301 |         2 |        227 |   5.99 | 2007-01-26 09:20:12.996577 |
|       16121 |          302 |         2 |         92 |   5.99 | 2007-01-25 14:07:12.996577 |
|       16139 |          311 |         2 |        274 |   5.99 | 2007-01-26 15:17:17.996577 |
|       16152 |          314 |         1 |         80 |   5.99 | 2007-01-25 10:40:33.996577 |
|       16165 |          321 |         1 |        620 |   5.99 | 2007-01-28 14:23:11.996577 |
|       16171 |          323 |         1 |        878 |   5.99 | 2007-01-30 04:17:39.996577 |

Displaying records 1 - 10

</div>

## Exercise 4.2

Construct a query that retrives all rows from the `payment` table where
the amount is greater then 5 and less then 8

``` sql
SELECT *
FROM payment
WHERE amount >5 AND amount < 8
order by amount
```

<div class="knitsql-table">

| payment\_id | customer\_id | staff\_id | rental\_id | amount | payment\_date              |
| ----------: | -----------: | --------: | ---------: | -----: | :------------------------- |
|       16068 |          274 |         1 |        394 |   5.99 | 2007-01-27 09:54:37.996577 |
|       16094 |          288 |         2 |        565 |   5.99 | 2007-01-28 07:54:57.996577 |
|       16106 |          296 |         1 |        511 |   5.99 | 2007-01-28 01:32:30.996577 |
|       16112 |          299 |         1 |        332 |   5.99 | 2007-01-27 00:55:36.996577 |
|       16118 |          301 |         2 |        227 |   5.99 | 2007-01-26 09:20:12.996577 |
|       16121 |          302 |         2 |         92 |   5.99 | 2007-01-25 14:07:12.996577 |
|       16139 |          311 |         2 |        274 |   5.99 | 2007-01-26 15:17:17.996577 |
|       16152 |          314 |         1 |         80 |   5.99 | 2007-01-25 10:40:33.996577 |
|       16165 |          321 |         1 |        620 |   5.99 | 2007-01-28 14:23:11.996577 |
|       16171 |          323 |         1 |        878 |   5.99 | 2007-01-30 04:17:39.996577 |

Displaying records 1 - 10

</div>

``` sql
SELECT *
FROM payment
WHERE amount BETWEEN 5 AND 8
order by amount
```

<div class="knitsql-table">

| payment\_id | customer\_id | staff\_id | rental\_id | amount | payment\_date              |
| ----------: | -----------: | --------: | ---------: | -----: | :------------------------- |
|       16068 |          274 |         1 |        394 |   5.99 | 2007-01-27 09:54:37.996577 |
|       16094 |          288 |         2 |        565 |   5.99 | 2007-01-28 07:54:57.996577 |
|       16106 |          296 |         1 |        511 |   5.99 | 2007-01-28 01:32:30.996577 |
|       16112 |          299 |         1 |        332 |   5.99 | 2007-01-27 00:55:36.996577 |
|       16118 |          301 |         2 |        227 |   5.99 | 2007-01-26 09:20:12.996577 |
|       16121 |          302 |         2 |         92 |   5.99 | 2007-01-25 14:07:12.996577 |
|       16139 |          311 |         2 |        274 |   5.99 | 2007-01-26 15:17:17.996577 |
|       16152 |          314 |         1 |         80 |   5.99 | 2007-01-25 10:40:33.996577 |
|       16165 |          321 |         1 |        620 |   5.99 | 2007-01-28 14:23:11.996577 |
|       16171 |          323 |         1 |        878 |   5.99 | 2007-01-30 04:17:39.996577 |

Displaying records 1 - 10

</div>

# Exercise 5

Retrive all the payment IDs and their amount from the customers whose
last name is ‘DAVIS’.

``` sql
PRAGMA table_info(customer)
```

<div class="knitsql-table">

| cid | name         | type    | notnull | dflt\_value | pk |
| :-- | :----------- | :------ | ------: | :---------- | -: |
| 0   | customer\_id | INTEGER |       0 | NA          |  0 |
| 1   | store\_id    | INTEGER |       0 | NA          |  0 |
| 2   | first\_name  | TEXT    |       0 | NA          |  0 |
| 3   | last\_name   | TEXT    |       0 | NA          |  0 |
| 4   | email        | TEXT    |       0 | NA          |  0 |
| 5   | address\_id  | INTEGER |       0 | NA          |  0 |
| 6   | activebool   | TEXT    |       0 | NA          |  0 |
| 7   | create\_date | TEXT    |       0 | NA          |  0 |
| 8   | last\_update | TEXT    |       0 | NA          |  0 |
| 9   | active       | INTEGER |       0 | NA          |  0 |

Displaying records 1 - 10

</div>

``` sql
PRAGMA table_info(payment)
```

<div class="knitsql-table">

| cid | name          | type    | notnull | dflt\_value | pk |
| :-- | :------------ | :------ | ------: | :---------- | -: |
| 0   | payment\_id   | INTEGER |       0 | NA          |  0 |
| 1   | customer\_id  | INTEGER |       0 | NA          |  0 |
| 2   | staff\_id     | INTEGER |       0 | NA          |  0 |
| 3   | rental\_id    | INTEGER |       0 | NA          |  0 |
| 4   | amount        | REAL    |       0 | NA          |  0 |
| 5   | payment\_date | TEXT    |       0 | NA          |  0 |

6 records

</div>

``` sql
SELECT c.customer_id, c.first_name, c.last_name, p.payment_id
FROM customer AS c
  INNER JOIN payment AS p
ON c.customer_id = p.customer_id 
WHERE c.last_name = 'DAVIS'
```

<div class="knitsql-table">

| customer\_id | first\_name | last\_name | payment\_id |
| -----------: | :---------- | :--------- | ----------: |
|            6 | JENNIFER    | DAVIS      |       16685 |
|            6 | JENNIFER    | DAVIS      |       16686 |
|            6 | JENNIFER    | DAVIS      |       16687 |

3 records

</div>

# Exercise 6

## Exercise 6.1

Use `COUNT(*)` to count the number of rows in `rental`

``` sql
SELECT COUNT(*) AS count
FROM rental
```

<div class="knitsql-table">

| count |
| ----: |
| 16044 |

1 records

</div>

## Exercise 6.2

Use `COUNT(*)` and `GROUP BY` to count the number of rentals for each
`customer_id`

``` sql
SELECT customer_id,
  COUNT(*) AS count
FROM rental
GROUP BY customer_id
```

<div class="knitsql-table">

| customer\_id | count |
| :----------- | ----: |
| 1            |    32 |
| 2            |    27 |
| 3            |    26 |
| 4            |    22 |
| 5            |    38 |
| 6            |    28 |
| 7            |    33 |
| 8            |    24 |
| 9            |    23 |
| 10           |    25 |

Displaying records 1 - 10

</div>

## Exercise 6.3

Repeat the previous query and sort by the count in descending order

``` sql
SELECT customer_id,
  COUNT(*) AS count
FROM rental
GROUP BY customer_id
ORDER BY COUNT(*) DESC
```

<div class="knitsql-table">

| customer\_id | count |
| -----------: | ----: |
|          148 |    46 |
|          526 |    45 |
|          236 |    42 |
|          144 |    42 |
|           75 |    41 |
|          469 |    40 |
|          197 |    40 |
|          468 |    39 |
|          178 |    39 |
|          137 |    39 |

Displaying records 1 - 10

</div>

## Exercise 6.4

Repeat the previous query but use `HAVING` to only keep the groups with
40 or more.

``` sql
SELECT customer_id,
  COUNT(*) AS count
FROM rental
GROUP BY customer_id
HAVING COUNT(*) > 40
```

<div class="knitsql-table">

| customer\_id | count |
| -----------: | ----: |
|           75 |    41 |
|          144 |    42 |
|          148 |    46 |
|          236 |    42 |
|          526 |    45 |

5 records

</div>

# Exercise 7

The following query calculates a number of summary statistics for the
payment table using `MAX`, `MIN`, `AVG` and `SUM` of `amount`

``` sql
SELECT MAX(amount) AS max_amount,
MIN(amount) AS min_amount,
AVG(amount) AS avg_amount,
SUM(amount) AS sum_amount
FROM payment
```

<div class="knitsql-table">

| max\_amount | min\_amount | avg\_amount | sum\_amount |
| ----------: | ----------: | ----------: | ----------: |
|       11.99 |        0.99 |    4.169775 |     4824.43 |

1 records

</div>

## Exercise 7.1

Modify the above query to do those calculations for each `customer_id`

``` sql
SELECT customer_id,
MAX(amount) AS max_amount,
MIN(amount) AS min_amount,
AVG(amount) AS avg_amount,
SUM(amount) AS sum_amount,
COUNT(*) AS count
FROM payment
GROUP BY customer_id
ORDER BY count DESC
```

<div class="knitsql-table">

| customer\_id | max\_amount | min\_amount | avg\_amount | sum\_amount | count |
| -----------: | ----------: | ----------: | ----------: | ----------: | ----: |
|          197 |        3.99 |        0.99 |    2.615000 |       20.92 |     8 |
|          506 |        8.99 |        0.99 |    4.132857 |       28.93 |     7 |
|          109 |        7.99 |        0.99 |    3.990000 |       27.93 |     7 |
|          596 |        6.99 |        0.99 |    3.823333 |       22.94 |     6 |
|          371 |        6.99 |        0.99 |    4.323333 |       25.94 |     6 |
|          274 |        5.99 |        2.99 |    4.156667 |       24.94 |     6 |
|          269 |        6.99 |        0.99 |    3.156667 |       18.94 |     6 |
|          251 |        4.99 |        1.99 |    3.323333 |       19.94 |     6 |
|          245 |        8.99 |        0.99 |    4.823333 |       28.94 |     6 |
|          239 |        7.99 |        2.99 |    5.656667 |       33.94 |     6 |

Displaying records 1 - 10

</div>

## Exercise 7.2

Modify the above query to only keep the `customer_id`s that have more
then 5 payments

``` sql
SELECT customer_id,
MAX(amount) AS max_amount,
MIN(amount) AS min_amount,
AVG(amount) AS avg_amount,
SUM(amount) AS sum_amount,
COUNT(*) AS count
FROM payment
GROUP BY customer_id
HAVING count > 5
```

<div class="knitsql-table">

| customer\_id | max\_amount | min\_amount | avg\_amount | sum\_amount | count |
| -----------: | ----------: | ----------: | ----------: | ----------: | ----: |
|           19 |        9.99 |        0.99 |    4.490000 |       26.94 |     6 |
|           53 |        9.99 |        0.99 |    4.490000 |       26.94 |     6 |
|          109 |        7.99 |        0.99 |    3.990000 |       27.93 |     7 |
|          161 |        5.99 |        0.99 |    2.990000 |       17.94 |     6 |
|          197 |        3.99 |        0.99 |    2.615000 |       20.92 |     8 |
|          207 |        6.99 |        0.99 |    2.990000 |       17.94 |     6 |
|          239 |        7.99 |        2.99 |    5.656667 |       33.94 |     6 |
|          245 |        8.99 |        0.99 |    4.823333 |       28.94 |     6 |
|          251 |        4.99 |        1.99 |    3.323333 |       19.94 |     6 |
|          269 |        6.99 |        0.99 |    3.156667 |       18.94 |     6 |

Displaying records 1 - 10

</div>

# Cleanup

Run the following chunk to disconnect from the connection.

``` r
# clean up
dbDisconnect(con)
```
