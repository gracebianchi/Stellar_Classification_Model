# Stellar Classification Model

PSTAT 231 — Final Project | Grace Bianchi | UC Santa Barbara

Overview

This project builds a machine learning pipeline to classify stellar objects as stars, galaxies, or quasars (QSOs) using photometric and spectroscopic data from the Sloan Digital Sky Survey (SDSS). The central research question is whether photometric filter measurements alone are sufficient for accurate classification, or whether redshift — a more resource-intensive spectroscopic measurement — is truly necessary.

Data

The dataset is the Stellar Classification Dataset - SDSS17 from Kaggle, containing 100,000 observations. Six predictors are used: five photometric filters (u, g, r, i, z) and redshift. A random sample of 5,000 observations was drawn for modeling.

Models

Seven classification models were fit and tuned using 10-fold cross-validation, each on two recipes — one including redshift (full) and one without (reduced):

K-Nearest Neighbors, Linear Discriminant Analysis, Quadratic Discriminant Analysis, Elastic Net, Decision Tree, Random Forest, Boosted Tree

Key Findings

With redshift, Random Forest achieves 0.9959 ROC AUC and 97.9% accuracy on the test set

Without redshift, performance drops to 0.9307 ROC AUC and 84.1% accuracy

STAR is the most affected class — nearly 39% misclassified without redshift, versus zero with it

QSOs retain a distinctive photometric profile even without redshift, suggesting spectroscopic follow-up may only be necessary for ambiguous low-redshift objects

Files

Pstat_231_Final_Bianchi.Rmd | Main report with EDA, results, and narration

Pstat_231_Final_Bianchi.html | Knitted HTML report

final_project_models_231.Rmd | Model training and tuning code

star_classification.csv | Raw SDSS dataset

stellar_codebook.txt | Variable descriptions

model_results.rda | Saved tuning results

workflows.rda | Saved model workflows

Tools

R · tidymodels · tidyverse · corrplot · ggthemes · kableExtra · dplyr · discrim · kknn · themis
