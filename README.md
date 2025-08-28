Project Documentation - Movie Streaming Platform Analytics
1. Project Overview
Objective:
Build an analytics platform for a movie streaming service to provide insights on user viewing behavior, trending content, and genre popularity.
Scope:
•	Ingest movie metadata and user watch history from TMDB API or simulated CSV logs.
•	Transform data to identify trends and ratings.
•	Store processed data in PostgreSQL.
•	Visualize insights in Grafana dashboards for business and product teams.
Stakeholders:
•	Product Managers (content strategy, recommendations)
•	Data Analysts & BI Teams
•	Marketing Team (promotions, campaigns)
2. Data Sources
Source	Type	Format	Frequency	Volume
TMDB API	REST API	JSON	Daily	~10K movies/updates
User Watch History	CSV logs (simulated)	CSV	Hourly / Batch	~50K records/day
Notes:
•	TMDB API requires API key authentication.
•	CSV logs contain user_id, movie_id, timestamp, rating, and session_id.
•	Historical watch data can be simulated for testing and validation.
3. Architecture
High-Level Architecture:
TMDB API / CSV Logs → Python ETL → PostgreSQL → Grafana Dashboards
Components:
1.	Data Ingestion:
o	Python script fetches metadata and watch history.
o	Handles incremental updates from API or new CSV logs.
2.	Data Storage:
o	PostgreSQL schema: movies, users, watch_sessions tables.
o	Indexing on movie_id, user_id, and timestamp for performance.
3.	Data Transformation:
o	Identify trending genres (based on recent watch counts).
o	Calculate top-rated movies (average rating, weighted by number of watches).
o	Aggregate viewing patterns by hour of day.
4.	Visualization:
o	Grafana dashboards for top 10 trending movies, genre popularity, and time-based viewing patterns.
4. PostgreSQL Schema
Movies table
CREATE TABLE movies (
    movie_id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    genre VARCHAR(100),
    release_date DATE,
    rating FLOAT
);
Users table
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    signup_date DATE
);
-- Watch sessions table
CREATE TABLE watch_sessions (
    session_id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(user_id),
    movie_id INT REFERENCES movies(movie_id),
    watch_timestamp TIMESTAMP,
    rating FLOAT
);
Indexes:
•	CREATE INDEX idx_watch_timestamp ON watch_sessions(watch_timestamp);
•	CREATE INDEX idx_movie_id ON watch_sessions(movie_id);
5. ETL / Transformation Logic
Step 1: Fetch Data
•	Python script to call TMDB API or read CSV logs.
•	Normalize JSON data to flat tabular format.
Step 2: Clean & Transform
•	Remove duplicates and invalid entries.
•	Standardize genre labels and dates.
•	Aggregate metrics:
o	Trending genres: count watches per genre over the last 7 days.
o	Top-rated titles: weighted average rating = (sum(rating * views) / sum(views)).
o	Viewing patterns: group by hour of day.
Step 3: Load to PostgreSQL
•	Use psycopg2 or SQLAlchemy to batch insert/update tables.
•	Maintain an ETL log table for auditing and monitoring.
6. Analytics & Dashboards
Grafana Dashboards:
1.	Top 10 Trending Movies
o	Metric: Watch counts over last 7 days.
o	Visualization: Bar chart.
2.	Genre Popularity Over Time
o	Metric: Number of views per genre per day/week.
o	Visualization: Line chart or stacked area chart.
3.	Viewing Patterns by Time of Day
o	Metric: Hourly distribution of watch sessions.
o	Visualization: Heatmap or line chart.
7. Data Quality & Monitoring
•	Validate movie metadata completeness and consistency.
•	Check for missing user IDs or watch timestamps.
•	Monitor ETL jobs for failures and performance issues.
8. Security & Compliance
•	Store only non-sensitive user data.
•	Secure database credentials and API keys.
•	Ensure logs do not contain personal identifiers beyond necessary analytics.
9. Future Enhancements
•	Include user demographic data to enhance recommendations.
•	Introduce machine learning models for content recommendations.
•	Real-time streaming analytics using Kafka or Spark Streaming.


