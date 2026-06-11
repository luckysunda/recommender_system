# 🎬 Philo Show Recommendation System

A recommendation system for predicting which TV shows users will watch next, based on historical playback data.

**Current Performance:** 21% Recall@10 (LightGBM Ranker)

---

## 📊 Quick Results

| Model | Recall@10 | MRR@10 | Training Time |
|-------|-----------|---------|---------------|
| Popularity Baseline | ~15% | ~0.03 | Seconds |
| ALS (Collaborative Filtering) | ~18-22% | ~0.04 | Minutes |
| **LightGBM Ranker** ✅ | **21%** | **0.04** | ~10 min |
| ALS + LGB Ensemble | 20% | 0.04 | ~15 min |

---

## 🚀 Quick Start

### Installation

```bash
# Clone the repository
cd philo-recommendation-system

# Create virtual environment
python3.9 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Inference Pipeline (Using Pre-Trained Model)

**Jupyter Notebook **

```bash
jupyter notebook notebooks/09_inference_pipeline.ipynb
```
- Just add your data path in the first cell after installing above requirements, the best model is already selected and run all cells. I have assumed the data is same format as it was given in this assignment.


**Output:**
```
Recall@10:    21.00%
MRR@10:       0.0400
Precision@10: 2.10%
```

---

## 📁 Project Structure

```
philo-recommendation-system/
├── data/
│   ├── playback_sessions.parquet       # Raw data (30 days, 100k users)
│   ├── train_features.csv              # Engineered features
│   └── test_ground_truth.csv           # Test set labels
├── models/
│   ├── popularity_recommender.py       # Simple baseline
│   ├── als_recommender.py              # Collaborative filtering
│   ├── lgb_ranker.py                   # LightGBM ranker ✅ BEST
│   ├── als_lgb_ensemble.py             # Hybrid ensemble
│   └── philo_lgb_recommender.pkl       # Trained model
├── utils/
│   ├── train_test_split.py             # Chronological splitting
│   └── evaluation.py                   # Recall@K, MRR@K metrics
├── notebooks/
│   ├── 01_EDA.ipynb                    # Data exploration
│   ├── 02_data_prep.ipynb              # Feature engineering
│   ├── 03_popularity_baseline.ipynb    # Baseline model
│   ├── 04_als_baseline.ipynb           # Collaborative filtering
│   ├── 05_lgb_ranker_baseline.ipynb    # LightGBM (BEST: 21%)
│   ├── 06_als_lgb_ensemble_baseline.ipynb  # Ensemble
│   └── 09_inference_pipeline.ipynb     # New data inference ✅
├── inference_pipeline.py               # Standalone script
├── requirements.txt                    # Dependencies
└── README.md                          # This file
```

---

## 🎯 How It Works

### Architecture

```
New User → [Candidate Retrieval] → [Feature Engineering] → [LightGBM Ranking] → Top 10 Shows
             (Top 500 popular)         (11 features)          (LambdaRank)
```

### Features (11 total)

**Show Features (4):**
- Global popularity (log)
- Total watch time (log)
- Unique viewers (log)
- Avg engagement per viewer

**User Features (4):**
- Total interactions
- Avg interaction score
- Unique shows watched
- Diversity score

**Cross Features (3):**
- Popularity bias resistance
- User-show interaction ratio
- User-show diversity match



Some shortcomings, which should be dealt in future:
- Data grew very big after feature engineering, it needs to be addressed
- Ensemble technique performed worse than standalone model, was not expecting that, need to debug
- ALS model did not perform well at all in this case
- Some data niches are yet not included, for example examining data points where watch_minutes are greater than 1460(total minutes of the day), and some outliers
- Some advanced models are not implemented like two tower, DCN, etc as my compute was limited. So, did not go there
- 21% is a good baseline to start with but it can be increased way further with feature engineering and right model choices, for example:using ALS ALS/LGB(for warm users) + popularity (for cold users), this segmentation was found in EDA