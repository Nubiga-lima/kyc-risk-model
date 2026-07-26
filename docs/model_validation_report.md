# Model Validation Report — KYC Risk Classification Model

## 1. Scope

This report documents the validation activities performed on the two candidate
models developed in `notebooks/03_model_development.ipynb`: a baseline Logistic 
Regression model and a challenger Random Forest model.

Validation was performed in `notebooks/04_model_validation.ipynb` and 
focuses on model stability, threshold sensitivity, generalisation,
minority-class performance, and feature importance stability.

## 2. Cross-Validation (Stability)

Five-fold stratified cross-validation was performed on the full
dataset using macro-averaged F1 score. Macro F1 was selected rather 
than overall accuracy because it assigns equal importance to each risk 
class and is therefore more informative given the severe class imbalance.

| Model               | Mean Macro F1 | Std. Dev. |
| ------------------- | ------------: | --------: |
| Logistic Regression |         0.491 |     0.012 |
| Random Forest       |         0.528 |     0.036 |

**Finding:** Random Forest achieves a moderately higher average macro F1 score
but with approximately three times the standard deviation across folds.
This indicates that its estimated performance is more sensitive to the 
particular data split, while Logistic Regression demonstrates more 
consistent performance across folds.

In a regulated risk context, this stability is an important consideration 
alongside aggregate predictive performance, particularly where model 
behaviour needs to remain understandable and reliable.

## 3. Threshold Sensitivity (High-Risk Class)

Precision and recall for the High-risk class were examined across 
a range of probability decision thresholds for the Random Forest model.

**Finding:** No evaluated threshold achieves both high precision and 
high recall simultaneously. This reflects the severe class imbalance and 
extremely limited number of High-risk observations. At lower thresholds, 
recall approaches 1.0 while precision remains low (approximately 0.05–0.15).

The model can therefore identify a larger proportion of true High-risk cases 
only at the cost of generating substantially more false positives requiring further review.

**Recommendation:** In a real deployment, the classification threshold should be
treated as a deliberate business and risk-management decision rather than relying
automatically on a default statistical cutoff. Where failing to identify genuinely
High-risk customers carries significant consequences, a lower threshold favouring 
recall may be appropriate, subject to the organisation's risk appetite and operational 
capacity for manual review.

## 4. Train vs. Test Consistency (Overfitting Check)

Class-specific recall for the Random Forest model was compared between the training 
set used during model fitting and the held-out test set.

| Class  | Train Recall | Test Recall |
| ------ | -----------: | ----------: |
| Low    |       ~96.6% |      ~96.6% |
| Medium |       ~42.4% |      ~37.4% |
| High   |         100% |      ~11.1% |

**Finding:** The Low-risk class generalises consistently from training to test data, 
while Medium-class performance shows a moderate decline. The High-risk class exhibits
a substantial generalisation gap, falling from 100% recall on the training sample to 
approximately 11.1% on unseen test observations.

This result is consistent with the extremely limited number of High-risk training 
examples (~21 observations) and indicates that the model's ability to reliably 
identify previously unseen High-risk cases is currently uncertain.

**Recommendation:** The model should not be relied upon in isolation to identify
High-risk customers. Before any production consideration, further development would 
require substantially more representative labelled data and evaluation of complementary
controls, such as transparent rule-based safeguards for established high-risk indicators 
and human-in-the-loop review of relevant cases.

## 5. Feature Importance Stability

Random Forest feature importances were compared across the five cross-validation folds. 
This approach assesses whether the model consistently relies on similar predictors without
using the held-out test set for model fitting.

**Finding:** The leading predictive features — `high_risk_jurisdiction` 
(mean importance 0.271, standard deviation 0.013), `country_risk_Low` (0.139, standard deviation 0.014),
and `media_pep_combo` (0.091, standard deviation 0.005) — demonstrate relatively low variation in importance across folds.

This suggests that the Random Forest's reliance on these features is reasonably stable across different 
training samples rather than being an artefact of a single data split.

Given that the target variable is synthetically constructed partly from related risk indicators, 
these feature-importance results should not be interpreted as empirical evidence that the same
relationships would hold in real KYC customer data.

## 6. Overall Assessment

| Aspect                       | Assessment                                                                                                 |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Cross-validation stability   | Logistic Regression more stable than Random Forest                                                         |
| Majority-class performance   | Strongest for Low-risk class                                                                               |
| Medium-class performance     | Moderate and weaker than Low-risk performance                                                              |
| High-risk performance        | Weak generalisation in both models; Logistic Regression provides stronger recall                           |
| Feature importance stability | Reasonably stable across folds for Random Forest                                                           |
| Overfitting risk             | Limited for Low class; some deterioration for Medium; substantial for High due to very limited sample size |
| Interpretability             | Logistic Regression offers greater inherent transparency                                                   |
| Production readiness         | Neither model is production-ready                                                                          |

## 7. Model Selection Recommendation

Despite the Random Forest achieving a moderately higher average cross-validation Macro F1 score (0.528 versus 0.491),
**Logistic Regression is recommended as the preferred model for this demonstration use case**.

The recommendation is based on several considerations:

* greater performance stability across cross-validation folds;
* stronger recall for the critical High-risk class;
* greater inherent interpretability;
* lower model complexity; and
* easier explanation and challenge of model behaviour.

This reflects a risk-sensitive model-selection approach in which stability, interpretability, and 
performance on the class of greatest business concern are considered alongside aggregate predictive performance. 
A marginal improvement in an overall performance metric does not automatically justify additional model complexity, 
particularly when the more complex model demonstrates greater performance variability.

## 8. Limitations and Next Steps

Both candidate models share the same fundamental limitation: unreliable generalisation for 
the High-risk class due to the very limited number of synthetic High-risk observations (~21 records in the training sample).

The entire project is based on synthetic data and simplified risk relationships. 
Consequently, neither model should be interpreted as a production-ready KYC risk model 
or as evidence of expected performance on real customer data.

Before any real-world deployment, further work would require:

* a substantially larger and more representative labelled dataset;
* independent validation against genuine customer outcomes;
* out-of-time validation to assess temporal stability and model drift;
* further analysis of model calibration and decision thresholds;
* assessment of data quality, bias, and relevant regulatory considerations;
* ongoing performance and drift monitoring;
* appropriate rule-based controls where required; and
* clearly defined human review and escalation procedures.

The purpose of this project is therefore not to propose a deployable KYC model, 
but to demonstrate an end-to-end approach to model development, validation, 
documentation, explainability, and risk-aware model selection within a simplified analytical setting.
