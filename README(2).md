# Hospital Performance, Patient Flow & Readmission Analytics

### Hospital Analytics & Readmission Intelligence

AICTE | IBM SkillsBuild Data Analytics with AI Academic Internship 2026 | BharatCares

## 1. Project Overview

An end-to-end healthcare analytics project that transforms five relational hospital datasets into operational KPIs, patient-flow and financial insights, readmission analysis, and an explainable machine-learning model for 30-day readmission risk.

The modeling task is framed **at discharge**, using information available by the time discharge planning is made. The project is analytical and retrospective; it is not a clinically validated deployment.

## 2. Problem Statement

Hospitals need a unified view of performance, patient flow, financial burden, and readmission patterns. This project integrates admissions, patients, diagnoses, billing, and hospital master data while preserving one row per admission episode, then uses descriptive analytics and ML to identify patterns associated with 30-day readmission.

## 3. Objectives

- Measure hospital performance and capacity indicators.
- Profile patient demographics, comorbidity and prior-admission burden.
- Analyse admission/discharge flow and length of stay.
- Examine billing, subsidy and out-of-pocket burden.
- Compare 30-day and 7-day readmission patterns.
- Build and compare Logistic Regression, Random Forest and XGBoost models.
- Use SHAP with the trained XGBoost model for global and local explanations.
- Translate observed patterns into operational hypotheses and recommendations.

## 4. Business Questions

- How many admissions and unique patients are represented?
- What are the system-wide length-of-stay and readmission rates?
- Which patient and episode characteristics are associated with higher readmission rates?
- How do hospital tier, teaching status and capacity relate to performance?
- Where is financial burden concentrated?
- Which model provides the strongest held-out ROC-AUC for 30-day readmission?
- Which features contribute most to XGBoost predictions?

## 5. Dataset

**India Hospital Readmission Dataset 2015–2024**

Source: Kaggle — https://www.kaggle.com/datasets/digutlaranjithkumar/india-hospital-readmission-dataset-20152024

The supplied dataset contains:

| File | Rows | Role |
|---|---:|---|
| `admissions.csv` | 120,000 | Admission episodes and clinical/flow outcomes |
| `patients.csv` | 86,400 | Patient demographics and prior-admission information |
| `diagnoses.csv` | 271,341 | Diagnosis records; aggregated before admission-level merge |
| `billing.csv` | 120,000 | Admission-level financial information |
| `hospitals.csv` | 33 | Hospital master attributes |

### Dataset architecture

`patients` 1:N `admissions` 1:N `diagnoses`

`admissions` 1:1 `billing`

`admissions` N:1 `hospitals`

Diagnoses are aggregated before merging so the final modeling table remains at **one row per admission episode**.

## 6. Technologies

- Python
- Pandas / NumPy
- Matplotlib / Seaborn
- Scikit-learn
- XGBoost
- SHAP
- Jupyter Notebook

## 7. Analytics Performed

- Data quality and duplicate-key checks
- Relational joins and admission-grain validation
- Exploratory demographic and clinical analysis
- Hospital performance analysis
- Patient analytics
- Patient-flow and length-of-stay analysis
- Financial and subsidy analysis
- 30-day / 7-day readmission analysis
- Leakage audit
- KPI dashboard-style summary
- Model comparison and evaluation
- Confusion matrix and ROC curve analysis
- SHAP global, beeswarm and local explanations

## 8. KPI Summary

Final executed notebook values:

| KPI | Value |
|---|---:|
| Total admissions | 120,000 |
| Unique patients | 64,873 |
| Average length of stay | 6.85 days |
| 30-day readmission rate, eligible discharges | 12.62% |
| 7-day readmission rate, eligible discharges | 0.89% |
| Average out-of-pocket cost | ₹48,173 |
| Total billing | ₹1,149.4 crore |
| Average billing per admission | ₹95,780 |
| Government subsidy | ₹571.3 crore |
| System-average admissions per bed | 15.92 |
| Average prior admissions | 0.90 |
| Average comorbidity count | 1.43 |
| Average patient age | 48.0 years |

The eligible readmission denominator excludes `Expired` discharges: 112,587 of 120,000 admissions (93.8%).

## 9. Machine Learning Methodology

### Target

`readmitted_30d`

### Prediction framing

The model predicts 30-day readmission **at discharge**, not at admission. Expired discharges are excluded because they cannot subsequently be readmitted.

### Leakage controls

- `readmitted_7d` is excluded because it is a future outcome and directly leaks information about the 30-day outcome window.
- IDs are excluded.
- Raw admission/discharge timestamps are excluded as direct model features.
- High-cardinality diagnosis text/codes are excluded in favour of `diag_category`.
- Preprocessing is fit only on the training split.
- Train/test splitting is stratified.
- Retrospective aggregate features such as full-period admissions-per-bed are disclosed as a limitation for any future prospective deployment.

### Models

1. Logistic Regression
2. Random Forest
3. XGBoost

The readmission-positive class is approximately 12.6% of the eligible modeling population. Class weighting is used rather than oversampling.

## 10. Model Comparison

Final held-out test ROC-AUC:

| Model | ROC-AUC |
|---|---:|
| Logistic Regression | 0.7571 |
| Random Forest | 0.7481 |
| XGBoost | 0.7535 |

The notebook selects the model with the highest test ROC-AUC. In the final execution, this is **Logistic Regression (0.7571)**.

For the selected Logistic Regression model:

- Accuracy: 0.7125
- Precision: 0.2546
- Recall: 0.6633
- F1: 0.3680
- ROC-AUC: 0.7571

Confusion-matrix counts on the held-out test set:

- TN: 14,158
- FP: 5,518
- FN: 957
- TP: 1,885

Accuracy is not interpreted alone because the positive class is only about 12.6%.

## 11. Explainability / SHAP

Real SHAP is calculated using `shap.TreeExplainer` on the trained **XGBoost** model. SHAP is therefore an explanation of XGBoost predictions, not of the selected Logistic Regression model.

The final run used a 2,000-row test-set sample for SHAP and produced 58 transformed model features after preprocessing.

Top features by mean absolute SHAP value in the final run included:

1. `age_comorb_score`
2. `los_days`
3. `ward_type_General`
4. `prev_admissions`
5. `insurance_type_Private`
6. `charlson_index`
7. `discharge_type_LAMA`
8. `haemoglobin`
9. `bpl_card_False`
10. `is_icu_stay`

SHAP values explain model behaviour and feature contribution; they do not establish causation.

## 12. Key Insights

- Clinical burden and episode severity are prominent readmission-related signals: age, comorbidity burden, prior admissions and length of stay appear repeatedly in the analysis and SHAP results.
- ICU/emergency episodes and LAMA discharges form important higher-risk operational segments in the analysis.
- Financial burden is concentrated in higher-cost care segments, with meaningful variation in out-of-pocket exposure across insurance/BPL groups.
- Tier-3 hospitals show substantially higher admissions-per-bed, providing a capacity-planning signal.
- Expired discharges have a mechanically zero readmission outcome, so excluding them from the readmission denominator and ML population is important for meaningful interpretation.

These are observed associations in the supplied retrospective dataset, not causal conclusions.

## 13. Recommendations

- Use risk scoring as a prioritisation signal for post-discharge follow-up rather than an automated clinical decision.
- Consider targeted discharge-review workflows for high-acuity and high-risk segments.
- Review LAMA cases and follow-up processes separately.
- Use admissions-per-bed as an input to capacity and resource-planning reviews.
- Monitor financial burden by insurance type alongside BPL status.
- Pilot and measure any intervention before treating it as effective; the project does not provide clinical validation.

## 14. Limitations

- Observational, retrospective data; associations are not causal effects.
- Only 33 hospitals are represented, limiting generalisation of hospital-level comparisons.
- The model is not clinically validated, calibrated prospectively, or tested in production.
- `hospital_admission_volume` / `admissions_per_bed` are calculated over the full dataset and would need a trailing-window design for prospective deployment.
- ROC-AUC around 0.75 indicates useful discrimination in this test split but leaves substantial unexplained variation.
- SHAP is demonstrated for XGBoost even though Logistic Regression has the highest ROC-AUC in the final run.

## 15. Future Scope

- Temporal or patient-level validation to better simulate deployment.
- Prospective/trailing-window hospital capacity features.
- Calibration and threshold analysis based on operational follow-up capacity.
- External validation on a separate hospital/dataset population.
- Fairness and subgroup-performance analysis.
- Integration into a monitored decision-support workflow after appropriate clinical governance and validation.

## 16. Project Structure

```text
Hospital-Performance-Readmission-Analytics/
├── KashishKamaal_HospitalAnalytics.ipynb
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── README.md
└── docs/
    └── KashishKamaal_HospitalAnalytics_ProjectReport.docx
```

Raw CSVs are kept locally for execution but are ignored by Git by default.

## 17. Installation

```bash
git clone <your-repository-url>
cd Hospital-Performance-Readmission-Analytics
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
```

## 18. Running the Notebook

1. Download the five dataset CSVs from the Kaggle source.
2. Place them in `data/` using the exact filenames listed above.
3. Start Jupyter:

```bash
jupyter notebook
```

4. Open `KashishKamaal_HospitalAnalytics.ipynb`.
5. Restart the kernel and run all cells from top to bottom.

The notebook uses the project-relative `data/` directory and does not depend on machine-specific `/mnt/...` or Windows absolute paths.

## 19. Dataset Setup Instructions

See [`data/README.md`](data/README.md). The raw CSVs are not intended to be committed automatically; `.gitignore` contains `data/*.csv`.

## 20. Author

**Kashish Kamaal**  
B.Tech — Computer Science & Engineering (AI/ML)  
LNCT, Bhopal

---

**Project status:** final local release candidate validated by clean notebook execution. No GitHub push is performed by this release package.
