# Dataset Setup

## Dataset

**India Hospital Readmission Dataset 2015–2024**

Source: Kaggle  
https://www.kaggle.com/datasets/digutlaranjithkumar/india-hospital-readmission-dataset-20152024

## Required files

Place these five CSV files directly inside this `data/` directory:

- `admissions.csv`
- `patients.csv`
- `diagnoses.csv`
- `billing.csv`
- `hospitals.csv`

The final notebook loads them from the project-relative `data/` directory.

## Dataset size verified for this release

| File | Rows |
|---|---:|
| admissions.csv | 120,000 |
| patients.csv | 86,400 |
| diagnoses.csv | 271,341 |
| billing.csv | 120,000 |
| hospitals.csv | 33 |

## Why the raw CSVs are not part of the Git release

The repository is structured so that the raw CSV files are ignored by Git (`data/*.csv`) rather than accidentally committed. Obtain the dataset from the source above and place the files locally before running the notebook.
