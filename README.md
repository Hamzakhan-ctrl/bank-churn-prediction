# Bank Customer Churn Analysis & Prediction

Predicting which bank customers are likely to leave, using a dataset of 10,000 customers spread across five Indian states (Maharashtra, Delhi, Karnataka, Tamil Nadu, Gujarat).

The idea is simple: banks lose way more money replacing a customer than they would spend keeping one happy. So instead of waiting for someone to close their account, this pipeline tries to flag the risk early using their profile data — credit score, balance, age, activity status, etc.

## What's in here

```
bank-churn-project/
├── data/
│   └── Churn_Modelling.csv          # raw dataset, 10k rows
├── notebooks/
│   ├── 01_sql_exploration.ipynb     # SQLite queries on the raw data
│   ├── 02_eda.ipynb                 # charts + correlation digging
│   ├── 03_preprocessing.ipynb       # cleanup, encoding, scaling, split
│   └── 04_random_forest.ipynb       # model training + evaluation
├── 05_dashboard.ipynb               # pulls it all into one dashboard
├── dashboard/
│   ├── churn_dashboard.png
│   └── Churn_Dashboard_V2.png
└── exports/
    └── churn_processed.csv          # cleaned data, ready for BI tools
```

Run the notebooks in order — each one feeds into the next.

## How it works

1. **SQL exploration** – dump the CSV into an in-memory SQLite db and run a handful of queries to get a first read on churn by state, gender, and product count.
2. **EDA** – matplotlib/seaborn charts: churn distribution, age spread, state-wise and gender-wise churn rates, a correlation heatmap.
3. **Preprocessing** – drop the useless ID columns, one-hot encode Geography and Gender, scale the numeric stuff with StandardScaler, then an 80/20 train-test split.
4. **Model** – a Random Forest classifier (scikit-learn), trained with a fixed random seed so results don't shift between runs.
5. **Dashboard** – everything above gets stitched into a single dark-themed PNG dashboard.

## Stack

- Python 3.8+
- pandas, NumPy
- matplotlib, seaborn
- scikit-learn
- sqlite3 (built into Python, no separate DB server needed)

## Setup

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

Drop `Churn_Modelling.csv` into `/data` and run the notebooks 01 → 04, then `05_dashboard.ipynb`.

## What the data says

- Overall churn rate sits at **16.78%** — roughly 1 in 6 customers walk away.
- **Delhi** has the worst churn at 23.88%, way ahead of Maharashtra at 14.13%. Probably comes down to more competing banks and easier switching in a bigger city.
- **Women churn more than men** — 19.02% vs 15.13%. Worth digging into why.
- Top churn predictors (by feature importance): **Estimated Salary, Credit Score, Age**. Money stuff matters more than demographics or how many products someone holds.
- Model lands at **~83% accuracy**, which clears the 80% bar I set going in. But that number's a bit misleading — see below.

## The honest part

Accuracy looks fine, but the dataset is heavily imbalanced (about 5 retained customers for every 1 churned), and I didn't correct for that. The confusion matrix tells the real story — the model is great at spotting people who'll stay, but it barely catches the people who actually leave (only 13 out of 314 churners in the test set got flagged correctly). High accuracy, weak recall — classic imbalance trap.

Other things I'd fix with more time:
- Try SMOTE or class-weighting to actually deal with the imbalance instead of ignoring it
- Compare against XGBoost/LightGBM and plain logistic regression instead of betting on one model
- Tune the Random Forest properly (it's running on just 10 trees right now — fast, but leaves accuracy on the table)
- Add more features — transaction history, app/web login frequency, complaint logs — real banks have way more signal than this dataset gives
hy* someone might churn, not just predicting that they will.

---

Built as a way to practice the full pipeline end to end — SQL, EDA, preprocessing, modeling, and packaging results into something a non-technical person could actually look at.
