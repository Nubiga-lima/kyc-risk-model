# Model Documentation — KYC Risk Classification Model

## 1. Purpose and Intended Use

This model is a demonstration project developed to illustrate model lifecycle,
governance, and validation practices relevant to KYC (Know Your Customer) and
financial crime risk modelling in a regulated banking context.

**This is a learning/demonstration project built on fully synthetic data.**
It does not use, and was not trained on, any real customer or transaction data.
Its purpose is to demonstrate technical competency in model development,
documentation, validation, and explainability practices — not to represent
a production-ready KYC risk model.

The model classifies customers into three risk tiers (Low / Medium / High) 
based on synthetic demographic, transactional, and financial-crime-indicator
attributes, incorporating features conceptually related to common KYC risk
factors (e.g. jurisdiction risk, PEP status, adverse media flags, and transaction behaviour). 

## 2. Data

- **Source:** Fully synthetic data, generated programmatically
  (`notebooks/01_data_generation.ipynb`).
- **Size:** 5,000 synthetic customer records.
- **Features:** Age, country risk tier, monthly transaction count and volume,
  product risk score, PEP flag, adverse media flag, and derived jurisdiction
  risk flag.
- **Target label:** `risk_class` (Low / Medium / High), derived from a
  transparent weighted formula over jurisdiction risk, PEP status, adverse
  media flags, and transaction frequency, with random noise added to avoid
  the label being a perfect deterministic function of the input features
  (see Section 5, Known Limitations).
- **Class balance:** Low 4,178 (83.6%) / Medium 792 (15.8%) / High 30 (0.6%).
 This imbalance is intended to approximate a plausible KYC risk-scoring setting
in which high-risk customers represent a small minority.

## 3. Feature Engineering

Documented in `notebooks/02_feature_engineering.ipynb`. Key steps:
- One-hot encoding of categorical `country_risk` (baseline category: High).
- Engineered features reflecting KYC/FC domain logic: transaction intensity
  (volume per transaction), high-volume flag, PEP × high-risk-jurisdiction
  interaction, combined adverse-media/PEP indicator, and a behavioural risk
  score.
- Standard scaling applied to numeric features.

## 4. Model Architecture

Two models were developed and compared:

- **Baseline: Logistic Regression** (`class_weight="balanced"`) — a simple,
  interpretable linear model, favoured in regulated contexts for its
  transparency.
- **Challenger: Random Forest** (200 trees, max depth 6,
  `class_weight="balanced"`) — a more flexible ensemble model, evaluated to
  test whether added complexity improves performance meaningfully enough to
  justify reduced interpretability.

Full development process and results: `notebooks/03_model_development.ipynb`.

## 5. Known Limitations

- **Synthetic data:** All patterns are simplified, rule-based approximations
  of real KYC risk factors. Real customer behaviour is far more complex and
  noisy.
- **Small minority class:** Only 30 "High" risk examples exist in the full
  dataset (~21 in training after the train/test split). Validation confirmed
  this leads to a meaningful generalisation gap for this class — see
  `model_validation_report.md`.
- **Residual feature-label relationship:** Although random noise was added
  to the label to prevent the label being a perfect deterministic function
  of model inputs, the top predictive features (jurisdiction risk, country
  risk) remain closely related to the label's construction. In a real
  deployment, feature-label relationships would need to be validated
  independently against genuine outcome data (e.g. confirmed SAR filings,
  investigation outcomes) rather than assumed from feature design.
- **No temporal validation:** All data is static/simulated at a single point
  in time. A production model would require validation against
  out-of-time data to check for temporal drift.

## 6. Governance References

This documentation structure is informed by established model risk management principles, 
including the Federal Reserve's Revised Guidance on Model Risk Management (SR 26-2, 2026), 
particularly its emphasis on sound model development and use, validation and monitoring, 
documentation, and governance and controls. 
The project also draws conceptually on European banking expectations concerning data quality,
model explainability, lifecycle governance, and clear accountability.
These principles have been adapted proportionately for a demonstration project and
should not be interpreted as evidence of regulatory compliance or production readiness.
