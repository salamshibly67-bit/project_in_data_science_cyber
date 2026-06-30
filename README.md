# Phishing Website Detection — Critical Reproduction Study

Final project for **Data Science in Cyber** (Dr. Uri Itai). This repository reproduces
and critically evaluates the GitHub project *"Phishing Website Detection by Machine
Learning Techniques"* by Shreya Gopal Sundari, which serves as the selected
article/tutorial/implementation for this assignment.

## Selected Source

- **Article / tutorial / implementation (GitHub):**
  https://github.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques
- The repository's README itself functions as the tutorial: it clearly defines the
  phishing-detection problem, proposes a 17-feature / six-classifier solution, includes
  full implementation notebooks, and provides the dataset needed to reproduce results —
  satisfying the assignment's source-selection requirements without needing a separate
  non-GitHub article.

## Project Description

The source project trains six classifiers (Decision Tree, Random Forest, Multilayer
Perceptron, XGBoost, an Autoencoder, and SVM) on 17 handcrafted lexical/domain/HTML
features extracted from 10,000 URLs (5,000 PhishTank phishing URLs, 5,000 University of
New Brunswick benign URLs) to classify a URL as phishing or legitimate, reporting 86.4%
test accuracy with XGBoost.

This reproduction trains four classifiers (Logistic Regression, Decision Tree, Random
Forest, Gradient Boosting — see the XGBoost note below) on the same published feature
table, and goes further to **stress-test the dataset and evaluation methodology**.

**Central finding:** the dataset contains 5,626 exact full-row duplicates when
considering all columns, including Domain. In addition, when considering only the
engineered feature vector and label, the 10,000 rows collapse to only 880 unique
feature-label patterns. This shows that the coarse binary feature representation creates
many repeated patterns, which makes a random train/test split overly optimistic: when we
replicated the original article's plain 80/20 split, 95.9% of test rows turned out to
have an identical feature+label twin already in the training set. Once that leakage is
removed, Random Forest's Matthews Correlation Coefficient drops from 0.74 to 0.20, even
though plain accuracy alone (0.86 → 0.77) understates the severity of the drop. Full
details, including the formal distinction between full-row, feature+label, feature-only,
and domain-only duplicates, are in Sections 2–5 of the report.

## Links

- **Original article / GitHub repo:** https://github.com/shreyagopal/Phishing-Website-Detection-by-Machine-Learning-Techniques
- **Original feature reference (UCI):** https://archive.ics.uci.edu/ml/datasets/Phishing+Websites
- **PhishTank (phishing URL source):** https://www.phishtank.com/
- **UNB benign URL corpus (legitimate URL source):** https://www.unb.ca/cic/datasets/url-2016.html

## Repository / Submission Structure

```
.
├── Phishing_Detection_Critical_Evaluation_Report.pdf    # PDF report (required submission format)
├── Phishing_Detection_Critical_Evaluation_Report.docx   # Source Word version (optional, same content)
├── phishing_detection_analysis.ipynb                    # Executed, documented analysis notebook
├── requirements.txt                                     # Exact Python dependencies
├── DataFiles/
│   ├── 5.urldata.csv                  # Final feature table (10,000 rows x 18 cols) — primary dataset
│   ├── 1.Benign_list_big_final.csv    # Raw legitimate URL list (UNB, 2016)
│   └── 2.online-valid.csv             # Raw PhishTank export (phishing URLs, ~May 2020)
├── XGBoostClassifier_pickle.dat       # Original author's pretrained XGBoost model (see XGBoost note)
├── figures/                           # Exported figures used in the report
└── README.md
```

## Dataset Source

- **Phishing URLs:** [PhishTank](https://www.phishtank.com/) — crowd-verified, hourly-updated feed.
- **Legitimate URLs:** [University of New Brunswick URL dataset](https://www.unb.ca/cic/datasets/url-2016.html).
- **Feature table:** pre-extracted by the original author and committed to their repository
  as `5.urldata.csv` (17 engineered features + label, referenced against the
  [UCI Phishing Websites](https://archive.ics.uci.edu/ml/datasets/Phishing+Websites) feature definitions).

All three CSVs are included directly in `DataFiles/` in this submission, so no download
step is required to run the notebook. (Note: PhishTank's feed updates hourly, so the raw
phishing URL list cannot be re-collected identically today if you wanted to regenerate
`2.online-valid.csv` from scratch — this is discussed as a reproducibility limitation in
Section 4 of the report. The committed CSV itself is static and will not change.)

## Installation

```bash
pip install -r requirements.txt
```

See `requirements.txt` for exact packages (pandas, numpy, scikit-learn, matplotlib,
seaborn, scipy, jupyter). XGBoost is optional — see note below.

## Execution Instructions

1. Clone this repository (or unzip this submission) and `cd` into its root folder.
2. Install dependencies: `pip install -r requirements.txt`
3. Launch Jupyter and open the notebook:
   ```bash
   jupyter notebook phishing_detection_analysis.ipynb
   ```
4. Run all cells top to bottom (Cell → Run All). The notebook reads
   `DataFiles/5.urldata.csv` via a path relative to the repository root, so **no path
   changes are needed** as long as you run Jupyter from the repo root — this matches the
   folder structure shown above.
5. The notebook is fully self-contained: it produces every table and figure included in
   the report from scratch, with a fixed random seed (`RANDOM_SEED = 42`) for
   reproducibility. We executed it end-to-end ourselves with zero errors before
   submission (68 cells, all outputs captured in the committed `.ipynb`).

### Note on XGBoost

The original article's best-performing model was XGBoost (86.4% accuracy), and the
author's own pretrained model file (`XGBoostClassifier_pickle.dat`) is included in this
submission for reference. The `xgboost` Python package was not installable in the
offline sandbox used to build this submission (no internet access), so we could not
train or run XGBoost directly here — we use scikit-learn's `GradientBoostingClassifier`
as the closest available substitute and say so explicitly throughout the report,
rather than presenting it as "the XGBoost result."

We did, however, extract the original model's exact hyperparameters and feature names by
inspecting the pickle file's structure directly (without needing to fully deserialize
it) — see the "Note on XGBoost" subsection in the notebook for details. If you have
`xgboost` installed locally, install it (`pip install xgboost`) and uncomment the
XGBoost training block in the notebook's Model Training section to train it directly
using the original author's exact hyperparameters and compare it against our other four
models on the same leakage-controlled split.

## Author

Individual submission — Data Science in Cyber, Final Project.
