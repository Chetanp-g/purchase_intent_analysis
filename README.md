# Sentiment Analysis - Purchase Intent Analysis

**Predicting player purchase intent from Reddit comments using NLP & VADER**

---

## The Problem

Valorant is Riot Games' free-to-play tactical shooter. The game earns revenue entirely through cosmetic skin bundles priced between $35–$100+. Before a bundle goes live, the community reacts on Reddit — and those reactions are a leading indicator of purchase conversion.

This project scrapes Reddit comments from Valorant skin bundle announcement posts and classifies each comment into one of three purchase intent categories using NLP and VADER sentiment analysis.
This project can also be used for other industry services and products launches by replacing the reddit post_id.

---

## Results

Three Valorant subreddit posts were analysed across two approaches:

### V2 Improved Results (Recommended)

| Bundle | Will Buy | Neutral / Undecided | Will Not Buy |
|--------|----------|---------------------|--------------|
| Bundle 1 (1b4p0vr) | ~38% | ~41% | ~21% |
| Bundle 2 (xibjap) | ~41% | ~41% | ~18% |
| Bundle 3 (i2ykah) | 35.2% | 32.7% | 32.1% |

Bundle 3 showed the most evenly split and highest negative sentiment — a warning signal that the design divided the community compared to Bundles 1 and 2.

---

## Methodology — V1 Baseline vs V2 Improved

This project explicitly documents the iteration from a naive approach to a better one.

### V1 — Baseline Approach
- Extracted VADER's positive and negative word lists manually
- Counted how many lemmatized tokens appeared in each list
- Sentiment score = positive count − negative count
- Classification: `score > 0` → Will Buy, `score < 0` → Will Not Buy, `score == 0` → Wants Changes

**Problems identified:**
- `"not good"` → tokenizes to `["not", "good"]` → "good" counted as +1 positive (negation ignored)
- `"very bad"` scored the same as `"bad"` — intensity modifiers ignored
- `score == 0 exactly` was almost never hit, making "Wants Changes" a near-empty category

### V2 — Improved Approach
Three specific fixes were implemented:

| Fix | V1 | V2 |
|-----|----|----|
| **Text cleaning** | Removes emoticons only | Also strips URLs, Reddit quote markers (`>`), broader emoji ranges |
| **Scoring method** | Manual word count | VADER `polarity_scores()` compound score directly |
| **Thresholds** | Score == 0 for neutral | ±0.05 (VADER's own recommended thresholds) |

---

## Pipeline

```
Reddit API (PRAW)
       ↓
Raw comment text
       ↓
Text cleaning (URLs, quotes, emojis, whitespace)
       ↓
Tokenisation → Stop word removal → Lemmatisation
       ↓
VADER compound score on cleaned text
       ↓
Threshold classification (±0.05)
       ↓
Purchase intent label: Will Buy / Neutral / Will Not Buy
```

---

## Repository Structure

```
valorant-sentiment-analysis/
│
├── Valorant_Reddit_Sentiment_Analysis.ipynb   # Main notebook
├── README.md
├── requirements.txt
└── .gitignore
```

---

## Setup

```bash
git clone https://github.com/Chetanp-g/valorant-sentiment-analysis.git
cd valorant-sentiment-analysis
pip install -r requirements.txt
jupyter notebook
```

### Reddit API Credentials

You need free Reddit API credentials to collect live data:

1. Go to [reddit.com/prefs/apps](https://www.reddit.com/prefs/apps)
2. Click **Create App** → choose **script**
3. Copy your `client_id` and `client_secret`
4. Replace `YOUR_CLIENT_ID` and `YOUR_CLIENT_SECRET` in the notebook

> **Important:** Never commit real credentials to GitHub. The notebook uses placeholder values by default.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| PRAW | Reddit API wrapper — scrapes comments |
| NLTK | Tokenisation, stop word removal, lemmatisation |
| VADER | Rule-based sentiment scoring (handles negations and intensifiers) |
| Pandas | Data manipulation |
| Matplotlib / Seaborn | Visualisation |

---

## Business Value

This pipeline can be run immediately after a bundle announcement to give a game studio an early read on expected purchase conversion before the bundle goes live. Generalises to any product launch with a Reddit community — games, tech, consumer goods.

---

## Limitations

- VADER is trained on general English — gaming slang (`W`, `L`, `fire`, `clean`) may be misclassified
- Reddit commenters are not representative of the full player base
- Purchase intent from text ≠ actual purchase behaviour
- Only top-level comments collected (nested replies excluded)

---

## About

**Chetan Prakash** | MSc Financial Technology (Merit) — University of Glasgow

chetanp.g21@gmail.com
