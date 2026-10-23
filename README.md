# Phishing Website Detection: Random Forest vs Support Vector Machine

A controlled, statistically validated comparison of optimised Random Forest (RF) and
Support Vector Machine (SVM) classifiers for phishing URL detection on the
[PhiUSIIL Phishing URL Dataset](https://archive.ics.uci.edu/dataset/967) (235,795 URLs).
Final-year project, Computer Engineering, University of Uyo.

## What's in this repo

| File | Description |
|------|-------------|
| `Project.ipynb` | Full pipeline: preprocessing, class weighting, grid search, training, evaluation, significance tests, feature importance |
| `Edikan_Inyang_Phishing_detection.pdf` | Full written report (literature review, methodology, results, discussion) |
| `requirements.txt` | Python dependencies |

## Method

- **Data:** 235,795 URLs (134,850 legitimate, 100,945 phishing). Label `1` = legitimate, `0` = phishing.
- **Preprocessing:** dropped 5 identifier/text columns and `URLSimilarityIndex` (a data-leakage feature), leaving 49 features. `StandardScaler` for SVM only, fit on the training split.
- **Imbalance:** cost-sensitive learning (`class_weight="balanced"`).
- **Split:** stratified 80/20 (188,636 train / 47,159 test), `random_state=42`.
- **Tuning:** 5-fold stratified `GridSearchCV` (F1) on the training set.
  RF: `n_estimators` {50, 100, 200} x `max_depth` {10, 20, None}.
  SVM (RBF): `C` {0.1, 1, 10} x `gamma` {scale, auto, 0.01}.
- **Validation:** McNemar's test on test-set predictions; 10-fold CV with paired t-test and Cohen's d; Gini and permutation feature importance.

## Results (test set)

| Metric | Random Forest | SVM |
|--------|---------------|-----|
| Accuracy | 99.9873% | 99.9512% |
| F1 | 99.9889% | 99.9574% |
| Total errors (of 47,159) | 6 | 23 |
| McNemar (continuity-corrected) | chi2 = 9.48, p = 0.0021 | |
| 10-fold paired t-test | t = 4.83, p = 0.0009, d = 1.53 | |

Both models score near ceiling; the dominant predictors are page-content features
(`LineOfCode`, `NoOfSelfRef`, `NoOfExternalRef`, `NoOfImage`) and `IsHTTPS`
(permutation importance). See the report for caveats on how far this generalises.

## Reproducing

```bash
git clone https://github.com/Edikan19/<repo-name>.git
cd <repo-name>
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook Project.ipynb
```

The notebook downloads the dataset automatically via `ucimlrepo` (UCI id 967) if
`PhiUSIIL_Phishing_URL_Dataset.csv` is not present locally. SVM training takes
roughly 20 minutes on a laptop CPU.

## Author

Edikan Inyang: [GitHub](https://github.com/Edikan19)
