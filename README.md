# bank-ai-prediction-study
Can We Trust AI Predictions in Banking?

A preliminary data analytics and machine learning study on the UCI Bank Marketing dataset.

The project asks whether machine-learning predictions of telemarketing outcomes at a bank can be trusted in practice, and in particular whether a model that works well on historical data stays reliable when economic conditions change.

What this repository contains
File	Description
Codes - Copy.ipynb	The full analysis in one notebook: data exploration, preparation, three models, evaluation, the time-based experiment and the figures
requirements.txt	Python packages needed to run the notebook
README.md	This file

The dataset itself is not included. Download it from the UCI repository (see below).

Summary of findings
The target is imbalanced: 11.3% of customers opened a term deposit, so accuracy alone is misleading (always answering "no" gives 88.7%).
Under a random split, Logistic Regression, Decision Tree and Random Forest all reach a similar ROC-AUC (0.795 to 0.811). At the default 0.5 threshold, Recall for the "yes" class is only 0.20 to 0.25.
The variable duration (call length) is known only after the call. Adding it raises Logistic Regression AUC from 0.801 to 0.942, which shows how a leaking variable makes a model look better than it is. It is excluded from the main models.
All models rely mainly on macroeconomic indicators (nr.employed, euribor3m, emp.var.rate).
When the models are trained on the earlier 80% of the rows and tested on the later 20%, ROC-AUC falls from about 0.80 to 0.75, and Random Forest predicts "yes" for nobody at threshold 0.5 (a loss of probability calibration). The best threshold moves from 0.2 to 0.1.

Key numbers:

Model	AUC, random split	AUC, time-based split
Logistic Regression	0.801	0.748
Decision Tree (depth 6)	0.795	not tested
Random Forest (200 trees, min_samples_leaf=50)	0.811	0.748
Data

Dataset: Bank Marketing, UCI Machine Learning Repository https://archive.ics.uci.edu/dataset/222/bank+marketing

This project uses the file bank-additional-full.csv (41,188 rows, 20 input variables and the target y, in chronological order, 2008 to 2010).

Citation requested by the dataset authors: S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.

How to reproduce the results
Option 1: run locally
Clone this repository and open its folder.
Download the dataset from the link above, unzip bank-additional.zip and copy bank-additional-full.csv into the same folder as the notebook.
Create an environment with Python 3.12 or newer and install the packages:
bash
   pip install -r requirements.txt
Start Jupyter and open the notebook:
bash
   jupyter notebook
Run all cells from top to bottom (Run All).
Option 2: run in Google Colab
Open the notebook in Colab (replace YOUR_USERNAME and YOUR_REPO): Open In Colab
In the left panel, choose Files, then upload bank-additional-full.csv.
Choose Runtime > Run all.
Notes for reproducing
random_state=42 is set in every split and model, so the results repeat exactly.
Run the cells in order. An early exploration cell creates a helper column y_num (the 0/1 version of the target), and the data-preparation cell removes it from the inputs. Running the preparation cell first causes an error.
The notebook uses an f-string with nested quotes, which needs Python 3.12 or newer.
Random Forest training (200 trees, several settings) takes a few minutes in total.
Notebook structure
Part	Content
1. Exploration	Shape, types, target distribution, missing values (unknown), duplicates, pdays = 999, outliers (IQR rule), relationship of job, month and poutcome with the target, duration check
2. Preparation	Target to 0/1, duration removed, previously_contacted flag, unknown kept as a category, duplicates and outliers kept
3. Split and preprocessing	Stratified 80/20 split; one-hot encoding and standardisation fitted on the training data only
4. Logistic Regression	Fit, confusion matrix, Accuracy, Precision, Recall, F1, ROC-AUC, threshold experiment, coefficients
5. Model with duration	Same model with duration added, for the leakage comparison
6. Decision Tree	Overfitting check, depth search, metrics, importances and rules
7. Random Forest	Overfitting check, min_samples_leaf search, metrics, threshold experiment, importances
8. Time-based experiment	Chronological 80/20 split, Logistic Regression and Random Forest, threshold table
9. Figures	Target distribution, ROC curves, interest rate and share of "yes" over time, AUC comparison
Limitations
Hyperparameters (tree depth, forest leaf size) and thresholds were compared on the test data, so the reported numbers are slightly optimistic. A separate validation set or cross-validation would be more rigorous.
The data come from one Portuguese bank during 2008 to 2010, so results may not transfer to other banks or periods.
The time-based split is an extreme case (the share of "yes" rises from 6.4% to 30.8% and the interest-rate ranges barely overlap), and only one cut point was used.
Coefficients and feature importances describe association, not cause, and the economic variables are strongly correlated with each other.
