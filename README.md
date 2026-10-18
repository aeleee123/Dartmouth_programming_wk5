# Observed Mortality Classification in Primary Biliary Cirrhosis

**Course:** HSE 751 — Programming for Health Data Science  
**Project:** Week 5 Final Project  
**Author:** Aeran Lee

## Project overview

This project develops and compares supervised binary classification models for **observed mortality status during follow-up** among patients with primary biliary cirrhosis (PBC). The analysis uses demographic, clinical, and laboratory variables to explore associations with the outcome and evaluate three classification approaches. It is an educational programming and data-science project, **not a clinically validated mortality prediction tool**.

## Dataset

- **Source:** [UCI Machine Learning Repository — Cirrhosis Patient Survival Prediction](https://archive.ics.uci.edu/dataset/878/cirrhosis+patient+survival+prediction+dataset-1)
- **Input file used in the notebook:** `cirrhosis.csv`
- **Records:** 418 patients
- **Original variables:** 17
- **Outcome source:** `Status`, with categories `D` (death), `C` (censored), and `CL` (censored due to liver transplantation)

The notebook creates `Death_Outcome`:

| Value | Definition |
| --- | --- |
| `1` | Death recorded during follow-up (`Status = D`) |
| `0` | No death recorded before censoring (`Status = C` or `CL`) |

**Interpretation:** `0` does **not** mean confirmed long-term survival. The binary outcome does not account for differences in follow-up time or transplantation as a competing event. Therefore, the models classify **observed death status**, not death within a fixed time horizon.

## Research question

How well can demographic, clinical, and laboratory features distinguish patients with documented death during follow-up from patients without documented death during follow-up in the PBC dataset?

## Analysis workflow

1. **Data input and profiling:** Upload one local dataset in Google Colab; inspect dimensions, variable types, summary statistics, and missingness.
2. **Validation and cleaning:** Check required columns, duplicate patient IDs, valid status/outcome values, and age; convert age from days to years and replace implausible zeros in selected laboratory variables with missing values.
3. **Exploratory analysis:** Summarize distributions and examine relationships using histograms and correlation visualizations, including Spearman and Phi-K analyses.
4. **Inferential statistics:** Conduct multivariable logistic regression (complete-case analysis, with adjusted odds ratios and confidence intervals) and a Mann–Whitney U test comparing bilirubin between outcome groups. The notebook includes LaTeX notation for the Mann–Whitney statistic.
5. **Data splitting and preprocessing:** Use a stratified 80/20 training/test split (`random_state=42`). Within scikit-learn pipelines, impute numerical features using the median and categorical features using the most frequent value; apply a Yeo–Johnson transformation and standardization to numerical features, and one-hot encoding to categorical features.
6. **Supervised learning:** Fit Logistic Regression, Decision Tree, and Random Forest classifiers. The notebook also performs stratified five-fold cross-validation for Logistic Regression.
7. **Evaluation and interpretation:** Compare accuracy, precision, recall, F1-score, ROC-AUC, confusion matrices, and ROC curves; discuss overfitting, missingness, and clinical interpretation.

## Results

The following are the **saved test-set results in the submitted notebook**, not independently reproduced estimates:

| Model | Accuracy | Precision | Recall | F1-score | ROC-AUC |
| --- | ---: | ---: | ---: | ---: | ---: |
| **Logistic Regression** | **0.857** | **0.857** | **0.750** | **0.800** | **0.907** |
| Decision Tree | 0.679 | 0.558 | 0.750 | 0.640 | 0.692 |
| Random Forest | 0.810 | 0.833 | 0.625 | 0.714 | 0.870 |

Logistic Regression had the highest **reported test ROC-AUC**, accuracy, and F1-score. The notebook therefore identifies it as the preferred model in this comparison. **Methodological caveat:** the current comparison uses the held-out test set to select the model; selecting among all three using training-set cross-validation would provide a stronger estimate of model-selection performance.

## How to run

1. Download or clone this repository.
2. Obtain the dataset from the [UCI dataset page](https://archive.ics.uci.edu/dataset/878/cirrhosis+patient+survival+prediction+dataset-1). Follow the repository's license and attribution terms.
3. Open `Wk5_Final_Project.ipynb` in **Google Colab**. If the notebook has a different downloaded filename, open that `.ipynb` file instead.
4. Install any missing packages, including `phik` and `imbalanced-learn`, before running the relevant cells. See **Dependencies** below.
5. In Colab, choose **Runtime → Run all**. When prompted, upload **one** local copy of `cirrhosis.csv`.
6. Review the validation messages, statistical outputs, model comparison table, and ROC curves.

The upload cell supports CSV, TSV, TXT, Excel, JSON, and Parquet file formats, but **the subsequent analysis is tailored to the columns and coding of the UCI cirrhosis dataset**. Uploading an arbitrary dataset will not necessarily work.

## Dependencies

The notebook runs in a Python-based **Google Colab** environment and imports packages including:

- `numpy`, `pandas`, `scipy`
- `matplotlib`, `seaborn`
- `scikit-learn`, `statsmodels`
- `phik`, `imbalanced-learn`
- `openpyxl` for Excel input; `pyarrow` for Parquet input (when those formats are used)

Google Colab also provides the `google.colab` upload interface. To document a reproducible environment, create a `requirements.txt` using the **actual versions in the environment where the notebook successfully runs**. This README does not claim that dependencies have been pinned or tested in a fresh environment.

## Reproducibility and code structure

- The notebook uses `random_state=42` for the train/test split and relevant model and cross-validation operations.
- Numerical and categorical preprocessing is defined with scikit-learn `Pipeline` and `ColumnTransformer` objects; model training fits the preprocessing on training data.
- The notebook includes functions for dataset validation and model evaluation.
- The original dataset must be supplied at runtime using Colab's local upload prompt.
- **Before final submission:** restart the Colab session and run all cells from top to bottom; verify that the final ROC-AUC values agree across the table, curves, and narrative. Save the executed notebook and record package versions.

## Limitations

- **Outcome definition:** Censoring and liver transplantation are grouped as `0`; follow-up time and competing risks are not modeled.
- **Missing data:** Several predictors have substantial missingness. Imputation does not eliminate potential bias, especially when missingness is informative.
- **Sample size and validation:** The dataset contains 418 observations. Performance is based on a single train/test split, with five-fold cross-validation shown for Logistic Regression only; no external validation is reported.
- **Overfitting:** The unconstrained Decision Tree and Random Forest show large training/test performance differences in the saved results.
- **Model selection:** Choosing the preferred model using test-set metrics may bias the apparent performance of the selected model.
- **Clinical applicability:** This project is exploratory and educational. It should not be used to guide individual patient care or clinical risk decisions.

## Repository organization

```text
PBC-Mortality-Classification/
├── README.md
├── Wk5_Final_Project.ipynb
├── requirements.txt       # To be generated from the tested environment
└── data/                  # Optional: dataset or download instructions, subject to license
```

The structure above is a **recommended layout**; it does not imply that all listed files already exist in the repository.

## Dataset citation

UCI Machine Learning Repository. *Cirrhosis Patient Survival Prediction*. https://archive.ics.uci.edu/dataset/878/cirrhosis+patient+survival+prediction+dataset-1
