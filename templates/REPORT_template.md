# Report — Course Completion Predictor

**Name:**
**Dates worked:**
**Total hours (your honest estimate):**
**Video walkthrough:** <link>

> Replace every italic prompt with your own writing. If a section doesn't apply
> to you, keep the heading and write why. Plain, direct English is what we want —
> no need to sound impressive. Aim for something a program manager could read.

---

## 1. Summary — read this if you read nothing else

*Five sentences maximum. What did you build, how well does it work, and what is
the single most useful thing you learned about why learners drop out?*

---

## 2. The data

### What I found when I explored it

*What does this dataset actually contain? How many learners, what completion
rate, what stood out?*

### Data problems I found and what I did about them

*One row per problem. Be specific about counts. Add as many rows as you need —
there are more than four.*

| # | Problem | How many rows | What I did | Why |
| --- | --- | --- | --- | --- |
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |

### Columns I excluded from the model, and why

*Required section. For every column you did not feed the model, say why. Pay
particular attention to any column whose value would not exist at the moment the
prediction has to be made.*

| Column | Excluded because |
| --- | --- |
|  |  |

*If you found a column that was leaking the answer: how did you notice? What
were your scores before and after removing it? We are much more interested in
this paragraph than in your final accuracy.*

---

## 3. What I did

### Baseline

*What accuracy do you get by always predicting the majority class? Every model
below is compared against this number.*

- Majority-class baseline accuracy: ____

### How I split the data

*What split or cross-validation scheme, and why. Why can't you evaluate on the
data you trained on?*

### Features

*What went into the model in the end? Anything you built yourself out of
existing columns? Did it help?*

### Models I tried

*Fill in every model you ran, including the ones that did badly. Failed
experiments are results — do not delete them.*

| Model | Accuracy | Precision | Recall | F1 | Notes |
| --- | --- | --- | --- | --- | --- |
| Majority-class baseline |  |  |  |  | no model |
|  |  |  |  |  |  |
|  |  |  |  |  |  |

### The model I chose, and why

*Not necessarily the highest-scoring one. Say why you picked it.*

---

## 4. How well does it work, really?

### Confusion matrix

*Paste it in, then interpret it.*

```
                 predicted: drop out    predicted: complete
actual: drop out
actual: complete
```

### What the two kinds of mistake mean for the program

*In plain English, in the program's terms. When the model is wrong in each of the
two possible directions, what actually happens to a real learner and to the
organisation? Which mistake is more expensive here?*

### My decision threshold

*What cutoff did you use to turn a probability into a yes/no, and why that one?
If you used 0.5, defend it as a choice rather than accepting it as a default.*

### Where this model should not be trusted

*Which learners does it get wrong? What would break it? What would you warn a
program manager about before they used it on real people?*

---

## 5. What actually predicts dropping out

*The part a program manager cares about. Which factors matter most, and how do
you know? Be careful with your language here: your model shows association, not
proof of cause. If early logins predict completion, that does not by itself mean
that making someone log in more would make them finish.*

### If I were advising the program team

*Two or three concrete, actionable suggestions supported by what you found. What
should the mentors actually do differently on the Monday of week 4?*

---

## 6. Honesty section

*This section is worth more to us than section 3. Please take it seriously — a
thoughtful answer here is a strong positive signal, not an admission of
weakness.*

### Things I did not understand

*What did you use without fully understanding it? What concept still feels fuzzy?
Everyone has some of these; naming them accurately is a skill.*

### Things I got wrong along the way

*What did you get wrong, and how did you find out? Include the embarrassing one.*

### What I would do with two more weeks

*Ranked, with your reasoning for the ranking.*

### What is unfinished or broken in this submission

*Be specific. Nothing here is penalised if it's declared.*

---

## 7. Level 3 — what I chose to do

*The open-ended part. What did you decide "make this useful to a program manager"
meant, and why? If you didn't get to it, say so — that's fine.*

---

## 8. Checklist

*Paste in the completed `SUBMISSION_CHECKLIST.md`.*
