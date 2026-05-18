# Predicting Country of Residence from Value Surveys: CART vs. Random Forest

**Language:** R · **Methods:** Decision trees (CART), Random Forest, cross-validation, leakage-safe preprocessing, model comparison
**Context:** Graduate coursework project (NYU, multivariate methods), 2026

## Question

Can responses to attitude and value survey items predict which country a respondent lives in? Using the World Values Survey (Wave 7, 2021), I built and compared two tree-based classifiers to distinguish respondents from the USA, Brazil, and Kenya — three countries chosen for their contrasting cultural, political, and economic contexts.

## Approach

The analysis proceeds in two phases:

**Phase I — CART.** A baseline classification tree, followed by a deliberately over-grown tree pruned using the 1-SE rule on cross-validated error. Variable importance was compared before and after pruning to verify that pruning removed noise rather than signal.

**Phase II — Random Forest.** An ensemble model on the same data, with `mtry` tuned via out-of-bag error and `ntree` selected by inspecting OOB convergence. Phase II was motivated by a specific weakness identified in Phase I (see below).

**Preprocessing.** Data were split with stratified sampling to preserve class proportions across the three countries. Median imputation for the ordinal Likert items was performed **after** the train/test split, with medians estimated on the training set only and applied to both sets — preventing test-set information from leaking into model training. Imputation was verified to leave zero remaining missing values in both sets.

## Key results

| Model | Accuracy | Kappa | Brazil sensitivity |
|---|---|---|---|
| Baseline CART | 73.2% | 0.56 | 53.4% |
| Pruned CART | 76.6% | 0.62 | 59.5% |
| Tuned Random Forest | 85.2% | 0.76 | 73.7% |

The most informative result was not the headline accuracy but *where* the models failed. Brazilian respondents were consistently the hardest to classify, because their value profile overlaps with both other countries. Random Forest's largest gain was precisely on this ambiguous boundary (+14 points for Brazil over pruned CART), consistent with the theoretical expectation that ensemble variance reduction helps most where class boundaries are unclear. Across both methods, political participation, education, and religiosity emerged as the strongest cultural discriminators.

## Files

- `cart-rf-country-classification.Rmd` — full analysis with commentary
- Data: World Values Survey Wave 7 (2021), **not redistributed here**. Available from [worldvaluessurvey.org](https://www.worldvaluessurvey.org) under their data access terms. Place the data file in the same directory as the `.Rmd` to reproduce.

## Notes

Coursework project, shared to illustrate an applied machine-learning workflow in R: data preparation, leakage-safe imputation, model tuning and pruning, validation, and interpretation of where and why a model fails.
