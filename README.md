# Imdb_Analysis
IMDb_Analysis is a Python-based data analytics project that downloads and explores IMDb datasets to uncover insights on top movies by genre, rating, OTT platform, picture quality, and box-office collections. Includes interactive Plotly visualizations for engaging movie trend analysis.
🎬 IMDb_Analysis

IMDb_Analysis is a Python-based data analytics project that downloads and explores IMDb datasets to uncover insights about top movies by genre, rating, picture quality, OTT platforms, and cinema box-office collections.
It features interactive Plotly dashboards for engaging, data-driven visual storytelling.


---

📊 Features

🔹 Automatic IMDb dataset download (title & rating data)

🔹 Cleans and merges movie data for analysis

🔹 Handles multiple genres dynamically

🔹 Simulates OTT platform, picture quality, and box-office data

🔹 Generates 4 interactive visualizations using Plotly:

1. Top Movies by Genre & Rating


2. IMDb Ratings by Picture Quality


3. Average Ratings by OTT Platform


4. Cinema Collections vs IMDb Ratings





---

⚙️ Technologies Used

Python 3

Pandas – data handling and analysis

NumPy – numerical operations

Plotly – interactive visualizations

Requests – dataset download



---

🚀 How to Run

🖥️ In Google Colab

1. Open a new notebook.


2. Copy and paste the full script from imdb_analysis.py.


3. Run all cells — interactive charts will open in your browser.



💻 In VS Code / Terminal / Termux

pip install pandas plotly requests numpy
python imdb_analysis.py

> 💡 If using Termux, run termux-setup-storage and copy your script to ~/storage/downloads/ before execution.




---

📂 Output Visualizations

🎬 Top Movies by Genre: Horizontal bar chart of highest-rated films by genre.

🎥 Picture Quality Analysis: Box plot comparing average IMDb ratings by video quality.

📺 OTT Popularity: Pie chart showing average ratings across platforms.

💰 Cinema Revenue: Scatter plot relating IMDb ratings with simulated box-office collections.



---

📈 Insights

Identify which genres consistently receive high IMDb ratings.

Discover which OTT platforms host top-rated content.

Analyze whether picture quality correlates with viewer ratings.

Explore how movie ratings align with box-office success.



---

🧠 Future Enhancements

Real OTT and revenue data integration via APIs.

Add sentiment analysis on IMDb reviews.

Deploy Streamlit dashboard for interactive filtering.



---

🧑‍💻 Author

Dinesh Nain
📍 Data Science & Analytics Enthusiast
🔗 Empowering insights through data visualization.











Perfect 🔥
Let’s walk step by step through how to analyze IMDb movie data and create interactive dashboards for
👉 Top Movies by Genre, Rating, Picture Quality, OTT Platform, and Cinema Sales Collection.


---

⚙️ Step 1: Import libraries

We'll use:

requests to download data

gzip + pandas to read IMDb .tsv.gz files

plotly.express for interactive charts

numpy for numeric work


import requests
import gzip
import pandas as pd
import numpy as np
import plotly.express as px
✅ Summary Table

Library          ||	Purpose	                       ||Why Needed

requests	         Fetch online data	               To download datasets or APIs
gzip	             Handle compressed files	         To read .gz data files
pandas	           Data analysis	                   To structure and analyze tabular data
numpy	             Math & array operations	         For fast numerical computations
plotly.express	   Visualization	                   To create interactive charts



---

📥 Step 2: Download IMDb datasets

IMDb provides multiple datasets — we’ll use:

title.basics.tsv.gz → movie titles, genres, year

title.ratings.tsv.gz → IMDb ratings

(optional mock data) for OTT platform & Cinema sales


# Download IMDb basics and ratings datasets
urls = {
    "basics": "https://datasets.imdbws.com/title.basics.tsv.gz",
    "ratings": "https://datasets.imdbws.com/title.ratings.tsv.gz"
}

for name, url in urls.items():
    response = requests.get(url)
    with open(f"{name}.tsv.gz", "wb") as f:
        f.write(response.content)
    print(f"{name} dataset downloaded successfully!")


---

📂 Step 3: Load and merge datasets

# Load TSV.GZ files directly into pandas
basics = pd.read_csv("basics.tsv.gz", sep='\t', compression='gzip', low_memory=False)
ratings = pd.read_csv("ratings.tsv.gz", sep='\t', compression='gzip', low_memory=False)

# Keep only movies
movies = basics[basics["titleType"] == "movie"]

# Merge with ratings
movies = pd.merge(movies, ratings, on="tconst", how="inner")

# Clean data
movies = movies.dropna(subset=["primaryTitle", "genres", "averageRating"])
movies = movies[movies["averageRating"] > 0]


---

🎭 Step 4: Handle multiple genres properly

# Split multiple genres into rows
movies["genres"] = movies["genres"].str.split(",")
movies = movies.explode("genres")
movies = movies[movies["genres"] != "\\N"]  # remove missing genres


---

🏆 Step 5: Find top movies by genre & rating

# Filter movies with at least 1000 votes to avoid low-sample bias
filtered = movies[movies["numVotes"] >= 1000]

top_movies = (
    filtered.groupby("genres", group_keys=False)
    .apply(lambda x: x.sort_values(by="averageRating", ascending=False).head(10))
    .reset_index(drop=True)
)

print(top_movies[["primaryTitle", "genres", "averageRating", "numVotes"]].head(15))


---

🎨 Step 6: Add mock OTT & Picture Quality data

IMDb doesn’t include OTT or quality fields,
so we’ll simulate them for demo purposes.

np.random.seed(42)
platforms = ["Netflix", "Amazon Prime", "Disney+", "Hulu", "Theatre Only"]
qualities = ["SD", "HD", "Full HD", "4K"]

movies["OTT_Platform"] = np.random.choice(platforms, len(movies))
movies["Picture_Quality"] = np.random.choice(qualities, len(movies))
movies["Cinema_Collection"] = np.random.randint(1, 500, len(movies)) * 1e6  # in $


---

📊 Step 7: Create Interactive Plotly Dashboards

1️⃣ Top Movies by Genre

fig1 = px.bar(
    top_movies,
    x="averageRating",
    y="primaryTitle",
    color="genres",
    orientation="h",
    title="🎬 Top Movies by Genre & Rating (IMDb)",
    hover_data=["numVotes"]
)
fig1.show()


---

2️⃣ Average Rating by Picture Quality

fig2 = px.box(
    movies,
    x="Picture_Quality",
    y="averageRating",
    color="Picture_Quality",
    title="🎥 Average IMDb Rating by Picture Quality"
)
fig2.show()


---

3️⃣ OTT Platform Popularity

ott_avg = movies.groupby("OTT_Platform")["averageRating"].mean().reset_index()

fig3 = px.pie(
    ott_avg,
    names="OTT_Platform",
    values="averageRating",
    title="📺 Average Ratings by OTT Platform"
)
fig3.show()


---

4️⃣ Cinema Sales vs Rating

fig4 = px.scatter(
    movies,
    x="averageRating",
    y="Cinema_Collection",
    color="OTT_Platform",
    size="numVotes",
    hover_name="primaryTitle",
    title="💰 Cinema Sales Collection vs IMDb Rating"
)
fig4.show()


---

🧠 Step 8: Insights You Can Draw

Genres with highest average ratings (e.g., Drama, Biography, Crime).

Which OTTs have higher-rated titles.

Whether 4K movies tend to have higher ratings.

How cinema collections correlate with IMDb ratings.
