# Introduction

Delve into the realm of "movie data" to uncover the trends that make a movie financially viable! 🎬 This project focuses on the box office between 2018 and 2024, exploring worldwide box office earnings, the correlation between movie ratings and box office success, the genres that sell the most, and the correlation between runtime and audience engagement. 📊 Additionally, it analyzes the effects of the pre-pandemic, pandemic, and post-pandemic eras on box office earnings. 🎥

# Background
As a movie-nerd and enthusiast, I aimed to delve into the movie market from the distributor's perspective. 🎬 The data I've gathered hails from official box office sources and is packed with insights on popular movie genres and earning trends that occurred within the six years pre and post-pandemic. 📊 Let's uncover the secrets of the silver screen! 🎥

The questions I wanted to answer through my SQL queries were:

1. What are the movies with the highest worldwide box office from 2018 to now (2024)?
2. What are the most popular movie genres since 2018?
3. What is the relationship between film metascore ratings and box office numbers?
4. What is the correlation between runtime and audience engagement?
5. What is the correlation between sequels and box office numbers?
6. What is the correlation between movie ratings and box office success?
7. How did the pandemic affect the worldwide box office?

# Tools I used
For my deep dive into the movie data analysis market, I harnessed the power of several key tools:

a. SQL: The backbone of my analysis, allowing me to query the database and unearth critical insights. 

b. PostgreSQL: The chosen database management system, ideal for handling the box office data. 

c. Visual Studio Code: My go-to for database management and executing SQL queries.

d. Git & GitHub: Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the box office market.

All worldwide earnings have been adjusted with the inflation rate for each year. 

 ## 1. Top Grossing Films (2018-2024)
 To identify the top movies with the highest worldwide earnings, I filtered the ID, release group (movie title), the year (the release year), and the worldwide earnings, and then ordered them in descending order. "Worldwide inflation" refers to the worldwide earnings adjusted for inflation rates.

``` sql
SELECT
 id,
 release_group,
 year,
 Worldwide
FROM (
   SELECT DISTINCT
       id,
       release_group,
       CONCAT(
           '$',
           RTRIM(TO_CHAR(ROUND(worldwide_inflation, 2), 'FM999,999,999,999.99'), '.')
       ) AS Worldwide,
       worldwide_inflation,
       year
   FROM
       box_office_2018_2024
) AS subquery
ORDER BY
   worldwide_inflation DESC
LIMIT 10;
```
Here's the breakdown of the most successful movies at the box office from 2018 to 2024: 

(Please note that as of April 2024, the year 2024 is not yet concluded so the numbers provided may vary by the end of 2024.)


## Top Grossing Films(2018-2024)

<img src="https://github.com/lydieb92/SQL_Project_Movies_Trends_Analysis/blob/main/Assets/Top%20grossing%20movies%20(208-2024).png">
Above is a table visualizing the top 10 grossing films from 2018 to 2024. This table was generated by ChatGPT from my SQL query results

# The Most Popular Film Genres (2018-2024)
To identify the top film genres (2018-2024), I counted the number of occurrences of each genre among the top 50 grossing films spanning the years 2018 to 2024.
```SQL
SELECT genre, COUNT(*) AS genre_count
FROM (
   SELECT COALESCE(genre_1, genre_2, genre_3) AS genre FROM top_50_genres
) AS all_genres
GROUP BY genre
ORDER BY genre_count DESC
LIMIT 10;
```
## Top 10 Most Popular Film Genres (2018-2024)
<img src="https://github.com/lydieb92/SQL_Project_Movies_Trends_Analysis/blob/main/Assets/Top%2010%20Film%20Genres%20pie%20chart.png">

- Action leads by contributing to 37% of successful movies in the box office from 2018-2024.
- Animation follows closely, accounting for 17% of the pie chart.
- Drama emerges as the third favorite genre, capturing 15% of the top worldwide gross film earnings over the past six years.

# Examining the Relationship Between Film Metascore Ratings and Box Office Performance

I analyzed the relationship between box office success and film metascore ratings among the top ten grossing films (2018-2024) by performing an inner join on two tables to retrieve the ratings of these films.

Metascore: A film's rating based on reviews from a wide array of respected critics. Weighted averages summarize their collective opinions.e.

``` SQL
SELECT
  top_50_2018_2024.id,
  top_50_2018_2024.release_group,
  top_50_2018_2024.year,
  CONCAT('$', TRIM(TRAILING '.' FROM TO_CHAR(top_50_2018_2024.worldwide_inflation, 'FM999,999,999,999.99'))) AS worldwide_inflation,
  top_50_genres.metascore
FROM
  top_50_2018_2024
INNER JOIN
  top_50_genres ON top_50_2018_2024.id = top_50_genres.id
ORDER BY
  top_50_2018_2024.worldwide_inflation DESC
LIMIT 10;
```
## Top 10 Grossing Films Metascore Ratings
<img src="Assets/Top 10 Grossing Film Metascore Ratings.png">

- Of the top 10 grossing films from 2018 to 2024, 5 have a Metascore rating above 70, while the remaining 5 have a Metascore rating below 70
- This initial data suggests that there is no clear correlation between film ratings and box office success. 


However, for verification purposes, I executed an additional SQL query to analyze films with high ratings. Employing the same inner join query, I sorted the results by rating rather than worldwide gross earnings, thereby identifying the films with the highest Metascore ratings.

``` SQL
SELECT
 top_50_2018_2024.id,
 top_50_2018_2024.release_group,
 top_50_2018_2024.year,
 CONCAT('$', TRIM(TRAILING '.' FROM TO_CHAR(top_50_2018_2024.worldwide_inflation, 'FM999,999,999,999.99'))) AS worldwide_inflation,
 top_50_genres.metascore
FROM
 top_50_2018_2024
INNER JOIN
 top_50_genres ON top_50_2018_2024.id = top_50_genres.id
WHERE
 top_50_genres.metascore IS NOT NULL
ORDER BY
 top_50_genres.metascore DESC,
 top_50_2018_2024.worldwide_inflation DESC
LIMIT 10;
```
## Top 10 Highly Rated Films
<img src="https://github.com/lydieb92/SQL_Project_Movies_Trends_Analysis/blob/main/Assets/Top%2010%20Highly%20Rated%20Films.png">

- Only Black Panther has earned a worldwide gross of more than $1 billion. Despite their high metascore ratings, the rest of the films do not achieve the same level of financial success as the top 10 grossing films. In fact, they fail to reach the $1 billion mark in comparison.
- We can conclude that there is no correlation between a film's rating and its box office success, whether high or low.

# Film Runtime and Audience Engagement
I examined the relationship between film runtime and audience engagement among the top ten grossing films from 2018 to 2024. This analysis involved performing an inner join on two tables to retrieve the film runtimes and worldwide gross earnings.
``` SQL
SELECT
 top_50_2018_2024.id,
 top_50_2018_2024.release_group,
 top_50_2018_2024.year,
 CONCAT('$', TRIM(TRAILING '.' FROM TO_CHAR(top_50_2018_2024.worldwide_inflation, 'FM999,999,999,999.99'))) AS worldwide,
 top_50_genres.runtime
FROM
 top_50_2018_2024
INNER JOIN
 top_50_genres ON top_50_2018_2024.id = top_50_genres.id
ORDER BY
 top_50_2018_2024.worldwide_inflation DESC
LIMIT 10;
```
## Top 10 Grossing Films and Their Runtimes
<img src="Assets/Top 10 Grossing Films and Their Runtimes.png">

- The average runtime of the top 10 grossing films is over 1 hour and 30 minutes.
- The runtime for these movies ranges from 1 hour and 43 minutes to 3 hours and 12 minutes.

According to sources, the average running time of movies typically falls between 90 minutes and 120 minutes. Films exceeding 120 minutes are generally considered to be longer movies. 

From this perspective, one might assume that longer films are less attractive options. However, when we examine the worldwide top-grossing movies from 2018 to 2024, it becomes evident that lengthy runtimes are not necessarily a deterrent for audiences.

# Examining the relationship between sequels and box office numbers
I examined the relationship between box office success and sequels by running an inner join SQL query to retrieve the sequel values and top 10 grossing films. From there, I analyzed the data to find the correlation between movies that are sequels and box office success.

``` SQL
SELECT
 top_50_2018_2024.id,
 top_50_2018_2024.release_group,
 top_50_2018_2024.year,
 CONCAT('$', TRIM(TRAILING '.' FROM TO_CHAR(top_50_2018_2024.worldwide_inflation, 'FM999,999,999,999.99'))) AS worldwide,
 top_50_genres.sequel
FROM
 top_50_2018_2024
INNER JOIN
 top_50_genres ON top_50_2018_2024.id = top_50_genres.id
ORDER BY
  top_50_2018_2024.worldwide_inflation DESC
LIMIT 10;
```
# Top 10 Grossing Films and Their Sequel Status
<img src="Assets/Top 10 Grossing Films and Their Sequel Status.png">

100% of the top 10 grossing films (2018-2024) are sequels, indicating a strong correlation between box office success and sequel movies.

# Correlation Between Movie Ratings and Box Office Success
I conducted an analysis on the correlation between box office performance and movie ratings. This involved querying the movie ratings and top 10 grossing films using an inner join SQL operation. Subsequently, I examined the data to determine the relationship between movie ratings and box office success.

``` SQL
SELECT
 top_50_2018_2024.id,
 top_50_2018_2024.release_group,
 top_50_2018_2024.year,
 CONCAT('$', TRIM(TRAILING '.' FROM TO_CHAR(top_50_2018_2024.worldwide_inflation, 'FM999,999,999,999.99'))) AS worldwide,
 top_50_genres.rated
FROM
 top_50_2018_2024
INNER JOIN
 top_50_genres ON top_50_2018_2024.id = top_50_genres.id
ORDER BY
  top_50_2018_2024.worldwide_inflation DESC
LIMIT 10;
```
## Top 10 Grossing Films and Their Ratings

* PG: Parental guidance suggested
* G: General audiences
<img src="Assets/Top 10 Grossing Films and Their Ratings.png">

- All top 10 grossing films are suitable for general audiences. Parental guidance indicates that a movie may contain some scenes unsuitable for young children, but these movies are generally considered family-friendly.
- From this dataset, we can conclude that there is a strong correlation between movie ratings and box office success. By delving deeper into the analysis, we can extract insights into what this correlation reveals about consumer preferences. I will further discuss this aspect in the conclusions. 

# Global Box Office: Pre, During & Post Pandemic (2018-2024)

To comprehend the pandemic's influence on box office figures, I scrutinized the global gross earnings spanning from 2018 to 2024. 

This process entailed filtering worldwide box office earnings by release year and executing a SUM aggregation to ascertain the total worldwide earnings for each year within the specified timeframe.

I aimed to investigate the worldwide earnings from 2018 to 2024 to determine if the pandemic had an impact on earnings over time.

``` SQL
SELECT
  year::integer AS release_year,
  CONCAT(
     '$',
     RTRIM(TO_CHAR(SUM(worldwide_inflation), 'FM999,999,999,999.99'), '.')
 ) AS total_worldwide
FROM
  box_office_2018_2024
WHERE
  year BETWEEN 2018 AND 2024
GROUP BY
  year::integer

ORDER BY
  SUM(worldwide_inflation) DESC;
```
## Worldwide Box Office Earnings (2018-2024)
