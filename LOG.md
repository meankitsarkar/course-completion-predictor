# Work Log

## Day 1 - Setup and Initial Exploration

**Date:** 08 August 2026  
**Time spent:** Around 2 hours

### What I did

Today I started the Course Completion Predictor project.

I loaded the training and holdout CSV files in Google Colab using pandas. I checked the size of both datasets and looked at the first few rows.

The training dataset has 2484 rows and 19 columns. The holdout dataset has 400 rows and 18 columns.

I also checked the column names, data types, and missing values.

I checked the `completed_course` column to understand how many learners completed the course and how many did not.

### What I found

There are 2475 learners with a known completion result.

- 1561 learners did not complete the course.
- 914 learners completed the course.
- Around 63.1% did not complete.
- Around 36.9% completed.

I also found that some columns have missing values. There are also 83 completely duplicated rows.

### What was difficult

I am new to Python and machine learning, so I had to understand what things like `shape`, `info()`, `isnull()` and `value_counts()` mean.

### What I learned

I learned how to load a CSV file into pandas and how to get basic information about a dataset.

### Next step

Tomorrow I will explore the columns in more detail and look for problems in the data.


## Day 2 - Data Exploration

**Date:** 08 August 2026  
**Time spent:** Around 2 hours

### What I did

Today I explored the dataset more carefully.

I checked the different values in the category columns and also checked the numeric columns using `describe()`.

I also checked duplicate learner IDs and looked at the learners where the completion result is missing.

### Problems I found

I found several data quality problems.

1. `city_tier` has different ways of writing the same values. For example, there are values like `1`, `T1`, `Tier 1` and `tier-1`.

2. `education` also has different versions of the same education level. For example, `Bachelors`, `Bachelor's`, `bachelors` and `bachelor degree`.

3. `has_mentor` has many different values for yes and no, such as `1`, `TRUE`, `Yes`, `Y`, `0`, `FALSE`, `No` and `N`.

4. `device` has different names for similar devices. For example, `mobile`, `Mobile`, `MOBILE` and `phone`.

5. The age column has some impossible values. The minimum age is -3 and the maximum is 300.

6. `hours_per_week_pledged` has a very high value of 326.8 hours per week, which is not realistic because there are only 168 hours in a week.

7. `video_watch_pct` has values above 100. The maximum is 115.3%.

8. I found 83 completely duplicated rows.

9. I found 84 duplicate learner IDs. I checked some of them and the repeated records appeared to contain the same information.

10. There are 9 learners where `completed_course` is missing.

11. Several columns have missing values, including age, education, city tier, mentor information and some activity columns.

### Things that looked okay

`referral_source` and `payment_plan` did not show the same type of inconsistent values.

`weekly_logins_avg` was within the expected 0 to 7 range.

`assignments_submitted` was also within the expected range of 0 to 10.

### Something important I noticed

I noticed that `final_score` and `certificate_issued` are related to information that becomes available after the course finishes.

Since our prediction is supposed to happen at the end of week 3, I need to be careful not to use information that would not have been available at that time.

### What was difficult

The main difficult part was understanding which values are actually problems and which ones are normal. I also had to learn how to investigate duplicate records instead of immediately deleting them.

### What I learned

I learned that real data is not always clean. The same thing can be written in different ways, some values can be missing or impossible, and duplicate records can exist.

I also learned that we need to think about when information becomes available before using a column for prediction.

### Next step

On Day 3 I will start cleaning the data. I will make a decision for each problem I found and record why I made that decision.
