# Stellar Object Classification: Does Redshift Matter?

An end-to-end multiclass machine learning project in R that classifies astronomical objects as **stars, galaxies, or quasars (QSOs)** using data from the Sloan Digital Sky Survey.

The central research question is whether five photometric measurements alone can accurately classify stellar objects or whether adding redshift—a more resource-intensive spectroscopic measurement—meaningfully improves performance.

Seven classification algorithms were trained and evaluated under both feature sets. The best model, Random Forest with redshift, achieved a **0.9959 multiclass ROC AUC** and **97.9% accuracy** on a held-out test set.

**[View the complete HTML report](file:///Users/gracebianchi/Desktop/3rd%20year/Spring%202026/PSTAT%20231/final_project/Pstat_231_Final_Bianchi.html)** | **[View the report source](./Pstat_231_Final_Bianchi.Rmd)** | **[View the training pipeline](./final_project_models_231.Rmd)**

**Academic context:** Developed as the final project for UCSB’s graduate-level PSTAT 231: Statistical Machine Learning, which I received approval to take as an undergraduate. The project earned a score of 100%.

## Research Question

Large astronomical surveys can collect photometric measurements more efficiently than spectroscopic redshift measurements. This project tests whether redshift is necessary for accurate classification by comparing two feature configurations:

| Feature Set       | Predictors                                                      |
| ----------------- | --------------------------------------------------------------- |
| **Full model**    | Five photometric filters (`u`, `g`, `r`, `i`, `z`) and redshift |
| **Reduced model** | Five photometric filters only (`u`, `g`, `r`, `i`, `z`)         |

Each configuration was evaluated across the same seven classification algorithms, cross-validation folds, and held-out test observations.

## Dataset

The original dataset contains **100,000 observations** from the Sloan Digital Sky Survey Data Release 17. Each observation represents one astronomical object classified as:

* **GALAXY:** A gravitationally bound system of stars, gas, and dust
* **STAR:** A luminous astronomical object within a galaxy
* **QSO:** A quasar, or highly luminous active galactic nucleus

After removing placeholder values, the cleaned dataset contained **99,989 observations**:

| Class  | Observations | Proportion |
| ------ | -----------: | ---------: |
| GALAXY |       59,445 |     59.45% |
| STAR   |       21,593 |     21.60% |
| QSO    |       18,961 |     18.96% |

A reproducible random sample of 5,000 observations was selected for exploratory analysis and modeling.

## Data Preparation

The preprocessing workflow was designed to prevent data leakage and support fair model comparison:

1. Retained the five photometric filters, redshift, and target class.
2. Removed observations containing the SDSS missing-value placeholder `-9999`.
3. Excluded administrative and observational identifiers that do not describe the physical object.
4. Excluded `spec_obj_ID` because repeated IDs are associated with the same class and could introduce target leakage.
5. Excluded right ascension and declination, which describe telescope pointing coordinates rather than intrinsic object characteristics.
6. Converted the response variable to a factor.
7. Drew a reproducible sample of 5,000 observations.
8. Created a stratified 80/20 training-test split.
9. Normalized predictors within each modeling recipe.
10. Created stratified 10-fold cross-validation folds using only the training data.

The resulting split contained approximately 4,000 training observations and 1,001 held-out test observations.

## Models

Seven classification algorithms were evaluated:

* K-Nearest Neighbors
* Linear Discriminant Analysis
* Quadratic Discriminant Analysis
* Multinomial Elastic Net
* Decision Tree
* Random Forest
* Boosted Tree

Each model was fit twice—once with redshift and once without—for a total of **14 modeling workflows**.

KNN, Elastic Net, Decision Tree, Random Forest, and Boosted Tree were tuned through grid search. LDA and QDA were evaluated directly across the cross-validation folds.

## Cross-Validation Results

Models were ranked using multiclass ROC AUC averaged across 10 stratified folds.

| Model         | With Redshift | Without Redshift |  Change |
| ------------- | ------------: | ---------------: | ------: |
| Random Forest |        0.9953 |           0.9279 | -0.0674 |
| Boosted Tree  |        0.9940 |           0.9187 | -0.0753 |
| Elastic Net   |        0.9920 |           0.8307 | -0.1613 |
| Decision Tree |        0.9909 |           0.8764 | -0.1145 |
| QDA           |        0.9869 |           0.8617 | -0.1252 |
| KNN           |        0.9832 |           0.9162 | -0.0670 |
| LDA           |        0.9371 |           0.8180 | -0.1191 |

Every algorithm performed better when redshift was included. Random Forest and Boosted Tree produced the highest cross-validation ROC AUC values under both feature configurations and were selected for final evaluation.

## Held-Out Test Performance

| Model         | Feature Set     |    ROC AUC |   Accuracy |
| ------------- | --------------- | ---------: | ---------: |
| Random Forest | With redshift   | **0.9959** | **97.90%** |
| Boosted Tree  | With redshift   |     0.9937 |     97.20% |
| Random Forest | Photometry only |     0.9307 |     84.12% |
| Boosted Tree  | Photometry only |     0.9268 |     83.62% |

Removing redshift reduced Random Forest accuracy by approximately **13.8 percentage points**, from 97.9% to 84.1%.

The photometry-only models retained meaningful discriminative power, but neither ensemble method recovered the performance achieved when redshift was available.

## Class-Level Findings

The confusion matrices showed that removing redshift did not affect every class equally.

### With Redshift

The full Random Forest model misclassified only **21 of 1,001 test observations**:

* 12 QSOs were classified as galaxies
* 9 galaxies were classified as QSOs
* All 220 stars were classified correctly

### Without Redshift

The reduced Random Forest model struggled most with the STAR class:

* 58 stars were classified as galaxies
* 27 stars were classified as QSOs
* 85 of 220 stars were misclassified overall

The STAR misclassification rate therefore increased from **0% to approximately 38.6%** when redshift was removed.

QSOs were the most robust class under the reduced feature set, suggesting that quasars retain a comparatively distinctive photometric profile even without spectroscopic redshift information.

## Key Conclusion

Redshift provides substantial predictive information for stellar classification, particularly when distinguishing stars from extragalactic objects.

Photometric filters alone produced a respectable Random Forest ROC AUC of 0.9307, showing that brightness measurements across wavelength bands contain meaningful class information. However, adding redshift increased test accuracy from 84.1% to 97.9% and eliminated STAR classification errors in the held-out sample.

These findings suggest a potential two-stage classification strategy:

1. Use lower-cost photometric measurements for initial screening.
2. Prioritize spectroscopic follow-up for observations whose classifications remain ambiguous without redshift.

Further research would be required to validate such a strategy across different surveys and redshift ranges.

## Modeling Workflow

1. Cleaned the raw SDSS data and removed leakage-prone identifiers.
2. Explored predictor distributions and class-level differences.
3. Created full and reduced preprocessing recipes.
4. Constructed a stratified training-test split.
5. Generated stratified 10-fold cross-validation folds.
6. Defined and tuned seven classification algorithms.
7. Ranked models using multiclass ROC AUC.
8. Selected Random Forest and Boosted Tree as the leading models.
9. Finalized each workflow using its optimal hyperparameters.
10. Evaluated the four final models on the untouched test set.
11. Compared ROC AUC, accuracy, ROC curves, and confusion matrices.
12. Analyzed the class-specific impact of removing redshift.

## Tools and Skills

* **R:** data analysis and machine learning
* **tidymodels:** recipes, workflows, resampling, tuning, and evaluation
* **tidyverse / dplyr:** data cleaning and transformation
* **ranger:** Random Forest classification
* **xgboost:** gradient-boosted tree classification
* **glmnet:** multinomial Elastic Net
* **kknn:** K-Nearest Neighbors
* **discrim / MASS:** Linear and Quadratic Discriminant Analysis
* **rpart:** Decision Tree classification
* **ggplot2 / ggthemes:** exploratory and model-performance visualizations
* **corrplot:** predictor correlation analysis
* **kableExtra:** formatted model-comparison tables
* **R Markdown:** reproducible reporting

## Repository Structure

| File                                                             | Description                                                                    |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| [`Pstat_231_Final_Bianchi.Rmd`](./Pstat_231_Final_Bianchi.Rmd)   | Main report containing the research question, EDA, evaluation, and conclusions |
| [`Pstat_231_Final_Bianchi.html`](./Pstat_231_Final_Bianchi.html) | Rendered interactive HTML report with code, tables, and visualizations         |
| [`final_project_models_231.Rmd`](./final_project_models_231.Rmd) | Model specification, tuning, and cross-validation pipeline                     |
| [`star_classification.csv`](./star_classification.csv)           | Raw SDSS17 stellar-classification dataset                                      |
| [`stellar_codebook.txt`](./stellar_codebook.txt)                 | Definitions for the original dataset variables                                 |
| [`model_results.rda`](./model_results.rda)                       | Saved cross-validation and tuning results                                      |
| [`workflows.rda`](./workflows.rda)                               | Saved Random Forest and Boosted Tree workflows                                 |
| `image2.png`, `image3.png`, `image4.png`, `vashon.png`           | Images used in the rendered report                                             |

## Data Source

The project uses the [Stellar Classification Dataset - SDSS17](https://www.kaggle.com/datasets/fedesoriano/stellar-classification-dataset-sdss17/data), containing observations collected through the Sloan Digital Sky Survey.

## Limitations

* Only 5,000 of the approximately 100,000 available observations were used for modeling because of computational constraints.
* Performance was evaluated using one stratified train-test split; repeated cross-validation or external validation would provide a stronger estimate of generalization.
* The class distribution is moderately imbalanced. Multiclass ROC AUC and accuracy were reported, but macro F1, balanced accuracy, and class-specific recall would provide additional perspective.
* Results may not generalize directly to other telescope systems, survey pipelines, or object populations.
* The reduced model excludes measured redshift entirely; it does not evaluate photometric-redshift estimates that could provide a lower-cost approximation.
* The experiment measures the predictive contribution of redshift but does not account for the observational cost of acquiring it.
* The proposed spectroscopic-triage strategy is an interpretation of the results and would require prospective validation before operational use.

## Potential Extensions

* Train and tune models using the full cleaned dataset.
* Report macro F1, balanced accuracy, precision, and recall by class.
* Evaluate probability calibration and uncertainty-based abstention.
* Analyze photometry-only performance across different redshift ranges.
* Use feature importance or SHAP values to explain model decisions.
* Build a selective-classification system that requests spectroscopic follow-up only for uncertain observations.

