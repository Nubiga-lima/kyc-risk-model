# KYC Risk Model — Synthetic Demonstration Project

A demonstration project illustrating model development, validation,
documentation, explainability, and governance considerations relevant to
KYC (Know Your Customer) and financial crime risk modelling in a regulated
banking context.

**This project uses fully synthetic data.** It was developed to demonstrate
technical competency in model development, documentation, validation, and
explainability. It is not intended to represent a production-ready KYC
model or a claim of professional financial-crime modelling experience.

## Why this project exists

This project provides an applied demonstration of the model lifecycle,
covering data preparation, model development, documentation, validation,
explainability, and model-selection considerations.

The synthetic dataset incorporates features conceptually related to common
KYC risk factors, including jurisdiction risk, PEP status, adverse-media
indicators, and transaction behaviour.

## Project Structure

kyc-risk-model/
│
├── notebooks/
│   ├── 01_data_generation.ipynb
│   ├── 02_feature_engineering.ipynb
│   ├── 03_model_development.ipynb
│   └── 04_model_validation.ipynb
│
├── docs/
│   ├── model_documentation.md
│   └── model_validation_report.md
│
├── data/
│   ├── raw/
│   └── processed/
│
├── requirements.txt
├── README.md
└── .gitignore

## What This Project Covers

- **Synthetic data generation** with deliberate class imbalance and label
  noise to create a non-trivial classification problem.

- **Feature engineering** incorporating KYC/financial-crime-related concepts,
  including jurisdiction risk, PEP/adverse-media interactions, and
  transaction behaviour.

- **Baseline vs. challenger modelling:** Logistic Regression and Random
  Forest models are compared using performance, stability, complexity,
  and interpretability considerations rather than headline accuracy alone.

- **Explainability:** SHAP analysis is used to examine which features
  influence model predictions and in what direction.

- **Model validation:** Five-fold stratified cross-validation, threshold
  sensitivity analysis, train/test consistency checks, minority-class
  performance analysis, and feature-importance stability are used to
  illustrate several considerations relevant to model validation.

- **Governance-oriented documentation:** Structured model documentation and
  a separate validation report record model purpose, methodology,
  assumptions, limitations, validation findings, and model-selection
  rationale.

- **Reproducibility and version control:** Code, notebooks, documentation,
  dependencies, and project history are maintained using Git and GitHub.

## An Important Model-Development Finding

An early version of the synthetic target was constructed as a deterministic
function of model input features. This produced suspiciously perfect
classification performance.

Rather than treating the result as evidence of a highly successful model,
the target-generation process was investigated and the issue identified.
Random noise was subsequently introduced into the synthetic label-generation
process to create a less deterministic modelling problem.

This illustrates an important validation principle: unusually strong model
performance can itself warrant investigation, particularly where target
construction or feature design may inadvertently make the prediction task
trivial.

## Model Selection Outcome

The Random Forest achieved a moderately higher average cross-validation
Macro F1 score (0.528 versus 0.491 for Logistic Regression). However,
Logistic Regression demonstrated greater stability across folds, stronger
recall for the rare High-risk class, greater inherent interpretability,
and lower model complexity.

**Logistic Regression was therefore selected as the preferred model for
this demonstration use case.**

The decision illustrates a risk-sensitive model-selection approach in which
stability, interpretability, minority-class performance, and complexity are
considered alongside aggregate predictive performance.

Full analysis is available in `docs/model_validation_report.md`.

## Known Limitations

- All observations are synthetic and represent simplified assumptions rather
  than real customer behaviour.

- The High-risk class is deliberately rare, with only 30 of 5,000 observations.
  This produces a substantial generalisation challenge, particularly for the
  High-risk class.

- The synthetic target is constructed partly from risk indicators also
  represented in the model features. Results therefore should not be
  interpreted as evidence that these relationships would hold in real KYC
  customer data.

- No temporal or out-of-time validation has been performed because the
  dataset represents a static simulated population.

- The project does not address all regulatory, operational, fairness,
  data-governance, or human-review requirements that would be necessary for
  a production KYC model.

Neither candidate model should therefore be considered production-ready.

Further details are provided in:

- `docs/model_documentation.md`
- `docs/model_validation_report.md`

## Governance Context

The project's documentation and validation approach is informed by general
model risk management principles, including the Federal Reserve's Revised
Guidance on Model Risk Management (SR 26-2, 2026), particularly its emphasis
on model development and use, validation and monitoring, documentation, and
governance and controls.

These principles have been adapted proportionately for a demonstration
project and should not be interpreted as evidence of regulatory compliance.

## How to Run This Project

1. Clone the repository:

   git clone https://github.com/Nubiga-lima/kyc-risk-model.git

2. Install dependencies:

   pip install -r requirements.txt

3. Run the notebooks sequentially:

   01_data_generation.ipynb
   02_feature_engineering.ipynb
   03_model_development.ipynb
   04_model_validation.ipynb

Each notebook reads the outputs generated by the preceding stage where
applicable.

## Author

**Roland Lima**

Quantitative finance professional with a background in financial
econometrics, machine learning, banking analytics, and computational
modelling.
