# Cloud

Sql project code #1

CREATE TABLE applestore_description_combined AS

select * from appleStore_description1

UNION all 

select * FROM appleStore_description2

union all 

select * FROM appleStore_description3

union ALL

select * FROM appleStore_description4






-- check the number of unique apps in both tablesaAppleStore

select COUNT(DISTINCT id) as UniqueAppIDs
from AppleStore

select COUNT(DISTINCT id) as UniqueAppIDs
from applestore_description_combined

-- check for any missing values in key fields 

select count(*) as MissingValues
from AppleStore
where track_name is null or user_rating is null or prime_genre is NULL

select count(*) as MissingValues
from applestore_description_combined
where app_desc is null 

-- find out the number of apps per genre 

select prime_genre, count(*) as NumApps
from AppleStore
group by prime_genre
order by NumApps desc 

-- Get an overview of the apps' ratings 

select  min(user_rating) as MinRating,
        max(user_rating) as MaxRating,
        avg(user_rating) as AvgRating
 from AppleStore    
 
 -- Determine wheather paid apps have a higher rating than free apps
 
 select CASE 
 			when price > 0 then 'Paid'
            else 'Free'
            end as App_type, 
            avg(user_rating) as user_rating
from AppleStore
group by App_Type

-- Check is apps with more supported languages have higher ratings

select case 
          when lang_num < 10 then '<10 languages'
          WHEN lang_num BETWEEN 10 and 30 then '10-30 languages'
          else '>30 languages'
          end as language_bucket,
          avg(user_rating) as Avg_Rating
from AppleStore
group by language_bucket
order by Avg_Rating DESC

-- check genres with low ratings 

select prime_genre,
       avg(user_rating) as Avg_Rating
FROM AppleStore
GROUP by prime_genre
order by Avg_Rating ASC
limit 10

-- check if there is a correlation between the length of the app description and rating 

select case 
           WHEN length(b.app_desc) <500 then 'Short'
           WHEN length(b.app_desc) BETWEEN 500 and 1000 then 'Medium'
           else 'Long'
           end as description_length_bucket,
           avg(a.user_rating) as average_rating
           
FROM
    AppleStore as A
    
join 
    applestore_description_combined as B
    
 ON
 a.id = b.id
    
GROUP BY description_length_bucket
order by average_rating DESC

-- check the top rated apps for each genre

SELECT
    prime_genre,
    track_name,
    user_rating
FROM (
    SELECT
        prime_genre,
        track_name,
        user_rating,
        RANK() OVER (PARTITION BY prime_genre ORDER BY user_rating DESC, rating_count_tot DESC) AS rank
    FROM AppleStore
) AS a
WHERE a.rank = 1;
