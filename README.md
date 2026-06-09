# README: Antioxidant Correlation, Regression & Logistic Modeling

This R script performs a complete statistical workflow:
- Permutation‑based Spearman correlation (no external packages)
- Multiple regression models (linear, polynomial, exponential, logarithmic) with BIC comparison
- Logistic regression, ROC/AUC, stepwise selection, and odds ratios
- Visualization of significant correlations

All analyses are based on antioxidant measurements and a binary outcome.

---

## Requirements

- **R version** ≥ 4.0
- **Packages** (automatically installed if missing):
  - `pROC` – for ROC curve and AUC

No other packages are required; the permutation test uses only base R.

---

## Data Format

Your input CSV file must contain the following columns (exact names as used in the script):

| Column         | Type               | Description                          |
|----------------|--------------------|--------------------------------------|
| `outcome`      | integer / factor   | Binary outcome (0/1)                 |
| `antioxidant1` | numeric            | Primary response variable            |
| `antioxidant2` | numeric            | Predictor / correlation variable     |
| `antioxidant3` | numeric            | Additional antioxidant               |
| `antioxidant4` | numeric            | Additional antioxidant               |

- Rows with missing `outcome` are removed automatically.
- Missing values in other variables may cause errors; clean your data beforehand.

---

## Script Workflow

### 1. Setup & Data Loading
- Prints current working directory (`getwd()`)
- Reads CSV from `C://Users//ASUS//Downloads//data_for_analysis.csv` (modify path if needed)
- Converts `outcome` to factor
- Displays summary statistics

### 2. Normality Check
- Shapiro‑Wilk test for `antioxidant1` and `antioxidant2`
- Histogram and Q‑Q plot for `antioxidant1`

### 3. Permutation Spearman Correlation
- Custom `perm_spearman_test(x, y, R=10000)`  
  - Computes observed Spearman correlation  
  - Generates null distribution by permuting `x` (10,000 times)  
  - Returns two‑sided p‑value  
- Correlations computed:
  - `antioxidant1` vs. each of `antioxidant2`, `antioxidant3`, `antioxidant4`
  - All pairwise among `antioxidant2`, `antioxidant3`, `antioxidant4`
- Results stored in a data frame and printed

### 4. Visualization of Significant Correlations (p < 0.05)
- For each significant pair:
  - Sorted scatter plot with linear regression line (red)
- If no significant results, a default plot of `antioxidant2` vs. `antioxidant1` is generated (with blue line connecting points and red regression line)

### 5. Regression Analysis (response = `antioxidant1`, predictor = `antioxidant2`)
- **Linear**: `lm(antioxidant1 ~ antioxidant2)`
- **Second‑degree polynomial**: `lm(antioxidant1 ~ poly(antioxidant2, 2))`
- **Third‑degree polynomial**: `lm(antioxidant1 ~ poly(antioxidant2, 3))`
- **Exponential**: `lm(log(antioxidant1) ~ antioxidant2)`
- **Logarithmic**: `lm(antioxidant1 ~ log(antioxidant2))`
- All model summaries printed
- **BIC comparison** table (sorted ascending by BIC; lower = better fit)
- Plot of linear model fit (scatter + blue fitted line)

### 6. Logistic Regression (response = `outcome`)
- **Model 1 (simple)**: `outcome ~ antioxidant1`
- **Model 2 (two predictors)**: `outcome ~ antioxidant1 + antioxidant2`
- **Model 3 (full)**: `outcome ~ antioxidant1 + antioxidant2 + antioxidant3 + antioxidant4`
- For Model 2:
  - Predict probabilities, classify at threshold 0.5
  - Confusion matrix (actual vs. predicted)
  - ROC curve and AUC (using `pROC`)
- **Stepwise variable selection** (both directions, AIC) starting from the full model
- **Odds ratios** with 95% confidence intervals for Model 2 (exponentiated coefficients)

---

## Outputs

### Console
- Data summary, normality test results
- Permutation Spearman correlation table
- Summaries of all five regression models
- BIC comparison table
- Logistic regression summaries, confusion matrix, AUC value
- Stepwise model output
- Odds ratios table

### Graphics (displayed interactively)
- Histogram and Q‑Q plot of `antioxidant1`
- Scatter plots for significant correlation pairs (with red regression line)
- Linear regression fit plot (scatter + blue line)
- ROC curve

---

## How to Run

1. **Place your data file** at:  
   `C://Users//ASUS//Downloads//data_for_analysis.csv`  
   (or edit the path in `read.csv()`)

2. **Open R / RStudio** and run the entire script.

3. **If `pROC` is not installed**, the script will install it automatically.

4. **Observe the console output** and **view the generated plots**.

> **Note:** Some parts (e.g., many correlation plots) may produce multiple graphics windows; you may want to run the script in chunks.

---

## Customization Tips

- **Change number of permutations**: modify `R` inside `perm_spearman_test()`.
- **Adjust significance threshold**: replace `0.05` in `sig_results` filter.
- **Add more variables** to regression or logistic models: extend the formula.
- **Save plots automatically**: wrap plotting code with `pdf("file.pdf")` and `dev.off()`.

---

## Important Notes

- **BIC comparison** across models with different dependent variables (e.g., `log(antioxidant1)` vs. `antioxidant1`) is **not strictly valid** – use only for models with the same left‑hand side.
- The permutation test assumes exchangeability under the null. With tied ranks, Spearman correlation is still appropriate; the permutation procedure remains valid.
- Missing values are **not handled** automatically beyond removing rows with missing `outcome`. Ensure that `antioxidant1`–`antioxidant4` have no `NA`s before running.

---

## Author & License

This script was written for antioxidant data analysis. Free to use, modify, and distribute with proper attribution.

**Last updated:** June 2026
