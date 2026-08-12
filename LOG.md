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

## Day 4 - Preparing Data for Machine Learning

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
* **Confusion Matrix:**
  * True Negatives: **258** (Correctly predicted non-completers)
  * False Positives: **43** (Non-completers incorrectly predicted as completers)
  * False Negatives: **66** (Actual completers incorrectly predicted as non-completers)
  * True Positives: **112** (Correctly predicted completers)
  * Total Correct: **370** | Total Incorrect: **109**

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

---

## Day 5 - Model Comparison & Improvement

**Date:** 11 August 2026  
**Time Spent:** ~2 hours

### Tasks Completed
* Established the majority-class baseline at 62.84% accuracy.
* Trained and evaluated an unconstrained Decision Tree classifier.
* Checked training accuracy to diagnose overfitting in the initial Decision Tree model.
* Retrained and evaluated a depth-limited Decision Tree classifier (`max_depth=5`) to reduce model complexity.
* Compared model performance using accuracy, precision, recall, F1-score, and confusion matrices.
* Compared Decision Tree models with the existing Logistic Regression model.

### Model Comparison

| Model | Accuracy | Remarks |
| :--- | :---: | :--- |
| **Majority baseline** | 62.84% | Benchmark |
| **Decision Tree (Unconstrained)** | 71.19% | Severe overfitting |
| **Decision Tree (`max_depth=5`)** | 76.41% | Reduced overfitting |
| **Logistic Regression** | **77.24%** | **Best overall performance** |

### Observations
* All machine-learning models substantially outperformed the majority-class baseline.
* The unconstrained Decision Tree performed weakest among the machine-learning models due to severe overfitting, achieving 100% training accuracy but only 71.19% test accuracy.
* Limiting the tree depth to `max_depth=5` successfully controlled model complexity, raising test accuracy from 71.19% to 76.41% and drastically narrowing the gap between training and test accuracy from 28.81 percentage points down to 1.06 percentage points.
* Logistic Regression remained the best-performing model with a test accuracy of 77.24%, closely followed by the depth-limited Decision Tree at 76.41%.

### Overfitting & Tuning Analysis
* The unconstrained Decision Tree became too complex and memorized noise in the training data, leading to poor generalization on unseen test data.
* Restricting the tree depth to 5 prevented deep splitting, resulting in a balanced model with 77.47% training accuracy and 76.41% test accuracy.
* The classification results for the improved Depth-5 Decision Tree showed:
  * True Negatives: 244
  * False Positives: 57
  * False Negatives: 56
  * True Positives: 122
  * Overall Correct Predictions: 366 out of 479
* Learner features show strong statistical patterns for predicting course completion, but these correlations should not be interpreted as direct evidence of causation.

### Next Steps
* Perform deeper error analysis on the leading models.
* Examine false positives (57) and false negatives (56) to understand prediction failures.
* Check whether model performance remains consistent across different learner segments.
* Explore further feature engineering or hyperparameter tuning only after completing the error analysis.

---

## Day 6 - Final Model Selection & Evaluation

**Date:** 12 August 2026  
**Time Spent:** ~2 hours

### Tasks Completed
* Evaluated all candidate models on the test dataset using comprehensive classification metrics (Accuracy, Precision, Recall, F1-Score, and Confusion Matrix).
* Selected Logistic Regression as the final primary classification model based on overall accuracy and class balance.
* Resolved evaluation metric errors caused by unimported Scikit-Learn functions.
* Serialized and saved the trained Logistic Regression model as a pickle file (`final_logistic_regression_model.pkl`) using `joblib`.
* Reloaded the saved pickle model and verified that its predictions and accuracy match the original trained model.

### Final Model Comparison

| Model | Test Accuracy | Status / Summary |
| :--- | :---: | :--- |
| **Majority baseline** | 62.84% | Benchmark |
| **Decision Tree (Unconstrained)** | 71.19% | Severe overfitting |
| **Decision Tree (`max_depth=5`)** | 76.41% | Controlled overfitting |
| **Logistic Regression** | **77.24%** | **Selected Final Model** |

### Evaluation Metrics Breakdown
The final selected Logistic Regression model achieved the following performance metrics on the 479 test samples:
* **Accuracy:** 77.24% (`0.7724425887265136`)
* **Precision (Class 1 - Completers):** 72.26% (`0.7225806451612903`)
* **Recall (Class 1 - Completers):** 62.92% (`0.6292134831460674`)
* **F1-Score (Class 1 - Completers):** 67.27% (`0.6726726726726727`)

**Confusion Matrix Results:**
* True Negatives (Class 0): 258
* False Positives (Class 0 predicted as 1): 43
* False Negatives (Class 1 predicted as 0): 66
* True Positives (Class 1): 112

### Errors Encountered & Solutions
* **Missing Metric Import Error:** Encountered a `NameError: name 'precision_score' is not defined` while calculating explicit metric scores.
* **Fix:** Imported missing functions directly from Scikit-Learn using `from sklearn.metrics import precision_score, recall_score, f1_score`.

### Model Persistence & Verification
* **Model Saving:** Serialized the trained Logistic Regression object into `final_logistic_regression_model.pkl` via `joblib.dump()`. This eliminates the need to retrain the model when making future predictions or deploying to applications.
* **Restoration Check:** Successfully loaded the `.pkl` file back into memory using `joblib.load()`.
* **Accuracy Verification:** Evaluated the restored model on `X_test`, confirming an exact match with the original score (`0.7724425887265136`), verifying that no weights or parameters were altered during serialization.

### Next Steps
* Perform final notebook cleanup, code refactoring, and markdown structure checks.
* Audit repository documentation (`LOG.md`, `AI_USAGE.md`, `README.md`) before project submission.

---

## Day 7 - Final Course Completion Review

**Date:** 12 August 2026  
**Time Spent:** ~2 hours

### Objective
To complete the final review of the machine-learning assignment and confirm the performance of the finalized model.

### Work Completed
1. Reviewed the final Logistic Regression model.
2. Reviewed the final classification metrics.
3. Confirmed the final precision, recall, and F1-score.
4. Reviewed the overall model comparison and final model selection.
5. Reviewed the major errors encountered during the project and their corrections.
6. Confirmed the complete ML workflow from data preparation to final evaluation.

### Confirmed Final Metrics
* **Final Precision:** **72.26%** (`0.7225806451612903`)
* **Final Recall:** **62.92%** (`0.6292134831460674`)
* **Final F1-Score:** **67.27%** (`0.6726726726726727`)
* **Final Test Accuracy:** **77.24%**

### Conclusion

I completed my machine-learning assignment and selected Logistic Regression as my final model because it gave the best accuracy among the models I tested. I learned how to train different models, compare their results, and check the model using precision, recall, F1-score, and the confusion matrix. I also fixed the errors that came during the work. Overall, this project helped me understand the basic steps of a machine-learning project.
