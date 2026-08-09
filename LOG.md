# Daily Work Log

## Day 1

**Time Spent:** 2 hours

### Tasks Completed

* Created GitHub repository.
* Set up Google Colab notebook.
* Uploaded the datasets.
* Loaded the training and holdout CSV files.
* Explored the dataset using:
  * head()
  * shape
  * columns
  * info()
  * describe()
* Read the Data Dictionary and understood each feature.

### Challenges

* Learning the basics of Pandas and DataFrames.

### Next Steps

* Explore missing values, duplicates, and data quality issues.

## Day 2

**Time Spent:** ~2 hours

### Tasks Completed

* Investigated missing values across the training dataset.
* Calculated missing-value percentages.
* Checked for duplicate learner IDs and exact duplicate rows.
* Investigated duplicated learner records and found 84 rows with repeated learner IDs.
* Identified 83 exact duplicate rows.
* Investigated numerical ranges and identified impossible age values.
* Identified impossible pledged study hours above 168 hours per week.
* Investigated video watch percentages above 100%.
* Explored categorical columns and identified inconsistent education, mentor, device, and city-tier labels.
* Identified `final_score` and `certificate_issued` as possible future-information/data-leakage columns.
* Identified 9 training records with a missing `completed_course` value.

### Problems Encountered

* Several columns contain missing values.
* The dataset contains duplicate learner records.
* Some numerical values are clearly impossible.
* Several categorical columns use inconsistent labels.
* Some information may not be available at the time the prediction is supposed to be made.

### Key Findings

* 84 rows contain duplicated learner IDs.
* 83 rows are exact duplicates.
* 9 training records have no `completed_course` value.
* Several age values are impossible, including `-3`, `199`, and `300`.
* Some learners have impossible pledged study hours above 168 hours per week.
* Several video watch percentages are above 100%.
* Several categorical fields contain inconsistent representations of the same category.
* `final_score` and `certificate_issued` may contain information that would not be available at the prediction point.

### Next Steps

* Clean duplicate records.
* Handle missing values.
* Standardize categorical values.
* Handle impossible numerical values.
* Create a cleaned dataset for modelling.


## Day 3

**Time Spent:** ~2 hours

### Tasks Completed

* Removed 83 exact duplicate rows from the training dataset.
* After removing duplicates, the dataset had 2,401 rows and 19 columns.
* Standardized `city_tier` values into 1, 2, and 3.
* Standardized `education` values into common categories such as:
  * Bachelor
  * High School
  * Diploma
  * Masters
  * Other
* Standardized `has_mentor` values into `Yes` and `No`.
* Standardized `device` values into common device categories.
* Checked numerical columns for impossible values.
* Found 29 impossible age values and replaced them with missing values.
* Found 8 impossible `hours_per_week_pledged` values and replaced them with missing values.
* Found 36 `video_watch_pct` values above 100% and corrected them.
* Checked `weekly_logins_avg`, `assignments_submitted`, and `forum_posts` and found no impossible values.
* Checked `days_since_last_login` and confirmed the cleaned values were within the range of 0 to 70 days.
* Standardized `certificate_issued` values into `yes` and `no`.
* Checked `final_score` and found no impossible scores.
* Replaced `-` in the `notes` column with a missing value.
* Kept missing `final_score` and `notes` values as missing instead of filling them with guessed values.
* Removed 9 rows where `completed_course` was missing because it is the target variable.
* Reset the dataset index after removing rows.
* Performed a final duplicate and missing-value check.

### Important Findings

* The final cleaned dataset contains 2,392 rows and 19 columns.
* There are 0 duplicate rows remaining.
* There are 0 missing values in `completed_course`.
* `final_score` still has missing values, but these were kept because the missing values are meaningful and should not be filled with guessed scores.
* `notes` also contains missing values and was kept as missing.
* The categorical columns are now much more consistent.
* Invalid numerical values identified during Day 2 were cleaned.

### Challenges

* Deciding whether a value was actually wrong or just missing was one of the difficult parts.
* Some categorical columns had many different versions of the same value.
* I had to be careful not to fill missing target values because `completed_course` is what the model will predict.
* I also had to understand why some missing values, such as `final_score`, should be left missing instead of being replaced with an average.

### Final Data Quality Check

* Final dataset shape: **2,392 rows × 19 columns**
* Duplicate rows: **0**
* Missing `completed_course`: **0**
* Impossible age values remaining: **0**
* Impossible pledged-hours values remaining: **0**
* Video watch values above 100% remaining: **0**
* Final score impossible values: **0**

### Next Steps

* Prepare the cleaned data for machine learning.
* Decide which columns should be used as model features.
* Handle the remaining missing values during the preprocessing stage.
* Establish a baseline model.
* Train and evaluate the first machine-learning model.
