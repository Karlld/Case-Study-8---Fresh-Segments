**Case Study Questions**

The following questions can be considered key business questions that are required to be answered for the Fresh Segments team.

Most questions can be answered using a single query however some questions are more open ended and require additional thought and not just a coded solution!

**Data Exploration and Cleansing**

Update the fresh_segments.interest_metrics table by modifying the month_year column to be a date data type with the start of the month

```sql
ALTER TABLE interest_metrics
ALTER COLUMN month_year TYPE date
USING to_date('01-' || month_year, ‘DD-MM-YYYY');

SELECT * FROM interest_metrics
LIMIT 5;
```

| month | year | month_year | interest_id | composition | index_value | ranking | percentile_ranking |
|-------|------|------------|-------------|-------------|-------------|---------|--------------------|
| 7 | 2018 | 2018-07-01 | 32486 | 11.89 |	6.19 |	1 |	99.86 |
| 7 | 2018 | 2018-07-01 | 6106 | 9.93 |	5.31 |	2 |	99.73 |
| 7 | 2018 | 2018-07-01 | 18923 | 10.85 |	5.29 |	3 |	99.59 |
| 7 | 2018 | 2018-07-01 | 6344 | 10.32 |	5.1 |	4	 | 99.45 |
| 7 | 2018 | 2018-07-01 | 100 | 10.77 |	5.04 |	5	 | 99.31 |

What is count of records in the fresh_segments.interest_metrics for each month_year value sorted in chronological order (earliest to latest) with the null values appearing first?

```sql
SELECT month_year,
       COUNT(*)
	   FROM interest_metrics
	   GROUP BY month_year
	   ORDER BY month_year NULLS FIRST;
```

| month_year | count |
|------------|-------|
| 2018-07-01 |	729 |
 | 2018-08-01 |	767 | 
| 2018-09-01 |	780 |
| 2018-10-01 |	857 |
| 2018-11-01 |	928 |
| 2018-12-01 |	995 |
| 2019-01-01 |	973 |
| 2019-02-01 |	1121 |
| 2019-03-01 |	1136 |
| 2019-04-01 |	1099 |
| 2019-05-01 |	857 |
| 2019-06-01 | 824 |
| 2019-07-01 | 864 |
| 2019-08-01 |	1149 |

What do you think we should do with these null values in the fresh_segments.interest_metrics

If there are null values in the date columns then it is worth excluding these rows because it is not possible to relate the other columns to the rest of the data.

 
How many interest_id values exist in the fresh_segments.interest_metrics table but not in the fresh_segments.interest_map table? What about the other way around?


```sql
SELECT COUNT(distinct(interest_id)) AS missing_count
FROM interest_metrics me
LEFT JOIN interest_map ma ON ma.id = me.interest_id::int
WHERE ma.id ISNULL;
```

| count |
|-------|
| 0 |

```sql
SELECT COUNT(distinct(id)) AS missing_count
FROM interest_map ma
LEFT JOIN interest_metrics me ON ma.id = me.interest_id::int
WHERE me.interest_id ISNULL;
```

| missing_count |
|---------------|
| 7 |

Summarise the id values in the fresh_segments.interest_map by its total record count in this table

```sql
SELECT COUNT(id) AS total_id
   FROM interest_map;
 ```
  
| total_id |
|----------|
| 1209 |


What sort of table join should we perform for our analysis and why? Check your logic by checking the rows where interest_id = 21246 in your joined output and include all columns from fresh_segments.interest_metrics and all columns from fresh_segments.interest_map except from the id column.

```sql
A LEFT JOIN should be used for this analysis, as it can be seen 

SELECT me.*,
       ma.interest_name,
	   ma.interest_summary,
	   ma.created_at,
	   ma.last_modified
   FROM interest_metrics AS me
        LEFT JOIN interest_map AS ma ON ma.id = me.interest_id:: INT 
		WHERE me.interest_id = '21246';
```

| month | year | month_year | interest_id | composition | index_value | ranking | percentile_ranking | interest_name |	interest_summary | created_at | last_modified |
|-------|------|------------|-------------|-------------|-------------|---------|--------------------|---------------|-------------------|------------|---------------|
| 7 	| 2018 | 2018-07-01 | 21246 |	2.26 | 0.65 |	722 |	0.96 |	Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	"2018-06-11 17:50:04 | 
| 8 |	2018 |	2018-08-01 |	21246 |	2.13 |	0.59 |	765 |	0.26 |	Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
| 9 |	2018	| 2018-09-01 |	 21246 | 2.06 |	0.61 |	774 | 	0.77 |	Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	 2018-06-11 17:50:04 |
| 10 |	2018	| 2018-10-01 |	21246 |	1.74 |	0.58 |	855 |	0.23 |	Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
| 11 |	2018 |	 2018-11-01 |	 21246 | 2.25 |	0.78 |	 908 |	2.16	 | Readers of El Salvadoran Content | People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
 | 12 | 2018 |	2018-12-01 |	21246 |	1.97 |	0.7 |	983 |	1.21	 | Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
| 1 |	2019 |	2019-01-01 |	21246 |	2.05 |	0.76 |	954 |	1.95 |	Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources.| 	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
| 2 |	2019 |	2019-02-01 |	21246 |	1.84 |	0.68 |	1109 |	1.07 |	Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
| 3 |	2019  | 2019-03-01 |	21246 |	1.75 |	0.67 |	1123 |	1.14 |	Readers of El Salvadoran Content |	People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
| 4 |	2019 |	2019-04-01 |	21246 |	1.58 |	0.63 |	1092 |	0.64	 | Readers of El Salvadoran Content |  People reading news from El Salvadoran media sources. |	2018-06-11 17:50:04 |	2018-06-11 17:50:04 |
			
