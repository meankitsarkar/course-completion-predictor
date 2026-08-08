# Data Dictionary — Youth Skills Program, cohort export

Exported from the program's learner management system. One row per learner
enrolment.

This document was written by the operations team, not by a data team. It is
accurate as far as it goes, but it is not exhaustive — a few columns are legacy
and nobody currently on staff is completely sure how they were populated. Read
the column descriptions carefully; the timing of when a field gets filled in
matters as much as what it contains.

## Files

- **`cohort_train.csv`** — past learners, including whether they completed.
- **`cohort_holdout.csv`** — 400 further learners. Identical columns except that
  `completed_course` has been removed. Predict this column.

## Columns

| Column | Type | Description |
| --- | --- | --- |
| `learner_id` | text | Unique identifier for the enrolment, e.g. `L-01234`. Assigned at signup. |
| `signup_date` | date | Date the learner registered. Entered by hand at three different intake centres over the years, so the formatting is not consistent. |
| `age` | integer | Age in years, self-reported at signup. Not validated by the signup form. |
| `city_tier` | category | Government classification of the learner's city: 1 (metro), 2, or 3 (small town / rural). Recorded differently by different intake centres. |
| `education` | category | Highest education level completed, self-reported. Free-text field in older versions of the form, dropdown in newer ones. |
| `hours_per_week_pledged` | float | How many hours per week the learner said they would commit, asked at signup. Free-numeric field, not validated. |
| `weekly_logins_avg` | float | Average number of days per week the learner logged in **during the first three weeks** of the program. Range 0–7. |
| `assignments_submitted` | integer | Number of the **first 10** weekly assignments the learner submitted. Measured at the end of week 3. |
| `forum_posts` | integer | Posts made in the discussion forum during the first three weeks. A few learners post very heavily. |
| `video_watch_pct` | float | Percentage of the first three weeks' video content watched. Should be 0–100. The player occasionally over-reports when learners rewatch sections. |
| `has_mentor` | boolean | Whether the learner was assigned a volunteer mentor at intake. Stored as text, and the accepted values have changed as the system was migrated. |
| `device` | category | Primary device used to access the course in the first three weeks. Detected automatically from the browser, labels not normalised. |
| `referral_source` | category | How the learner heard about the program. |
| `payment_plan` | category | `free`, `scholarship` (sponsored, learner interviewed first), or `paid` (learner paid a nominal fee). |
| `days_since_last_login` | integer | Days between the learner's last login **in the first three weeks** and the end of week 3. |
| `certificate_issued` | boolean | Whether a completion certificate was printed and posted to the learner. The certificates team runs this batch **after the 12-week program ends**. Occasional manual errors in this field. |
| `final_score` | float | The learner's overall grade, 0–100, **computed at program close** from all 12 weeks of assessments. Blank where no grade was recorded. |
| `notes` | text | Free-text scratch field used by intake staff. No agreed convention, and often left blank. |
| `completed_course` | integer | **Target.** 1 if the learner completed the program, 0 if they dropped out. Determined at the end of the 12 weeks. |

## When each field becomes known

Roughly, in order:

1. **At signup:** `learner_id`, `signup_date`, `age`, `city_tier`, `education`,
   `hours_per_week_pledged`, `has_mentor`, `referral_source`, `payment_plan`
2. **By the end of week 3:** `weekly_logins_avg`, `assignments_submitted`,
   `forum_posts`, `video_watch_pct`, `device`, `days_since_last_login`
3. **At the end of the 12-week program:** `completed_course`, `final_score`
4. **After the program, in a batch:** `certificate_issued`

`notes` can be edited at any time.

**The prediction you are being asked to make happens at the end of week 3** —
that is the whole point of the exercise, because that is when a mentor can still
usefully intervene. Keep that in mind when you decide which columns your model
is allowed to see.

## Known issues (non-exhaustive)

The operations team is aware of the following, and there are likely others:

- The export script has been known to write some enrolments out twice.
- A small number of rows have no completion status recorded at all.
- Field validation on the signup form was only added in 2024, so older
  self-reported values can be nonsense.
- Text categories were never normalised across the system migration.

If you find a problem that isn't listed here, that's expected. Write it down.
