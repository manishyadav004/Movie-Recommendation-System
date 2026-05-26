# 🎬 Movie Recommendation System

A full-stack movie recommendation web application powered by **NLP (TF-IDF)**, **FastAPI**, and **Streamlit** — with live movie data, posters, and genre-based discovery via the TMDB API.

---

## 📌 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Setup & Installation](#setup--installation)
- [Running the App](#running-the-app)
- [API Endpoints](#api-endpoints)
- [Screenshots](#screenshots)
- [Dataset](#dataset)
- [License](#license)

---

## Overview

This project builds a content-based movie recommender system using **TF-IDF vectorization** on movie metadata (overview, genres, tagline). A **FastAPI** backend serves recommendations and fetches live movie data from [TMDB](https://www.themoviedb.org/), while a **Streamlit** frontend provides an interactive UI with posters, search, and detailed movie pages.

---

## Features

- 🔍 **Keyword Search** — autocomplete suggestions + poster grid from TMDB
- 🎭 **TF-IDF Recommendations** — content-based similarity using NLP on movie tags
- 📽️ **Genre-Based Discovery** — TMDB-powered "more like this" by genre
- 🏠 **Home Feed** — trending, popular, top-rated, now playing, upcoming
- 🖼️ **Movie Details Page** — poster, backdrop, overview, release date, genres
- ⚡ **Fast REST API** — built with FastAPI, async-ready, CORS-enabled

---

## Tech Stack

| Layer       | Technology                          |
|-------------|--------------------------------------|
| ML/NLP      | scikit-learn (TF-IDF), NLTK, NumPy  |
| Backend     | FastAPI, Uvicorn, httpx, Pydantic   |
| Frontend    | Streamlit                            |
| Data Source | TMDB API                             |
| Data        | movies_metadata.csv (45k+ movies)   |
| Storage     | Pickle (.pkl) for model artifacts   |

---

## Project Structure

```
Movie-Recommendation-System/
│
├── app.py                          # Streamlit frontend
├── main.py                         # FastAPI backend
│
├── Movie_Recommendation_System_    # Jupyter/Colab notebook
│   using_NLP_TF_IDF___FASTAPI.ipynb
│
├── movies_metadata.csv             # Raw dataset
│
├── df.pkl                          # Processed DataFrame
├── indices.pkl                     # Title-to-index mapping
├── tfidf.pkl                       # Fitted TF-IDF vectorizer
├── tfidf_matrix.pkl                # TF-IDF feature matrix
│
├── .env                            # API keys (not committed)
├── LICENSE
└── README.md
```

---

## How It Works

### 1. Data Preprocessing (Notebook)
- Load `movies_metadata.csv`
- Keep relevant columns: `title`, `overview`, `genres`, `tagline`, `vote_average`, `popularity`
- Parse genres from JSON strings → space-separated text
- Combine `overview + genres + tagline` into a single `tags` column
- Clean text: lowercase → remove punctuation → remove stopwords → lemmatize (NLTK)

### 2. TF-IDF Model
- Fit `TfidfVectorizer(max_features=50000, ngram_range=(1,2))` on the `tags` column
- Serialize vectorizer, matrix, DataFrame, and title index to `.pkl` files

### 3. Recommendation Logic
- For a given movie title, retrieve its row index from `indices`
- Compute **cosine similarity** between that row and all other rows in the TF-IDF matrix
- Return the top-N most similar titles

### 4. FastAPI Backend
- Loads all pickle files at startup
- Wraps TF-IDF logic and TMDB API calls into clean REST endpoints
- Enriches local recommendations with TMDB posters and metadata

### 5. Streamlit Frontend
- Calls FastAPI endpoints to render the UI
- Handles routing via `st.query_params` (home / details views)
- Displays poster grids, dropdowns, and movie detail cards

---

## Setup & Installation

### Prerequisites

- Python 3.9+
- A free [TMDB API key](https://www.themoviedb.org/settings/api)

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/Movie-Recommendation-System.git
cd Movie-Recommendation-System
```

### 2. Install Dependencies

```bash
pip install fastapi uvicorn streamlit scikit-learn nltk pandas numpy httpx python-dotenv requests pydantic
```

### 3. Configure Environment

Create a `.env` file in the root directory:

```env
TMDB_API_KEY=your_tmdb_api_key_here
```

### 4. Generate Pickle Files

If `.pkl` files are not present, run the notebook or script to generate them:

```bash
python movie_recommendation_system_using_nlp_tf_idf___fastapi.py
```

This will produce `df.pkl`, `indices.pkl`, `tfidf.pkl`, and `tfidf_matrix.pkl`.

---

## Running the App

Open **two terminals**:

**Terminal 1 — Start FastAPI backend:**

```bash
uvicorn main:app --host 127.0.0.1 --port 8000 --reload
```

**Terminal 2 — Start Streamlit frontend:**

```bash
streamlit run app.py
```

Then open your browser at **http://localhost:8501**

---

## API Endpoints

| Method | Endpoint               | Description                                    |
|--------|------------------------|------------------------------------------------|
| GET    | `/health`              | Health check                                   |
| GET    | `/home`                | Home feed (trending / popular / top_rated etc.)|
| GET    | `/tmdb/search`         | Keyword search — returns TMDB results          |
| GET    | `/movie/id/{tmdb_id}`  | Full movie details by TMDB ID                  |
| GET    | `/movie/search`        | Bundle: details + TF-IDF recs + genre recs     |
| GET    | `/recommend/tfidf`     | TF-IDF recommendations by title                |
| GET    | `/recommend/genre`     | Genre-based recommendations by TMDB ID         |

### Example Request

```bash
# Get TF-IDF recommendations for "Toy Story"
curl "http://127.0.0.1:8000/recommend/tfidf?title=Toy%20Story&top_n=5"

# Search for a movie
curl "http://127.0.0.1:8000/tmdb/search?query=avengers"
```

Interactive API docs are available at **http://127.0.0.1:8000/docs**

---

## Dataset

This project uses the [TMDB Movies Metadata dataset](https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset) from Kaggle, containing metadata for 45,000+ movies.

Key columns used: `title`, `overview`, `genres`, `tagline`, `vote_average`, `popularity`

---

## License

This project is licensed under the terms of the [LICENSE](LICENSE) file included in this repository.

---

> Built with ❤️ using FastAPI, Streamlit, and scikit-learn
