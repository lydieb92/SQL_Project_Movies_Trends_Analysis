# Introduction

Delve into the realm of "movie data" to uncover the trends that make a movie financially viable! 🎬 This project focuses on the box office between 2018 and 2024, exploring worldwide box office earnings, the correlation between movie ratings and box office success, the genres that sell the most, and the correlation between runtime and audience engagement. 📊 Additionally, it analyzes the effects of the pre-pandemic, pandemic, and post-pandemic eras on box office earnings. 🎥

# Background
As a movie-nerd and enthusiast, I aimed to delve into the movie market from the distributor's perspective. 🎬 The data I've gathered hails from official box office sources and is packed with insights on popular movie genres and earning trends that occurred within the six years pre and post-pandemic. 📊 Let's uncover the secrets of the silver screen! 🎥

The questions I wanted to answer through my SQL queries were:

1. What are the movies with the highest worldwide box office from 2018 to now?
2. What are the most popular movie genres since 2018?
3. What is the relationship between movie ratings and box office numbers?
4. What is the correlation between runtime and audience engagement?
5. What is the correlation between sequels and box office numbers?
6. How did the pandemic affect the worldwide box office?

# Tools I used
For my deep dive into the movie data analysis market, I harnessed the power of several key tools:

a. SQL: The backbone of my analysis, allowing me to query the database and unearth critical insights. 

b. PostgreSQL: The chosen database management system, ideal for handling the box office data. 

c. Visual Studio Code: My go-to for database management and executing SQL queries.

d. Git & GitHub: Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis
Each query for this project aimed at investigating specific aspects of the box office market.

This project was created in April 2024, so the box office data for the year 2024 only includes films released up to April 2024. Additionally, the inflation rate has been applied to the years 2018, 2019, 2020, 2021, 2022, and 2023.

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

*Table visualizing the top 10  worldwide grossing films from 2018-2024; ChatGPT generated this table from my SQL query results.*

## The Top Popular Film Genres(2018-2024)

To identify the top film genres (2018-2024), I counted the number of occurrences of each genre among the top 50 grossing films for each year from 2018 to 2024.

``` sql
SELECT genre, COUNT(*) AS genre_count
FROM (
   SELECT COALESCE(genre_1, genre_2, genre_3) AS genre FROM top_50_genres
) AS all_genres
GROUP BY genre
ORDER BY genre_count DESC
LIMIT 10;
```
