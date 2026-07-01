# Phishing Website Detection — Critical Reproduction Study

Final project for **Data Science in Cyber** (Dr. Uri Itai). This repository reproduces
and critically evaluates the GitHub project *"Phishing Website Detection by Machine
Learning Techniques"* by Shreya Gopal Sundari, which serves as the selected
article/tutorial/implementation for this assignment.

## Selected Source

- **Article / tutorial / implementation (GitHub):**
  https://github.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques

The repository's README itself functions as the tutorial: it clearly defines the
phishing-detection problem, proposes a 17-feature / six-classifier solution, includes
full implementation notebooks, and provides the dataset needed to reproduce results.

## Project Description

The source project trains six classifiers (Decision Tree, Random Forest, Multilayer
Perceptron, XGBoost, an Autoencoder, and SVM) on 17 handcrafted lexical/domain/HTML
features extracted from 10,000 URLs (5,000 PhishTank phishing URLs, 5,000 University of
New Brunswick benign URLs) to classify a URL as phishing or legitimate, reporting 86.4%
test accuracy with XGBoost.

This reproduction trains five classifiers (Logistic Regression, Decision Tree, Random
Forest, Gradient Boosting, and XGBoost) on the same published feature table, and
stress-tests the dataset and evaluation methodology.

**Central finding:** the dataset contains 5,626 exact full-row duplicates when
considering all columns including Domain. When considering only the engineered feature
vector and label, the 10,000 rows collapse to only 880 unique feature-label patterns.
A standard 80/20 random split therefore places an identical feature+label twin in both
train and test for 95.9% of test rows — inflating the original article's reported
accuracy. Once this leakage is removed, Random Forest's MCC drops from 0.74 to 0.20,
even though plain accuracy (0.86 → 0.77) understates the severity of the drop.

## Links

- **Original article / GitHub repo:** https://github.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques
- **Original feature reference (UCI):** https://archive.ics.uci.edu/ml/datasets/Phishing+Websites
- **PhishTank (phishing URL source):** https://www.phishtank.com/
- **UNB benign URL corpus (legitimate URL source):** https://www.unb.ca/cic/datasets/url-2016.html

## Repository Structure

```
.
├── Phishing_Detection_Critical_Evaluation_Report.pdf    # PDF report
├── phishing_detection_analysis.ipynb                    # Executed analysis notebook
├── requirements.txt                                     # Python dependencies
├── XGBoostClassifier_pickle.dat                         # Original author's pretrained XGBoost model
├── DataFiles/
│   ├── 5.urldata.csv                  # Main dataset: 10,000 rows x 18 cols (features + label)
│   ├── 1.Benign_list_big_final.csv    # Raw legitimate URLs (UNB corpus, 2016)
│   └── 2.online-valid.csv             # Raw phishing URLs (PhishTank export, ~May 2020)
├── figures/
│   ├── fig_class_balance.png          # Class distribution chart
│   ├── fig_corr_heatmap.png           # Pearson correlation matrix
│   ├── fig_metrics.png                # Model comparison across all metrics
│   ├── fig_confusion.png              # Confusion matrices for all models
│   ├── fig_roc.png                    # ROC curves for all models
│   └── fig_importance.png             # Random Forest feature importances
└── README.md
```

## Dataset Source

- **Phishing URLs:** [PhishTank](https://www.phishtank.com/)
- **Legitimate URLs:** [University of New Brunswick URL dataset](https://www.unb.ca/cic/datasets/url-2016.html)
- **Feature table:** pre-extracted by the original author (`DataFiles/5.urldata.csv`)

All three CSVs are included in `DataFiles/` — no download step required to run the
notebook. Note: PhishTank's feed updates hourly, so `2.online-valid.csv` cannot be
re-collected identically today; the committed file is static.

## Installation

```bash
pip install -r requirements.txt
```

To also run XGBoost (the original article's headline model):

```bash
pip install xgboost
```

## Execution Instructions

1. Clone this repository and `cd` into its root folder.
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   pip install xgboost   # optional but recommended — enables the XGBoost section
   ```
3. Launch Jupyter:
   ```bash
   jupyter notebook phishing_detection_analysis.ipynb
   ```
4. Run all cells top to bottom (**Cell → Run All**).  
   The notebook reads `DataFiles/5.urldata.csv` relative to the repo root — no path
   changes needed as long as Jupyter is launched from the repo root.
5. All outputs are already captured in the committed `.ipynb` (68 cells, zero errors).
   The XGBoost cell will additionally execute if `xgboost` is installed.

## Note on XGBoost

The original author's pretrained model (`XGBoostClassifier_pickle.dat`) and exact
hyperparameters (recovered by inspecting the pickle structure: `max_depth=7`,
`learning_rate=0.4`, `n_estimators=100`, `objective=binary:logistic`) are documented in
the notebook. The notebook includes a dedicated XGBoost training cell using these
hyperparameters — install `xgboost` locally and re-run to add XGBoost results to the
model comparison table.

## Author

Individual submission — Data Science in Cyber, Final Project.
