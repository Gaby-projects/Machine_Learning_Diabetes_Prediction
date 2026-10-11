# 🩺 Diabetes Prediction from Health Indicators

![Data Analysis](https://img.shields.io/badge/Data_Analysis-1F4E79?style=for-the-badge)
![Feature Engineering](https://img.shields.io/badge/Feature_Engineering-2E7D32?style=for-the-badge)
![Machine Learning](https://img.shields.io/badge/Machine_Learning-6A1B9A?style=for-the-badge)

A machine learning model that flags people likely to have **diabetes or prediabetes** from their health, lifestyle and socioeconomic answers in the CDC's BRFSS 2015 survey. As a screening tool, it focuses on **recall**: missing a person with diabetes costs more than a false alarm.

## 📊 Dataset

- **Source:** [CDC Diabetes Health Indicators](https://www.kaggle.com/datasets/alexteboul/diabetes-health-indicators-dataset), from the CDC's BRFSS 2015 health survey (Kaggle / UCI ML Repository)
- **Size:** 253,680 survey answers, 21 features (~14% with diabetes), plus a balanced 50/50 version with 70,692 answers
- **Features include:**
  - Clinical health: high blood pressure, high cholesterol, BMI, stroke, heart disease, difficulty walking
  - Self-reported health: general health (1–5), bad mental and physical health days
  - Lifestyle: smoking, physical activity, fruits, vegetables, heavy drinking
  - Healthcare access and demographics: health coverage, cholesterol check, cost of seeing a doctor, sex, age, education, income
- **Outcome (50/50 version):** diabetes or prediabetes (1) vs. no diabetes (0), half and half
- **Files:** not included in this repo. Both versions are available on [Kaggle](https://www.kaggle.com/datasets/alexteboul/diabetes-health-indicators-dataset), and the full one on [UCI](https://archive.ics.uci.edu/dataset/891/cdc+diabetes+health+indicators).


## 🧹 Data preparation

- All columns are numeric and there are no missing values.
- BMI capped at 60: values like 92 or 98 are most likely input errors.
- The model was built on the balanced 50/50 file (70,692 answers), so both classes carry the same weight.
- 75/25 train/test split, with `StandardScaler` inside the pipeline.

## ⚙️ Feature engineering

New features built on clinical reasoning, plus interactions such as BMI × blood pressure.

**Feature selection:** dropped variables with a weak correlation to the target (|r| < 0.1) and redundant ones with a high VIF. The final model uses **20 features**: 14 original ones and these 6 new ones:

| Feature | Definition |
|---|---|
| `MetSyn_full` | High blood pressure, high cholesterol and BMI ≥ 30 together (metabolic syndrome proxy) |
| `Age_45plus` | Age 45 or older (ADA screening cut-off) |
| `PoorHealth` | Self-rated health "fair" or "poor" |
| `SevereObese` | BMI ≥ 40 |
| `Sedentary_Obese` | No physical activity and BMI ≥ 30 |
| `Lifestyle_score` | Physical activity + fruits + veggies + non-smoker + no heavy drinking |

## 🤖 Models tested

- Logistic Regression
- Decision Tree
- Random Forest
- KNN
- SVC
- Gradient Boosting ✅ (best performance)

All models were built with pipelines to combine preprocessing and training steps, and compared with 5-fold cross-validation. The two best, Gradient Boosting and Logistic Regression, were tuned with `GridSearchCV`.

## 📈 Results

**Best model: Gradient Boosting**, built and compared on the balanced 50/50 dataset

- Recall = 0.80 (caught 80% of people with diabetes)
- Precision ≈ 0.73
- ROC-AUC = 0.83

**Which features drive the prediction** (Gradient Boosting feature importance):

| Feature | Importance |
|---|---|
| `HighBP` | 35% |
| `GenHlth` | 25% |
| `BMI` | 10% |
| `Age` | 10% |
| `HighChol` | 5% |

High blood pressure and self-rated general health account for 60% of the model's decisions; the rest is split across BMI, age and the other 15 features.


## 🛠️ Tech stack

- [scikit-learn](https://scikit-learn.org/) – machine learning models, pipelines and evaluation
- [Python](https://www.python.org/) & [pandas](https://pandas.pydata.org/) – data cleaning and feature engineering
- [seaborn](https://seaborn.pydata.org/) & [matplotlib](https://matplotlib.org/) – exploratory charts
- [statsmodels](https://www.statsmodels.org/) – VIF to remove redundant features