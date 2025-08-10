# spotify-listening-analytics
Spotify streaming history analysis using Python and Tableau
### Project summary 
**Overview**

Personal Spotify analytics from 2014-2024
Pipeline: Spotify JSON -> Python cleaning (remove podcasts, normalize) -> CSV -> Tableau for KPIs and visuals (Top artists/Albums, Longest Session vs Completed, Listening Over Time, Hour-by-Day heatmap, etc.)

**Data**

- Spotify Extended Streaming History export
- Each file contains a list of plays with fields such as ts, master_metadata_album_artist_name, master_metadata_album_album_name, master_metadata_track_name, ms_played, episode_name, etc. 

--
### Python processing 

**Why podcasts show up** 

In the extended export, music and podcasts are mixed. Podcast rows usually have: 

- episode_name, episode_show_name populated, and
- music metada (master_metadata_*) empty, or
- titles that contain words like "podcast", "episode", "show". 

**What I did**

1.  Load every JSON file in the folder
2. Concatenate all plays in one DataFrame. 
3. Remove podcast rows using two rules: 
- If podcast-specific fields exist (episode_name or episode_show_name) then drop 
- OR if the music fields contain podcast-like words, then drop 
4. Normalize columns 
- Parse timestamp to a proper datetime 
- Convert ms_played to minutes_played and hours_played
- Keep only columns needed in visualization
- Write CSV for Tableau

### Tableau Build

**Connect data** 

1. Data source: connect to spot_his_combined_tab.csv
2. Use ts (Date/Time), artist_name, album_name, track_name, minutes_played, hours_played

**Calculated fields I created**
1. Hours Played:
SUM([minutes_played]) / 60 

2. Minutes Played (for KPI):
SUM([minutes_played])

3. Year over Year growth (for line charts)
( SUM([minutes_played]) - LOOKUP(SUM([minutes_played]), -1) ) / ABS(LOOKUP(SUM([minutes_played]), -1))

4. Positive YoY / Negative Yoy (for color/formatting): IF/THEN wrappers around the YoY calculation. 

5. Weekday (for the heatmap): DATENAME('weekday', [ts])

6. Hour of Day (for the heatmap): DATEPART('hour', [ts])

