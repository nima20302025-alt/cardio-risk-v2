# ❤️ Cardiovascular Disease Risk Predictor

An end-to-end machine learning app that predicts a patient's risk of
cardiovascular disease (CVD) from routine clinical measurements
(age, blood pressure, cholesterol, BMI, lifestyle factors), served through
an interactive Streamlit interface and deployed on Railway.

**🔗 Live demo:** _add your Railway URL here after deploying_

![Python](https://img.shields.io/badge/Python-3.11-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-1.38-red)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.5-orange)

## Overview

Trained on the [Cardiovascular Disease dataset](https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset)
(Kaggle, 70,000 patient records), this project compares five classifiers,
selects the best by validation ROC-AUC, and ships it behind a small web app
where a user can enter a patient's vitals and get a live risk estimate with
an explanation of what the model learned.

| | |
|---|---|
| **Best model** | Gradient Boosting |
| **Test accuracy** | ~74% |
| **Test ROC-AUC** | ~0.81 |

## Features

- 🔍 **Interactive prediction** — enter age, blood pressure, cholesterol,
  glucose, weight/height, and lifestyle factors; get an instant risk score
  with a gauge visualization.
- 🧠 **Per-patient explainability (SHAP)** — every prediction comes with a
  breakdown of which of that specific patient's measurements pushed their
  risk up or down, not just a global feature-importance chart.
- 📄 **Batch prediction** — upload a CSV of many patients, score them all
  at once, and download the results (with a ready-made CSV template).
- 📈 **Dataset exploration** — interactive charts of age, BMI, blood
  pressure, and cholesterol against CVD outcomes, plus a feature/target
  correlation chart.
- 📊 **Model performance dashboard** — validation comparison across five
  model families, feature importance, and a confusion matrix on the
  held-out test set.
- 🧼 **Reproducible data pipeline** — documented cleaning of physiologically
  implausible readings (e.g. diastolic ≥ systolic blood pressure) and
  feature engineering (age in years, BMI, pulse pressure).
- 🚀 **One-command deploy** to Railway.

## Project structure

```
.
├── app.py                    # Streamlit app (UI + inference)
├── train_model.py            # Data cleaning, model training & selection
├── requirements.txt
├── Procfile                  # Railway/Heroku-style start command
├── railway.json              # Explicit Railway build/deploy config
├── runtime.txt                # Pinned Python version
├── .streamlit/config.toml    # Streamlit server/theme config
├── data/
│   └── cardio_train.csv      # Training data (Kaggle)
└── models/
    ├── cardio_pipeline.joblib  # Trained, ready-to-serve pipeline
    └── metrics.json             # Saved evaluation metrics for the app
```

## Methodology

1. **Cleaning** — drop rows with impossible blood pressure or
   height/weight values (~2% of rows).
2. **Feature engineering**
   - `age_years` — age converted from days to years
   - `bmi` — weight (kg) / height (m)²
   - `pulse_pressure` — systolic − diastolic blood pressure
3. **Model comparison** — Logistic Regression, Decision Tree, Random
   Forest, Gradient Boosting, and k-NN, each in a `StandardScaler` +
   classifier pipeline, evaluated on a stratified 70/15/15 train/val/test
   split.
4. **Selection** — best model chosen by validation ROC-AUC, then scored
   once, and only once, on the held-out test set.
5. **Deployment** — the exact selected pipeline is serialized with
   `joblib` and loaded by the Streamlit app; there is no train/deploy
   mismatch.

## Run locally

```bash
git clone https://github.com/<your-username>/cardio-risk-app.git
cd cardio-risk-app
pip install -r requirements.txt

# (optional) retrain the model from scratch
python train_model.py --data data/cardio_train.csv --out models

streamlit run app.py
```

The app will be available at `http://localhost:8501`.

## Deploy to Railway

1. Push this repo to GitHub.
2. On [railway.app](https://railway.app), click **New Project → Deploy from
   GitHub repo** and select this repository.
3. Railway auto-detects Python via `requirements.txt` and uses the
   `Procfile` / `railway.json` start command:
   ```
   streamlit run app.py --server.port=$PORT --server.address=0.0.0.0
   ```
4. No environment variables or database are required — the trained model
   is committed to the repo (`models/cardio_pipeline.joblib`), so the app
   is ready to serve immediately after build.
5. Once deployed, Railway gives you a public URL — add it to the top of
   this README.

## Disclaimer

This project is for educational and portfolio purposes. It is **not** a
medical device and should not be used to make real clinical decisions.

## Dataset & credit

- Dataset: [Cardiovascular Disease dataset](https://www.kaggle.com/datasets/sulianova/cardiovascular-disease-dataset)
  by Svetlana Ulianova, Kaggle (CC0 license).
