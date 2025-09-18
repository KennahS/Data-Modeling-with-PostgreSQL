# Data-Modeling-with-PostgreSQL
My data modeling project assisting a start up music company Sparkify in creating an easier way to gather and assess their data. I was presented with JSON metadata and was tasked to take the log and songs data and create a Data Base with the data and create ETL pipelines to make navigating the data and querying easier. 
# Sparkify ETL Project

## Project Summary

This project builds a PostgreSQL database for Sparkify, a music streaming startup company, to enable analytics on user song play activity. The database uses a star schema which is optimized for querying song plays and includes a fact table ('songplays') and dimension tables ('user', 'song', 'artist', 'time'). The project uses an ETL pipeline that processes JSON data from song metadata files and user activity logs. It then loads them into the database through Python scripts. The schema supports queries such as the top songs by user demographics, artist popularity by location, and/or peak listening times. This can help Sparkify understand user behavior, personalize content recommendations for users, and drive decision making for the business.

This project helps Sparkify rech it's analytical goals by allowing data scientists and analysts to perform queries and generate insights that can aid the company in business strategy, marketing, and user recommendations.

## How to run the Python Script

Open a terminal and type:
python create_tables.py
Then run the ETL pipeline using the following code:
python etl.py
After running, the database sparkifydb will be populated with the data from the JSON files.

## Files in the Repository

sql_queries.py - Contains SQL queries to create, drop and insert into tables.
create_tables.py - Connects to PostgreSQL and creates all tables defined in sql_queries.py.
etl.py - Main ETL script - processes song and log data files and loads them into the database.
data/ - Folder containing song and log JSON datasets.
README.md Project documentation and instructions (this file.)

## Schema and ETL Explanation

### Schema Overview

The database is a star schema optomized for analytical queries:

#### Fact Table

- songplays - records of user song play events
    -songplay_id (Primary Key)
    -start_time (Foreign Key to time)
    -user_id (Foreign Key to users)
    -song_id (Foreign Key to songs)
    -artist_id (Foreign Key to artists)
    -level
    -session_id
    -location
    -user_agent
    
#### Dimension Tables

-users - stores user information.
-songs - stores song information.
-artists - stores artist information.
-time - breakdown of timestamps to hour, day, week, month, year, weekday.

#### Justification

The fact table contains events with foreign keys which links the fact table to the dimension tables. Dimensions allow for fast aggreagtion and detailed analysis on users, songs, artists, and time. A normalized schema reduces data redundancy and maintains the integrity of the data.

### ETL Pipeline Overview

1. Extract - Load the JSON song and log files into pandas DataFrames.
2. Transform - Clean and process the data by converting timestamps, handling missing values, and adjusting variable types as needed to meet the data requirements.
3. Load - Insert data into PostgreSQL tables using INSERT statements with conflict handling.
    - Songs and artists data populates the songs and artists tables.
    - Log data populates the users, time and songplays tables.
    - Timestamps are converted from milliseconds to Python datetime objects for storage in time and songplays tables.
    
## Example Queries

### 1. Top 5 Most Played Songs

In SQL type the following query:

SELECT s.title, COUNT(sp.songplay_id) AS play_count
FROM songplays sp
JOIN songs s ON sp.song_id = s.song_id
GROUP BY s.title
ORDER BY play_count DESC
LIMIT 5;

### 2. Peak Listening Hour

In SQL type the following query:

SELECT hour, COUNT(song_playid) AS play_count
FROM time t
JOIN songplays sp ON t.start_time = sp.start_time
GROUP BY hour
ORDER BY play_count DESC
LIMIT 1;
