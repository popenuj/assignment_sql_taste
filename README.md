Jake Tingley & John Popenuck

# assignment_sql_taste
A delicious appetizer of SQL-ey goodness

## Queries

### Example

```
SELECT *
  FROM tutorial.us_housing_units
  WHERE month = 1
```
tutorial.us_housing_units

10 results with information on all columns
```
SELECT *
  FROM tutorial.us_housing_units
  LIMIT 10
```
Housing starts in the Midwest
```
SELECT year, month, month_name, midwest
  FROM tutorial.us_housing_units
```
All housing starts in December since 1985
```
SELECT *
  FROM tutorial.us_housing_units
  WHERE month_name = 'December' AND year >= 1985
```
All housing starts in the second half of the year since 1990
```
SELECT *
  FROM tutorial.us_housing_units
  WHERE month > 6 AND year >= 1990
```
All rows where housing starts were above 30,000 in the South region
```
SELECT *
  FROM tutorial.us_housing_units
  WHERE south > 30
```
The sum of housing starts across all regions for each row
```
SELECT year, month, month_name,
       south + west + northeast + midwest AS housing_totals
  FROM tutorial.us_housing_units
```
All rows where the sum of all housing starts is above 70,000 Note: You can't use an alias in a WHERE clause.
```
SELECT year, month, month_name,
     south, west, northeast, midwest
FROM tutorial.us_housing_units
WHERE south + west + northeast + midwest > 70
```
All rows where the sum of all housing starts is between 50-80k
```
SELECT year, month, month_name,
       south, west, northeast, midwest
  FROM tutorial.us_housing_units
  WHERE south + west + northeast + midwest BETWEEN 50 AND 80
```
The average of all housing starts across all regions for each row
```
SELECT year, month, month_name,
       south, west, northeast, midwest,
       (south + west + northeast + midwest)/4 AS housing_ave
  FROM tutorial.us_housing_units
```
All rows where the housing starts in the South are above the sum of the other three regions
```
SELECT year, month, month_name,
       south, west, northeast, midwest
  FROM tutorial.us_housing_units
  WHERE south > (west + northeast + midwest)
```
The percentage of housing starts that occur in each region since 1990 Note: Use an alias to title the new columns appropriately
```
SELECT year, month, month_name,
       south/(south + west + northeast + midwest)*100 AS south_pct,
       west/(south + west + northeast + midwest)*100 AS west_pct,
       northeast/(south + west + northeast + midwest)*100 AS northeast_pct,
       midwest/(south + west + northeast + midwest)*100 AS midwest_pct
  FROM tutorial.us_housing_units
  WHERE year >= 1990
```

tutorial.billboard_top_100_year_end

All rows where Elvis Presley had a song on the top 100 charts
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist = 'Elvis Presley'
```
All rows where the artist's name contained "Tony" (not case sensitive)
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist LIKE '%Tony%'
```
All rows where the song title contained the word "love" in any way
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE song_name ILIKE '%love%'
```
All rows where the artist's name begins with the letter "A"
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist LIKE 'A%'
```
The top 3 songs from each year between 1960-1969
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE year BETWEEN 1960 AND 1969
  AND year_rank < 4
```
All rows where either Elvis Presley, The Rolling Stones, or Van Halen were the artist
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist IN ('Elvis Presley', 'Rolling Stones', 'Van Halen')
```
Which artist has had the most appearances on the top 100 list?
```
SELECT artist,
      COUNT(*) AS count
  FROM tutorial.billboard_top_100_year_end
  GROUP BY artist
  ORDER BY count DESC
```
Madonna and Elvis each with 36

Which artist has had the most #1 hits? How many?
```
SELECT artist,
      COUNT(*) AS count
  FROM tutorial.billboard_top_100_year_end
  WHERE year_rank = 1
  GROUP BY artist
  ORDER BY count DESC
```
Beatles and Elvis Presley each with 2

All rows from 1970 where the songs were ranked 10-20th
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE year_rank BETWEEN 10 AND 20
  AND year >= 1970
```
All rows from the 1990's where Madonna was not ranked 10-100th
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE year BETWEEN 1990 AND 1999
  AND (artist != 'Madonna' OR (artist = 'Madonna' AND year_rank < 10))
```
All rows from 1985 which do not include Madonna or Phil Collins in the group.
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE year = 1985
  AND "group" NOT ILIKE '%phil collins%'
  AND "group" NOT ILIKE '%madonna%'
```
All number 1 songs in the data set.
```
SELECT year, year_rank, song_name
  FROM tutorial.billboard_top_100_year_end
  WHERE year_rank = 1
```
All rows where the artist is not listed
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist IS NULL
```
All of Madonna's top 100 hits ordered by their ranking (1 to 100)
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist = 'Madonna'
  ORDER BY year_rank
```
All of Madonna's top 100 hits ordered by their ranking within each year
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE artist = 'Madonna'
  ORDER BY year, year_rank
```
Every number 1 song since 1990 followed by every number 2 song since 1990 and number 3 song since 1990. (Hint: Multiple ordering)
```
SELECT *
  FROM tutorial.billboard_top_100_year_end
  WHERE year >= 1990 AND year_rank < 4
  ORDER BY year_rank, year
```
What is the highest position ever reached by Phil Collins?
```
SELECT MIN(year_rank) AS highest
  FROM tutorial.billboard_top_100_year_end
  WHERE artist = 'Phil Collins'
```
What is the average position reached by Michael Jackson?
```
SELECT AVG(year_rank) AS average
  FROM tutorial.billboard_top_100_year_end
  WHERE artist = 'Michael Jackson'
```
Madonna's average position when she actually reached the top 10
```
SELECT AVG(year_rank) AS average
  FROM tutorial.billboard_top_100_year_end
  WHERE artist = 'Madonna'
  AND year_rank <= 10
```
List the top 10 artists based on their number of appearances on this list (and what that number is) since 1985
```
SELECT artist,
      COUNT(*) AS count
  FROM tutorial.billboard_top_100_year_end
  WHERE year > 1985
  GROUP BY artist
  ORDER BY count DESC
  LIMIT 10
```
The total count of top 10 hits written by either Elvis, Madonna, the Beatles, or Elton John
```
SELECT COUNT(*) AS hits
   FROM tutorial.billboard_top_100_year_end
   WHERE year_rank <= 10
     AND artist IN ('Elvis Presley',
                    'Madonna',
                    'Beatles',
                    'Elton John')
```
The count of days when Apple traded in a range that was larger than $5
```
SELECT COUNT(*) AS days
   FROM tutorial.aapl_historical_stock_price
   WHERE high - low > 5
```
The highest daily trading range that Apple stock achieved in 2012
```
SELECT MAX(high - low) AS highest
   FROM tutorial.aapl_historical_stock_price
   WHERE year = 2012
```
The average price for all days when Apple's trading volume exceeded 10,000,000 shares.
```
SELECT AVG(high + low / 2)
   FROM tutorial.aapl_historical_stock_price
   WHERE volume > 10000000
```
The number of trading days in each month of the year 2012
```
SELECT month, COUNT(*) AS days
   FROM tutorial.aapl_historical_stock_price
   WHERE year = 2012
   GROUP BY month
```
The maximum price Apple traded at during each year of the data set
```
SELECT year, MAX(high) AS year_high
   FROM tutorial.aapl_historical_stock_price
   GROUP BY year
```
The average price and trading volume on each calendar month across the full data set (this should return only 12 rows, one for each month!)
```
SELECT AVG(volume) AS avg_vol, month, AVG(high + low / 2)
   FROM tutorial.aapl_historical_stock_price
   GROUP BY month
   ORDER BY month
```
The average price for each month and year of data since 2008, ordered by years descending and months ascending.
The average price of days with a trading volume above 25,000,000 shares (just 1 row)
The average price on all months with an average daily trading volume above 10,000,000 shares.
The lowest and highest prices that Apple stock achieved between 2005 and 2010 (inclusive).
The average daily trading range in months where the stock moved more than $25 (open of month to close of month)
All months in the second half of the year where average daily trading volume was below 10,000,000.
A list of all calendar months by average daily trading volume (so only 12 rows), sorted from highest to lowest.
Count how many unique months there are in the data set (should equal 12)
Count how many unique years there are in the data set
Count how many unique prices there are in the data set
Return the percentage of unique "open" prices compared to all open prices in the data set
A listing of all months by their average daily trading volume and a classification that puts this volume into the following categories: "Low" = below 10MM, "Medium" = 10-25 MM, "High" = above 25MM
A listing of average monthly price plus which quarter of the year they are in (e.g. "Q2" or "Q4").
This same listing filtered for only Q4 (use the new column not the months explicitly as part of this filtering).
