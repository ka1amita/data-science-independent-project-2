# Basic Tasks

## Analyzing the Data:

- [X] Which tracks appeared in the most playlists? how many playlist did they appear in?
- multiple tracks have the same number of apperances (5). LIst of those exported to a [csv file](/task-01.csv)
``` sql
SELECT
	TrackId,
	count(PlaylistId)
FROM
	playlist_track
group BY
	1
HAVING count(PlaylistId)  = 5
ORDER BY 
	2 DESC
;
```
- [ ] Which track generated the most revenue? which album? which genre?
- 
- [ ] Which countries have the highest sales revenue? What percent of total revenue does each country make up?
- [ ] How many customers did each employee support, what is the average revenue for each sale, and what is their total sale?

# Additional Challenges

## Intermediate Challenge

- [ ] Do longer or shorter length albums tend to generate more revenue?[^1]
[^1]: Hint: We can use the WITH clause to create a temporary table that determines the number of tracks in each album, then group by the length of the album to compare the average revenue generated for each.
- [ ] Is the number of times a track appear in any playlist a good indicator of sales?[^2]
[^2]: Hint: We can use the WITH clause to create a temporary table that determines the number of times each track appears in a playlist, then group by the number of times to compare the average revenue generated for each.

# Advanced Challenge

- [ ] How much revenue is generated each year, and what is its percent change 74 from the previous year?[^3]
[^3]: Hint: The InvoiceDate field is formatted as ‘yyyy-mm-dd hh:mm:ss’. Try taking a look at using the strftime() function to help extract just the year. Then, we can use a subquery in the SELECT statement to query the total revenue from the previous year. Remember that strftime() returns the date as a string, so we would need to CAST it to an integer type for this part. Finally, since we cannot refer to a column alias in the SELECT statement, it may be useful to use the WITH clause to query the previous year total in a temporary table, and then calculate the percent change in the final SELECT statement.
