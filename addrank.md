# SQL Schema
Write a SQL query to rank scores. If there is a tie between two scores, both should have the same ranking. Note that after a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no "holes" between ranks.

| Id | Score |
|----|-------|
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |


For example, given the above Scores table, your query should generate the following report (order by highest score):

| score | Rank    |
|-------|---------|
| 4.00  | 1       |
| 4.00  | 1       |
| 3.85  | 2       |
| 3.65  | 3       |
| 3.65  | 3       |
| 3.50  | 4       |

**Important Note**: For MySQL solutions, to escape reserved words used as column names, you can use an apostrophe before and after the keyword. For example `Rank`.

## Solution 1:
```sql
# Write your MySQL query statement below
SELECT Score, FT2.Rank
FROM Scores AS FT1
JOIN(
SELECT UV.SC AS SCO, ROW_NUMBER() OVER(ORDER BY UV.SC DESC) AS 'Rank'
FROM (SELECT DISTINCT Score AS SC FROM Scores ORDER BY Score DESC) AS UV
) AS FT2
ON FT1.Score = FT2.SCO
ORDER BY Score DESC
```
## Solution 2:
```sql
select c.score, d.ran as 'Rank' from (
select a.score, ROW_NUMBER() OVER (ORDER BY a.score desc) AS ran from
(select distinct score from Scores) as a ) as d
inner join
Scores as c
on c.score = d.score
order by ran asc
```
## Solution 3:
```sql
SELECT Ranks.Score, Ranks.Rank FROM Scores LEFT JOIN 
       ( SELECT r.Score, @curRow := @curRow + 1  Rank 
            FROM (SELECT DISTINCT(Score), (SELECT @curRow := 0) 
                      FROM Scores ORDER by Score DESC) r
       ) Ranks 
       ON Scores.Score = Ranks.Score
       ORDER by Score DESC
```
## Solution 4:
```sql
 SELECT Score,  (SELECT COUNT(DISTINCT(Score)) FROM  Scores b WHERE b.Score > a.Score) + 1 AS 'Rank'
       FROM Scores a
       ORDER by Score DESC
```

[test link](https://leetcode.com/problems/rank-scores/)
