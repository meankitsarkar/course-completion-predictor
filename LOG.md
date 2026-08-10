# Daily Work Log

## Day 1 - Setup and Initial Exploration

**Date:** 08 August 2026  
**Time Spent:** ~2 hours

### Tasks Completed
* Set up the project repository on GitHub and opened Google Colab.
* Mounted Google Drive and loaded `cohort_train.csv` and `cohort_holdout.csv` using Pandas `read_csv()`.
* Inspected the dimensions of both datasets using `shape`.
* Viewed the first few rows using `head()` and checked column names using `columns`.
* Checked column data types and missing values using `info()` and `isnull().sum()`.
* Checked for exact duplicate rows across all columns using `duplicated().sum()`.
* Analyzed the target variable `completed_course` using `value_counts()` and `value_counts(normalize=True) * 100`.

### Key Findings
* Training dataset shape: **2,484 rows × 19 columns**.
* Holdout dataset shape: **400 rows × 18 columns**.
* Found **83 exact duplicate rows** in the training dataset.
* Target column `completed_course` distribution:
  * `0.0` (Did not complete): 1,561 learners (**63.1%**)
  * `1.0` (Completed course): 914 learners (**36.9%**)
* Found **9 training rows missing the target value** (`completed_course` is `NaN`).

### Challenges
Since I am a beginner learning Python for the first time, getting used to basic Pandas commands like `shape`, `info()`, and `isnull()` took some time. I had to carefully read what each table output meant.

### What I Learned
How to load CSV files into Google Colab, inspect table structures, and check basic summary stats and missing values.

### Next Steps
Explore each column individually to identify messy text categories, impossible numbers, and potential data leakage fields.

---

## Day 2 - Data Exploration

**Date:** 08 August 2026  
**Time Spent:** ~2 hours

### Tasks Completed
* Inspected unique values and counts for text columns using `value_counts(dropna=False)`.
* Inspected numerical feature ranges using `describe()` to calculate minimum, maximum, mean, and quartile values.
* Checked for duplicate learner IDs using `train_df["learner_id"].duplicated().sum()`.
* Investigated rows where the target column `completed_course` was missing.
* Cross-referenced `final_score` and `certificate_issued` with completion status to check for data leakage.

### Key Findings
* **Inconsistent Categorical Text:**
  * `city_tier` has mixed representations: `1`, `T1`, `Tier 1`, `tier-1`, `2`, `T2`, `Tier 2`, `tier-2`, etc.
  * `education` has mixed representations: `Bachelors`, `Bachelor's`, `bachelors`, `bachelor degree`, `B.Tech`, `High School`, `highschool`, `HS`, `Diploma`, `DIPLOMA`, `Masters`, `M.Sc`, etc.
  * `has_mentor` mixes multiple formats for yes/no: `1`, `TRUE`, `Yes`, `yes`, `Y`, `0`, `FALSE`, `No`, `no`, `N`.
  * `device` mixes terms: `mobile`, `Mobile`, `MOBILE`, `phone`, `laptop`, `Laptop`, `notebook`, `desktop`, `Desktop`, `PC`, `tablet`, `Tablet`, `ipad`.
* **Impossible Numerical Values:**
  * `age`: Min is **-3** and max is **300**.
  * `hours_per_week_pledged`: Max is **326.8 hours** in a week (impossible since a full week only has 168 hours).
  * `video_watch_pct`: Max is **115.3%**.
* **Duplicate IDs:** Found **84 duplicate learner IDs**.
* **Data Leakage Risk:** Discovered that `final_score` and `certificate_issued` contain post-course information that is created *after* the course finishes, so they cannot be used to predict completion at Week 3.

### Challenges
Understanding how to identify data leakage columns and learning why information generated after a course ends cannot be used for early prediction.

### What I Learned
Real-world datasets contain typing inconsistencies, impossible numbers, and future columns that must be handled carefully to avoid model cheating.

### Next Steps
Clean all messy text labels, fix invalid numbers, fill missing values, and prepare a clean dataset copy.

---

## Day 3 - Data Cleaning

**Date:** 09 August 2026  
**Time Spent:** ~2 hours

### Tasks Completed
* Created a fresh working copy of the dataset (`clean_df = train_df.copy()`).
* Removed 83 exact duplicate rows using `drop_duplicates()`.
* Standardized text columns (`city_tier`, `education`, `has_mentor`, `device`) using clean dictionary mappings.
* Cleaned invalid numerical values:
  * Found 29 impossible age values (<10 or >100) and replaced them with `NaN`.
  * Found 8 impossible `hours_per_week_pledged` values (>168) and replaced them with `NaN`.
  * Found 36 `video_watch_pct` values above 100% and capped them back to **100.0%**.
* Imputed missing numerical values (`age`, `hours_per_week_pledged`, `weekly_logins_avg`, `video_watch_pct`) using column medians.
* Filled missing categorical values (`city_tier`, `education`, `has_mentor`) with default labels like `"Bachelor"`, `"Unknown"`, and `"No"`.
* Standardized `certificate_issued` values into clean `yes` and `no` categories.
* Replaced `-` in the `notes` column with `NaN`.
* Dropped the 9 rows missing the target variable `completed_course` using `dropna()`.
* Reset the table index using `reset_index(drop=True)`.

### Important Findings
* Removing duplicate rows brought the row count from 2,484 down to 2,401.
* Replacing invalid numbers created 178 missing age values and 114 missing pledged hour values, which were all successfully filled using medians.
* Dropping the 9 missing target rows resulted in a final cleaned training shape of **2,392 rows × 19 columns** with zero missing values in main behavioral/demographic features.
* Confirmed through `crosstab` analysis that `final_score` and `certificate_issued` strongly correlate with completion, verifying they represent post-course data leakage and must be dropped before model training.

### Challenges
Learning how Pandas handles missing values (`NaN`) and ensuring that only rows missing the target outcome (`completed_course`) were dropped while keeping all valid learner records.

### Final Data Quality Check
* Final dataset shape: **2,392 rows × 19 columns**
* Duplicate rows: **0**
* Missing `completed_course`: **0**
* Impossible age values remaining: **0**
* Impossible pledged-hours values remaining: **0**
* Video watch values above 100% remaining: **0**

### Next Steps
* Drop data leakage columns (`final_score`, `certificate_issued`, `notes`).
* Convert categorical text features into numerical format (One-Hot Encoding).
* Split the clean dataset into Training and Validation sets.
* Train baseline machine learning models.

---

## Day 4 - Model Training and Evaluation

**Date:** 10 August 2026  
**Time Spent:** ~2 hours

### Tasks Completed
* Dropped data leakage columns (`final_score`, `certificate_issued`, `notes`) from the feature set.
* Encoded categorical features into numeric format using One-Hot Encoding, resulting in 33 input features for 2,392 rows.
* Split the dataset into an **80/20 Train-Test split** (1,913 training rows and 479 test rows) using `train_test_split()`.
* Resolved a feature name error by converting all column names to string format (`X_final.columns = X_final.columns.astype(str)`).
* Built and evaluated a baseline model using `DummyClassifier(strategy="most_frequent")`.
* Trained a primary classification model using `LogisticRegression(max_iter=1000)`.
* Evaluated model predictions on the unseen test set using Accuracy, Classification Report, and Confusion Matrix.

### Key Findings & Performance Metrics
* **Baseline Accuracy:** **62.84%** (predicting majority class `0` for all learners).
* **Logistic Regression Test Accuracy:** **77.24%** (an improvement of **+14.40 percentage points** over baseline).
* **Classification Report Breakdown:**
  * **Class 0 (Did Not Complete):** Precision = **80%**, Recall = **86%**, F1-Score = **83%** (Support: 301).
  * **Class 1 (Completed Course):** Precision = **72%**, Recall = **63%**, F1-Score = **67%** (Support: 178).
* **Confusion Matrix (`[[258, 43], [66, 112]]`):**
  * True Negatives (258): Correctly predicted non-completers.
  * False Positives (43): Non-completers incorrectly predicted as completers.
  * False Negatives (66): Actual completers incorrectly predicted as non-completers.
  * True Positives (112): Correctly predicted completers.
  * Total Correct: **370** | Total Incorrect: **109**.

### Errors Encountered & Fixed
* **Column Name TypeError:** Fitting the model threw `TypeError: Feature names are only supported if all input features have string names, but your input has ['int', 'str']`.
  * **Cause:** One-hot encoding created a mixture of integer and string data types in DataFrame column names.
  * **Fix:** Converted all feature names to strings (`X_final.columns = X_final.columns.astype(str)`), allowing scikit-learn to process features consistently.

### Key Insights & Viva Preparation
* **Predictive Patterns vs. Causation:** The 14.4% accuracy jump over baseline proves that learner engagement and demographic features contain strong predictive patterns. However, this shows correlation, not direct causation (we cannot claim a specific feature causes completion).
* **Asymmetric Class Performance:** The model is much stronger at predicting non-completers (86% recall) than completers (63% recall). It misses 37% of actual completers.
* **Why Logistic Regression over Linear Regression?:** The target variable `completed_course` is binary categorical (`0` or `1`). Logistic Regression predicts probabilities for discrete classes, whereas Linear Regression is designed for continuous numeric targets.

### What I Learned & Next Steps
* Learned how to evaluate classification models using baseline comparisons, confusion matrices, and precision/recall trade-offs.
* **Next Steps:** On Day 5, compare Logistic Regression with tree-based models (such as Decision Trees or Random Forests) to test whether Class 1 recall can be improved.
*
