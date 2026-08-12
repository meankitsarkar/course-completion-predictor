# Course Completion Predictor — Machine Learning Project Report

**Name:** Ankit Sarkar  
**Project Duration:** 06 August 2026 – 12 August 2026  
**Final Selected Model:** Logistic Regression  
**Final Test Accuracy:** 77.24%

---

## 1. Executive Summary

This project is a Machine Learning classification project to predict whether an online learner will complete a course or not. The target variable is `completed_course`, where `0.0` represents learners who did not complete the course and `1.0` represents learners who completed the course.

The project used learner demographic and early engagement information to train and compare different classification models. Logistic Regression, Decision Tree, and a depth-limited Decision Tree were compared with a majority-class baseline.

The final Logistic Regression model achieved **77.24% test accuracy**, which was about **14.40 percentage points higher than the test-set baseline accuracy of 62.84%**.

---

## 2. Dataset Profile & Problem Overview

The purpose of this project is to predict course completion using information available during the early stages of the learner's course participation.

### Dataset Summary

* Initial training dataset: **2,484 rows × 19 columns**
* Holdout dataset: **400 rows × 18 columns**
* Final cleaned training dataset: **2,392 rows**
* Final encoded feature set: **33 features**
* Missing target rows: **9**
* Exact duplicate rows removed: **83**

### Target Variable

The target variable is `completed_course`.

* **Class 0.0:** Did not complete — **1,561 learners (63.1%)**
* **Class 1.0:** Completed course — **914 learners (36.9%)**

The test-set majority-class baseline accuracy was **62.84%** and was used as the benchmark for comparing the machine-learning models.

---

## 3. Data Cleaning & Leakage Prevention

The dataset contained duplicate records, inconsistent text values, missing values, and some impossible numerical values. These issues were handled before training the models.

### Cleaning Steps

1. Removed **83 exact duplicate rows**.
2. Checked duplicate learner IDs and handled duplicate records appropriately.
3. Standardized categorical values in columns such as: `city_tier`, `education`, `has_mentor`, `device`.
4. Replaced impossible age values below 10 or above 100 with missing values.
5. Replaced `hours_per_week_pledged` values above 168 hours with missing values.
6. Capped `video_watch_pct` values above 100% at 100%.
7. Filled missing numerical values using the column median.
8. Filled missing categorical values with suitable labels.
9. Removed rows where the target variable `completed_course` was missing.
10. Replaced `-` values in the `notes` column with missing values.

After cleaning, the final training dataset contained **2,392 rows × 19 columns**.

### Data Leakage Prevention

The columns `final_score`, `certificate_issued`, and `notes` were removed before model training because they contained information that could be related to events occurring after the prediction point. Removing these columns helped prevent the model from using future information to make predictions.

---

## 4. Feature Encoding & Data Splitting

Categorical features were converted into numerical features using **One-Hot Encoding**. After encoding, the dataset contained **33 input features**.

The cleaned dataset was divided into:
* **Training set:** 1,913 rows (80%)
* **Test set:** 479 rows (20%)

The split was performed using `train_test_split()`.

A feature-name error was encountered because some encoded column names had mixed data types. This was fixed by converting the feature names to strings using: `X_final.columns = X_final.columns.astype(str)`. After this correction, the model was able to train successfully.

---

## 5. Model Training & Comparison

Several classification models were trained and compared:

* **Majority Baseline:** Test Accuracy = 62.84%
* **Decision Tree (Unconstrained):** Training Accuracy = 100.00% | Test Accuracy = 71.19%
* **Decision Tree (max_depth=5):** Training Accuracy = 77.47% | Test Accuracy = 76.41%
* **Logistic Regression:** Training Accuracy = 77.47% | Test Accuracy = 77.24%

### Observations

The unrestricted Decision Tree achieved 100% training accuracy but only 71.19% test accuracy, showing severe overfitting.

To reduce overfitting, the tree depth was limited to `max_depth=5`. This improved the test accuracy to 76.41% and drastically reduced the gap between training and test accuracy.

Logistic Regression achieved the highest test accuracy of **77.24%**, so it was selected as the final model.

---

## 6. Final Model Evaluation

The final selected model was Logistic Regression evaluated on 479 test samples.

### Final Metrics

* **Test Accuracy:** 77.24%
* **Precision (Class 1):** 72.26%
* **Recall (Class 1):** 62.92%
* **F1-Score (Class 1):** 67.27%

### Detailed Metrics Breakdown

* **Class 0.0 (Non-completers):** Precision = 0.80, Recall = 0.86, F1-Score = 0.83 (Support: 301)
* **Class 1.0 (Completers):** Precision = 0.72, Recall = 0.63, F1-Score = 0.67 (Support: 178)
* **Macro Average:** Precision = 0.76, Recall = 0.74, F1-Score = 0.75
* **Weighted Average:** Precision = 0.77, Recall = 0.77, F1-Score = 0.77

### Confusion Matrix Breakdown

* **258 True Negatives:** Class 0 correctly predicted as class 0.
* **43 False Positives:** Class 0 incorrectly predicted as class 1.
* **66 False Negatives:** Class 1 incorrectly predicted as class 0.
* **112 True Positives:** Class 1 correctly predicted as class 1.

The model made 370 correct predictions out of 479 test samples, giving an overall accuracy of 77.24%.

---

## 7. Error Analysis & Challenges

During the project, a few errors and challenges were encountered.

### Feature Name Error
A Scikit-Learn error occurred because the encoded DataFrame contained feature names with mixed data types. The problem was fixed by converting all feature names to strings: `X_final.columns = X_final.columns.astype(str)`.

### Missing Metric Import Error
While calculating final precision and recall, a `NameError` occurred because the required metric functions had not been imported. The missing functions were imported from `sklearn.metrics`: `from sklearn.metrics import precision_score, recall_score, f1_score`.

### Learning From the Errors
These errors demonstrated the importance of checking data types, importing required functions beforehand, and carefully reading error messages.

---

## 8. Model Persistence

After selecting Logistic Regression, the trained model was saved as a Pickle file using `joblib`: `final_logistic_regression_model.pkl`.

The model was reloaded using `joblib.load()` and re-tested on the test set.

* **Original Model Accuracy:** 0.7724425887265136
* **Reloaded Model Accuracy:** 0.7724425887265136

The exact accuracy match confirmed that the model was successfully saved and restored without parameter alteration.

---

## 9. Model Limitations

1. **Prediction & Support Tool:** The final model should be treated as an early-warning support tool rather than an automated decision-maker.
2. **Class 1 Recall:** The recall for Class 1 is 62.92%, meaning that some actual course completers are predicted as non-completers.
3. **Correlation vs. Causation:** Model results reflect statistical correlations in early learner behavior, not direct cause-and-effect relationships.

---

## 10. Repository File Structure

* `Course_Completion_Predictor.ipynb` — Main Jupyter/Colab notebook containing project code and analysis.
* `final_logistic_regression_model.pkl` — Saved final Logistic Regression model weights.
* `LOG.md` — Daily work log documenting Days 1 through 7.
* `AI_USAGE.md` — Declaration of AI assistance used for debugging and document formatting.
* `README.md` — Final project report and documentation.

## 11. Conclusion & Key Takeaways

1. The project successfully used machine learning to predict course completion from early learner information and engagement data.
2. Data cleaning was performed to remove duplicates, standardize text fields, and correct invalid numerical values.
3. Post-course columns were dropped to reduce the risk of data leakage.
4. Logistic Regression achieved the top test accuracy of 77.24%, beating the majority baseline by 14.40 percentage points.
5. Limiting the Decision Tree depth to `max_depth=5` helped reduce overfitting.
6. The final Logistic Regression model was saved as a pickle file (`.pkl`) and successfully reloaded and tested with the same accuracy.

Overall, this project helped me understand the basic steps of a machine-learning project, including data cleaning, feature preparation, model training, model comparison, and model evaluation.
