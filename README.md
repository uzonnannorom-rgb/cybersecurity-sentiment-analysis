# Reddit Cybersecurity Sentiment Analysis

A big data pipeline that collects posts from cybersecurity subreddits, stores them in MongoDB, and analyses their sentiment in two ways:

- **Batch analysis** of about 12,500 posts using **TextBlob**, **VADER** and **RoBERTa** (CardiffNLP)
- **Live streaming** of new posts with a dashboard that updates in real time using TextBlob and VADER

Built for the Big Data Computing module (7012DATSCI) of my MSc Data Science at Liverpool John Moores University, December 2025.

## Results in brief

- 12,523 posts collected from 13 subreddits; 12,235 remained after cleaning
- Most discussions focus on solving problems, with positive sentiment around learning, tools and community support
- Negative sentiment rises in discussions of active attacks, major vulnerabilities and privacy concerns
- RoBERTa labelled far more posts negative than TextBlob or VADER
- The live stream showed short-term swings in sentiment linked to security events

| Model | Positive | Neutral | Negative |
|---|---|---|---|
| TextBlob | 3,929 | 7,161 | 1,145 |
| VADER | 5,186 | 3,237 | 3,812 |
| RoBERTa | 1,160 | 6,975 | 4,100 |

The full write-up is in [`report/`](report/).

## Project structure

```
notebooks/
  01_batch_collection_and_sentiment.ipynb   collect, clean, score with 3 models, chart results
  02_live_stream_dashboard.ipynb            stream new posts, score them, live 2x2 dashboard
report/
  Social_Media_Sentiment_Analysis_of_Cybersecurity_on_Reddit.pdf
.env.example                                template for your private settings
requirements.txt
```

## Pipeline

**Batch notebook**

1. Collects up to 1,500 recent posts per subreddit through the Reddit API (PRAW)
2. Saves the raw posts to CSV and MongoDB
3. Cleans the text: removes links, lowercases, removes stopwords and very short posts
4. Scores each post with TextBlob, VADER and RoBERTa
5. Compares the three models with bar charts, pie charts and per-subreddit charts

**Live stream notebook**

1. Backfills recent posts, then streams new posts as they are published
2. Cleans and scores each post with TextBlob and VADER
3. Stores each post in MongoDB, skipping duplicates, and saves CSV snapshots
4. Updates a 2x2 dashboard: sentiment counts, sentiment by subreddit, and VADER and TextBlob trends over time

## Setup

1. Install the packages:

   ```bash
   pip install -r requirements.txt
   ```

2. Download the NLTK data once:

   ```python
   import nltk
   nltk.download("punkt")
   nltk.download("stopwords")
   ```

3. Create a Reddit app at https://www.reddit.com/prefs/apps (type "script") to get a client ID and secret.
4. Copy `.env.example`, rename the copy to `.env`, and fill in your Reddit details.
5. Start MongoDB locally, or set `MONGO_URI` in `.env`.
6. Open the notebooks in Jupyter and run them in order.

## Data

The collected Reddit posts are not included in this repository. Run the notebooks to collect a fresh dataset, which is saved to `data/`.

## Notes

- `.env` holds private keys and is excluded by `.gitignore`. Never upload it.
- RoBERTa scoring on about 12,000 posts can take a long time on a CPU.
- The live stream pauses between posts to stay within Reddit's rate limits.
