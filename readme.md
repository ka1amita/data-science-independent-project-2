# Basic Tasks

## Analyzing the Data:

- [X] Which tracks appeared in the most playlists? how many playlist did they appear in?

multiple tracks have the same number of apperances (5). LIst of those exported to a [csv file](/task-01.csv)
``` sql
SELECT TrackId, COUNT(PlaylistId)
FROM playlist_track
GROUP BY 1
HAVING COUNT(PlaylistId) = 5
ORDER BY 2 DESC
;
```
- [X] Which track generated the most revenue?
```sql
SELECT name, UnitPrice
FROM tracks
WHERE UnitPrice = (SELECT max(UnitPrice) FROM tracks)
;	
```
- ...which album?
```sql
WITH previous_result AS (
	SELECT AlbumId, sum(UnitPrice) AS 'price'
	FROM tracks
	GROUP BY AlbumId
	ORDER BY 2 DESC
	LIMIT 1
	)
SELECT previous_result.price , albums.AlbumId,  albums.Title
FROM previous_result
JOIN albums
ON previous_result.AlbumId = albums.AlbumId
;
```
- ...which genre?
```sql
WITH result AS (
	SELECT GenreId, sum(UnitPrice) AS 'total'
	FROM tracks
	GROUP BY GenreId
	ORDER BY 2 DESC
	LIMIT 1
	)
SELECT result.GenreId, genres.name, result.total
FROM result
JOIN genres
ON result.GenreId = genres.GenreId
;
```
- [X] Which countries have the highest sales revenue? What percent of total revenue does each country make up?
```sql
SELECT BillingCountry, sum(total) as 'summ', 100*sum(total)/(SELECT sum(total) from invoices)
FROM invoices
GROUP BY BillingCountry
Order BY summ DESC
LIMIT 10
;
```
- [X] How many customers did each employee support, 
```sql
WITH result AS (
SELECT SupportRepId, sum(CustomerId) AS 'summ'
FROM customers
GROUP BY SupportRepId
ORDER BY 2 DESC
)
SELECT employees.LastName, result.summ
FROM result
JOIN employees
ON result.SupportRepId = employees.EmployeeId
;
```
what is the average revenue for each sale, 
```sql
SELECT round(avg(total))
FROM invoices
;
```
and what is their total sale?

|rank|employee's last name|total sale|
|---|---|---|
|1|Peacock|833|
|2|Park|775|
|3|Johnson|720|

```sql
WITH result AS (select *
FROM invoices
JOIN customers
ON invoices.CustomerId = customers.CustomerId
)

SELECT employees.LastName, round(total(result.total),0)
FROM result
JOIN employees
ON result.SupportRepId = employees.EmployeeId
GROUP BY employees.EmployeeId
;
```
# Additional Challenges

## Intermediate Challenge

- [X] Do longer or shorter length albums tend to generate more revenue?[^1]

Longer.

[^1]: Hint: We can use the WITH clause to create a temporary table that determines the number of tracks in each album, then group by the length of the album to compare the average revenue generated for each.
- [X] Is the number of times a track appear in any playlist a good indicator of sales?[^2]

Not at all. Average revenue per track are independent on the number of apperances of these tracks in playlist.

```sql
WITH number_of_playlists AS (
SELECT TrackId, sum(PlaylistId) AS 'sumOfListApperances'
FROM  playlist_track
GROUP BY TrackId
),

number_of_orders AS (
SELECT TrackId, sum(UnitPrice) AS 'sumOfSells'
FROM invoice_items
GROUP BY TrackId
ORDER BY 2 DESC
)

SELECT number_of_playlists.sumOfListApperances, avg(number_of_orders.sumOfSells)
FROM number_of_playlists
JOIN number_of_orders
ON number_of_playlists.TrackId = number_of_orders.TrackId
GROUP BY 1
ORDER BY 1 DESC
;
```


[^2]: Hint: We can use the `WITH` clause to create a temporary table that determines the number of times each track appears in a playlist, then group by the number of times to compare the average revenue generated for each.

# Advanced Challenge

- [ ] How much revenue is generated each year, and what is its percent change from the previous year?[^3]
[^3]: Hint: The `InvoiceDate` field is formatted as ‘yyyy-mm-dd hh:mm:ss’. Try taking a look at using the strftime() function to help extract just the year. Then, we can use a subquery in the `SELECT` statement to query the total revenue from the previous year. Remember that strftime() returns the date as a string, so we would need to `CAST` it to an integer type for this part. Finally, since we cannot refer to a column alias in the `SELECT` statement, it may be useful to use the `WITH` clause to query the previous year total in a temporary table, and then calculate the percent change in the final `SELECT` statement.
