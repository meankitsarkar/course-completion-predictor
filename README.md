# Course Completion Predictor

A Machine Learning classification project that predicts whether an online learner will complete a course using learner demographic and early engagement information.

---

## Repository
[GitHub Repository](https://github.com/meankitsarkar/course-completion-predictor)

---

## Project Objective

The goal of this project is to build a classification model that predicts whether a learner will complete the course.

The target variable is `completed_course`:

- `0.0` = Did not complete
- `1.0` = Completed

---

## Dataset Overview

The project started with a training dataset containing:

- **2,484 rows**
- **19 columns**
- **9 rows with missing target values**
- **83 exact duplicate rows**

After cleaning, the final dataset contained **2,392 rows × 19 columns**.

The target distribution was:

- **1,501 learners (62.75%)** — Did not complete
- **891 learners (37.25%)** — Completed

The majority-class baseline accuracy on the test set was **62.84%**.

---

## Machine Learning Workflow

The project followed these main steps:

1. Loaded and explored the dataset.
2. Checked the dataset structure and data types.
3. Removed duplicate records.
4. Identified and handled missing values.
5. Cleaned invalid numerical values.
6. Standardized categorical values.
7. Removed features that could cause data leakage.
8. Performed One-Hot Encoding on categorical features.
9. Split the cleaned dataset into training and test sets using `train_test_split()`.
10. Compared different classification models.
11. Analysed Decision Tree overfitting.
12. Tuned the Decision Tree using `max_depth=5`.
13. Evaluated the final model using classification metrics.
14. Examined the confusion matrix.
15. Saved the final model using `joblib`.
16. Reloaded the saved model and verified its accuracy.
17. Documented the project and results.

---

## Data Cleaning

Several data-quality issues were identified and handled before model training.

### Duplicate Records

A total of **83 exact duplicate rows** were removed.

Duplicate learner IDs were also checked to ensure that learner records were not unnecessarily repeated.

### Invalid Values

The following checks were performed:

- Age values below 10 or above 100 were treated as invalid.
- `hours_per_week_pledged` values above 168 hours were treated as invalid.
- `video_watch_pct` values above 100% were capped at 100%.
- Missing numerical values were handled using median imputation.
- Missing categorical values were handled during preprocessing.
- Inconsistent categorical labels were standardized.

---

## Data Leakage Prevention

The following columns were removed before model training:

- `final_score`
- `certificate_issued`
- `notes`

These columns could contain information related to events occurring after or around course completion.

Removing them helped prevent the model from using future information that would not realistically be available when making an early prediction.

---

## Feature Encoding & Data Splitting

Categorical variables were converted into numerical variables using **One-Hot Encoding**.

After encoding, the model used **33 input features**.

The cleaned dataset was split into:

- **Training set:** 1,913 rows (80%)
- **Test set:** 479 rows (20%)

The split was performed using `train_test_split()`.

---

## Model Training & Comparison

Several classification approaches were trained and compared.

| Model                        | Training Accuracy | Test Accuracy |
|-------------------------------|-------------------|----------------|
| Majority Baseline             | —                  | 62.84%         |
| Decision Tree                 | 100.00%            | 71.19%         |
| Decision Tree (max_depth=5)   | 77.47%             | 76.41%         |
| Logistic Regression           | 77.47%             | 77.24%         |

### Decision Tree Overfitting

The unrestricted Decision Tree achieved 100% training accuracy but only 71.19% test accuracy.

This large difference indicated that the model was overfitting the training data.

To reduce overfitting, the tree depth was limited using:

```python
max_depth=5
```

The tuned Decision Tree achieved 76.41% test accuracy and had a much smaller difference between training and test performance.

### Final Model Selection

Logistic Regression achieved the highest test accuracy among the tested models at 77.24%.

It also had a very small difference between training accuracy and test accuracy.

Therefore, **Logistic Regression** was selected as the final model.

---

## Final Model Evaluation

The final Logistic Regression model was evaluated on the 479 samples in the held-out test set.

### Final Metrics

| Metric              | Result  |
|----------------------|---------|
| Test Accuracy        | 77.24%  |
| Precision (Class 1)  | 72.26%  |
| Recall (Class 1)     | 62.92%  |
| F1-Score (Class 1)   | 67.27%  |

### Classification Report

```
              precision    recall  f1-score   support

         0.0       0.80      0.86      0.83       301
         1.0       0.72      0.63      0.67       178

    accuracy                           0.77       479
   macro avg       0.76      0.74      0.75       479
weighted avg       0.77      0.77      0.77       479
```

---

## Confusion Matrix

|            | Predicted 0 | Predicted 1 |
|------------|-------------|--------------|
| **Actual 0** | 258         | 43           |
| **Actual 1** | 66          | 112          |

The values represent:

- **258 True Negatives:** Class 0 correctly predicted as Class 0.
- **43 False Positives:** Class 0 incorrectly predicted as Class 1.
- **66 False Negatives:** Class 1 incorrectly predicted as Class 0.
- **112 True Positives:** Class 1 correctly predicted as Class 1.

The model made 370 correct predictions out of 479 test samples, resulting in an accuracy of approximately 77.24%.

---

## Model Persistence

After selecting Logistic Regression, the trained model was saved locally using `joblib`.

The model was loaded again using `joblib.load()` and evaluated on the same test data. The accuracy matched the original model.

```text
Original Model Accuracy: 0.7724425887265136
Reloaded Model Accuracy: 0.7724425887265136
```

This confirmed that the saved model could be successfully restored.

---

## Model Limitations

The final model has some limitations:

1. The model is based only on the information available in the provided dataset.
2. The Class 1 recall is 62.92%, meaning some actual completers are predicted as non-completers.
3. The model results represent statistical associations and should not be interpreted as proof of causation.
4. The model should be treated as a prediction and support tool rather than an automatic decision-maker.
5. Performance may differ when the model is applied to a different learner population or future dataset.
6. The model has not been deployed as a production system.

---

## Repository Structure

The main project files are organized as follows:

```
Course-completion-predictor/
│
├── Data/
│   ├── cohort_train.csv
│   └── cohort_holdout.csv
│
├── Notebooks/
│   └── Course_Completion_Predictor.ipynb
│
├── README.md
├── REPORT.md
├── LOG.md
├── AI_USAGE.md
└── requirements.txt
```

### File Description

- **cohort_train.csv** — Training dataset used for the project.
- **cohort_holdout.csv** — Holdout dataset provided for prediction/evaluation where applicable.
- **Course_Completion_Predictor.ipynb** — Main Jupyter/Colab notebook containing the code and analysis.
- **REPORT.md** — Detailed project report.
- **LOG.md** — Daily project work log.
- **AI_USAGE.md** — Declaration of AI assistance and other learning resources.
- **requirements.txt** — Python packages required to run the project.

---

## How to Run

### Google Colab

1. Open `Notebooks/Course_Completion_Predictor.ipynb` in Google Colab.
2. Make sure the required dataset files are available in the expected location.
3. Run the notebook cells from top to bottom.

### Local Python Environment

Install the required packages:

```bash
pip install -r requirements.txt
```

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Then open:

```
Notebooks/Course_Completion_Predictor.ipynb
```

Run the notebook cells sequentially.

---

## Conclusion

This project successfully developed a Machine Learning classification pipeline for predicting course completion.

The project covered the main stages of a Machine Learning workflow, including data exploration, data cleaning, handling missing and invalid values, preventing data leakage, feature encoding, train-test splitting, model comparison, evaluation, and model persistence.

The unrestricted Decision Tree showed clear overfitting, while limiting the tree depth improved its test performance.

Among the tested models, Logistic Regression achieved the highest test accuracy of 77.24% and was selected as the final model.

The final model achieved 72.26% precision, 62.92% recall, and 67.27% F1-score for Class 1.

Overall, this project helped develop a practical understanding of how a Machine Learning model can be built from raw data through to evaluation and saving the final trained model.
