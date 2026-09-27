# HealthConnect-Final-Integration-Presentation-Project-Showcase

Predicting missed medical appointments so a healthcare provider can identify higher-risk appointments before they happen and decide where a reminder or other low-cost intervention may help.

> **AnalystLab Africa | Experience Lab Internship Programme**
> Data Science Track | Weeks 4–8

---

## The Problem

HealthConnect Clinic is a fictional healthcare provider used for this internship project.

Missed appointments can mean wasted appointment slots, staff time, and clinic capacity. The goal of this project was to use historical appointment data to identify upcoming appointments with a higher risk of no-show.

The idea is not to make decisions about patients. It is to give clinic staff another signal they can use when deciding where a low-cost intervention, such as a reminder call, may be useful.

## What's in This Repo

This repository contains my Data Science work across an 8-week multidisciplinary project. The wider project included Data Analytics, Data Science, ML Engineering, Generative AI, and Project Management tracks working from the same dataset.

| Week | Focus                                                                          |
| ---- | ------------------------------------------------------------------------------ |
| 4    | Problem framing, target definition, and assessment of dataset suitability      |
| 5    | Feature engineering, train/test strategy, and baseline model comparison        |
| 6    | Model refinement, cross-validation, and integration of Data Analytics findings |
| 7    | Feature testing, overfitting checks, segment analysis, and threshold testing   |
| 8    | Final model integration, documentation, and handoff specification              |

## Dataset

`HealthConnect_Appointment_Data.csv` contains 5,000 appointment records and 18 columns covering patient demographics, appointment and booking details, reminder status, distance to the clinic, waiting time, and appointment outcome.

The outcome contains three categories:

* `Attended`
* `No-Show`
* `Cancelled`

For modeling:

* Target: `is_no_show`
* Cancelled appointments were excluded
* A cleaned version of the dataset is used for modeling

## Final Model

The final model is **Logistic Regression** with `class_weight='balanced'`.

I used grouped 5-fold cross-validation so that appointments from the same patient do not appear across different folds. This helps reduce patient-level leakage during evaluation.

| Metric                  | Week 5 Baseline | Final Model            |
| ----------------------- | --------------- | ---------------------- |
| Accuracy (5-fold CV)    | 0.624           | 0.629                  |
| ROC-AUC (5-fold CV)     | 0.680           | 0.680                  |
| Train/test accuracy gap | —               | ~0.3 percentage points |

The improvement in accuracy is small, and ROC-AUC stayed the same. The main value of the later work was testing whether the model and its features actually held up under further evaluation.

### Main Features

The final feature set includes:

* `previous_no_shows`
* `booking_lead_days`
* `distance_to_clinic_km`
* `previous_no_shows × reminder`
* `distance × reminder`

The two interaction features came from findings identified by the Data Analytics track and were tested in the Data Science model.

### Decision Threshold

A working threshold of **0.45** was tested against the default 0.50 threshold.

At 0.45, recall was about 72%, compared with about 63% at 0.50.

The reasoning was that, for a low-cost intervention such as an additional reminder call, it may be useful to catch more potential no-shows. The final threshold should depend on the actual cost and capacity of the intervention, so 0.45 remains provisional.

## Key Findings

### 1. Previous no-shows and lead time matter

`previous_no_shows` and `booking_lead_days` were among the strongest signals in the model.

Historical patient behaviour was useful for identifying risk, while booking lead time also carried predictive information.

### 2. A useful pattern is not automatically a useful feature

I created lead-time bands because they showed a clear pattern during exploratory analysis.

When I tested the bands against the raw `booking_lead_days` feature, the model performed almost identically.

The bands were kept because they make the pattern easier to communicate, not because they improved predictive performance.

That distinction is documented in the project rather than treating an interpretability benefit as a model improvement.

### 3. Performance varies across groups

The model did not perform equally across all patient segments.

* Diagnostic Tests: **71.6% accuracy**
* Specialist Consultations: **58.3% accuracy**
* Ages 55–64: **68.1% accuracy**
* Ages 65+: **59.2% accuracy**

These differences are documented as limitations and areas for further investigation. The current analysis does not establish why the gaps exist.

### 4. Not every cross-track finding held up in the model

The `previous_no_shows × reminder` interaction retained predictive weight in the model.

The `distance × reminder` interaction did not show the same signal.

This was useful because it showed that a pattern found during exploratory analysis still needs to be tested in the actual predictive model.

### File overview

* `data/` contains the raw and cleaned datasets
* `notebooks/` contains the final modeling notebook
* `docs/` contains the final Data Science package, including model documentation, error analysis, and the ML handoff specification

## Reproducing the Results

Install the required packages:

```bash
pip install pandas numpy scikit-learn matplotlib
```

Then open the notebook:

```bash
jupyter notebook notebooks/HealthConnect_Week8_Final_Model_Notebook.ipynb
```

Run the notebook from top to bottom.

Figures are saved to `processed_w7/`.

## Model Suitability and Limitations

The model is best viewed as a **decision-support tool for low-cost interventions**, such as reminder calls.

It should not be used on its own to make decisions that could meaningfully affect individual patients.

Extra caution is needed for groups where the model performed less reliably, particularly Specialist Consultation appointments and patients aged 65+.

### Open Items

* The differences in segment performance have not yet been fully explained.
* A full one-feature-at-a-time ablation was not completed across the entire feature set. The lead-time feature was tested this way.
* The decision threshold is provisional and needs real intervention-cost and capacity information.
* Day-of-week effects remain unresolved.

## Cross-Track Collaboration

### Data Analytics

The Data Analytics track provided crosstab findings that became the starting point for two interaction features.

The Data Science work then tested whether those patterns actually contributed to the predictive model.

### ML Engineering

The final feature list, encoding approach, and configurable threshold are documented for handoff.

See the files in `docs/` for the model documentation and handoff specification.

## Author

**Jadon Umeri**
Data Science Track Intern, AnalystLab Africa Experience Lab

[LinkedIn](www.linkedin.com/in/jadon-umeri-12b2b93a4) | [GitHub](https://github.com/umerijadon)

---

#AnalystLabAfrica #DataScience
