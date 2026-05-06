# Earthquake Magnitude Prediction — Central Asia

> Comparative ML study predicting earthquake magnitudes across Central Asia using a custom regional dataset (5,290 seismic events). Benchmarked 4 models, tuned the best with GridSearchCV, and conducted a focused analysis on Kyrgyzstan's seismic patterns.

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.3+-orange?logo=scikit-learn)
![Dataset](https://img.shields.io/badge/Dataset-5%2C290%20seismic%20events-purple)
![Region](https://img.shields.io/badge/Region-Central%20Asia-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## Results

### Model Comparison

| Model | MSE | MAE | R² | vs. Baseline |
|-------|:---:|:---:|:--:|:------------:|
| Baseline (mean predictor) | — | 0.4536 | — | — |
| Linear Regression | 0.2608 | 0.3726 | 0.331 | +18.4% MAE ↓ |
| Gradient Boosting | 0.2029 | 0.3124 | 0.480 | +31.1% MAE ↓ |
| Support Vector Machine | 0.3200 | 0.4120 | 0.180 | +9.2% MAE ↓ |
| Random Forest | 0.1965 | 0.3014 | 0.496 | +33.6% MAE ↓ |
| **Random Forest (tuned)** | **0.1919** | **0.2967** | **0.508** | **+34.6% MAE ↓** |

**Best model:** Tuned Random Forest Regressor — R² = 0.508, MAE = 0.297

### Hyperparameter Tuning (GridSearchCV, 5-fold CV)

```
Best parameters:
  n_estimators:      200
  max_depth:         None
  min_samples_split: 10
  min_samples_leaf:  2
```

### Top Predictive Features (Random Forest Feature Importance)

| Rank | Feature | Insight |
|------|---------|---------|
| 1 | **Depth** | Deeper earthquakes correlate strongly with higher magnitude — consistent with seismology |
| 2 | **Longitude** | East-west position tracks tectonic plate boundaries in the region |
| 3 | **Latitude** | North-south position captures proximity to the Pamir and Tian Shan fault systems |
| 4 | Year | Long-term seismic cycle signal |
| 5 | Minute | Low-importance temporal noise — expected |

---

## Dataset

- **Source:** Custom Central Asian Earthquake Dataset (`Central-Asian-earthquake-dataset.csv`)
- **Records:** 5,290 seismic events
- **Time span:** Multi-year historical record
- **Features:** Date, Time, Latitude, Longitude, Depth (km), Magnitude, Country
- **Countries covered:** Afghanistan, Kyrgyzstan, Tajikistan, Uzbekistan, Kazakhstan, China, Turkmenistan + border regions
- **Magnitude range:** 1.2 – 6.6 (mean: 2.40, std: 0.60)
- **Depth range:** 0 – 391 km (mean: 12.4 km — predominantly shallow events)

### Strongest Earthquakes by Country (Magnitude > 5.0)

| Country | Max Magnitude |
|---------|:-------------:|
| Afghanistan | **6.6** |
| Tajikistan | 6.0 |
| Uzbekistan | 5.8 |
| Kyrgyzstan | 5.1 |

---

## Kyrgyzstan Deep Dive

A dedicated analysis was conducted on the 860+ Kyrgyzstan events within the dataset:

- **Magnitude distribution:** Right-skewed, concentrated between 1.5–3.5
- **Depth:** Predominantly shallow (< 50 km), consistent with regional crustal seismicity
- **Temporal pattern:** Year-over-year count trends visualized; no strong seasonal signal observed
- **Key correlation:** Depth positively correlated with magnitude (r = 0.47) — deep-focus earthquakes tend to be stronger, consistent with regional geology (Tian Shan subduction zone dynamics)

---

## Approach

### Feature Engineering
- Extracted `Year`, `Month`, `Day`, `Hour`, `Minute` from datetime fields
- Created binary feature `IsShallow` (Depth < 50 km)
- One-hot encoded `Country` column (`drop_first=True`)
- 80/20 train/test split (`random_state=42`)

### Models Trained
1. **Linear Regression** — baseline parametric model
2. **Random Forest Regressor** — ensemble tree method
3. **Gradient Boosting Regressor** — sequential boosting
4. **Support Vector Regressor (SVR)** — kernel-based method

### Hyperparameter Tuning
GridSearchCV (5-fold cross-validation) over 81 parameter combinations for Random Forest:
- `n_estimators`: [50, 100, 200]
- `max_depth`: [None, 10, 20]
- `min_samples_split`: [2, 5, 10]
- `min_samples_leaf`: [1, 2, 4]

---

## Key Findings

**1. Earthquake magnitude prediction is a hard regression problem.** An R² of 0.508 means the model explains ~51% of magnitude variance — reasonable given that earthquake magnitude is influenced by subsurface fault geometry that no surface-observable feature can fully capture.

**2. Depth is the strongest predictor.** This aligns with established seismology: focal depth directly affects the stress regime and energy release mechanism of seismic events.

**3. Gradient Boosting and Random Forest significantly outperform linear models.** The non-linear relationship between geographic features and magnitude makes tree-based methods the clear choice here.

**4. SVR underperforms.** Without feature scaling, SVR's RBF kernel is at a disadvantage. Standardizing features before fitting SVR would likely improve its results substantially (noted for future work).

**5. A mean predictor (baseline MAE = 0.454) is easy to beat.** The tuned Random Forest achieves MAE = 0.297 — a 34.6% reduction, which is operationally meaningful for seismic risk assessment.

---

## Project Structure

```
earthquake-prediction-kyrgyzstan/
│
├── earthquake_KG_code.ipynb        # Full pipeline: EDA → modeling → tuning → visualization
├── Earthquake_Project_Report.pdf   # Detailed written report
├── earthquake_presentation.pdf     # Summary presentation slides
├── requirements.txt                # Dependencies
└── README.md
```

---

## How to Run

```bash
git clone https://github.com/Bufatima-Nk/earthquake-prediction-kyrgyzstan
cd earthquake-prediction-kyrgyzstan
pip install -r requirements.txt
jupyter notebook earthquake_KG_code.ipynb
```

> **Note:** The dataset file `Central-Asian-earthquake-dataset.csv` is required. Place it in the root directory before running the notebook.

---

## Tech Stack

| Category | Tools |
|----------|-------|
| ML Models | scikit-learn (RandomForest, GradientBoosting, LinearRegression, SVR) |
| Tuning | GridSearchCV (5-fold CV) |
| Data | pandas, NumPy |
| Visualization | Matplotlib, Seaborn, Plotly Express |
| Geo Visualization | Plotly Mapbox (scatter_mapbox) |

---

## Limitations & Future Work

- **R² ceiling (~0.51):** Physical predictability of magnitude from observable surface/temporal features is inherently limited. Incorporating subsurface geology or fault proximity features could improve this.
- **SVR without scaling:** Re-running SVR with `StandardScaler` would give a fairer comparison.
- **Temporal modeling:** A time-series approach (LSTM, or sequence-based features like "days since last event in region") could capture seismic clustering effects not captured by the current feature set.
- **Dataset expansion:** Merging with USGS or ISC global catalogs would dramatically increase training data.
- **Deployment:** A Streamlit app allowing users to input coordinates and get a predicted magnitude range would make this immediately interactive.

---

## Author

**Bufatima N.K.**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-bufatima--n--k-blue?logo=linkedin)](https://linkedin.com/in/bufatima-n-k)
[![GitHub](https://img.shields.io/badge/GitHub-Bufatima--Nk-black?logo=github)](https://github.com/Bufatima-Nk)
