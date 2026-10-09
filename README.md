# Diamond Price Prediction

**Four regression models of increasing complexity that predict diamond prices from weight, cut, color, and clarity.**

The project starts from a one-variable baseline and adds encoding, scaling, and polynomial terms step by step, measuring how much each decision improves the fit.

📓 **[Open the notebook](diamond_price_regression.ipynb)** (outputs included; text in Spanish)

---

## Results

Test set: 20% hold-out of 53,771 diamonds.

| Model | What it adds | RMSE (USD) | R² |
|---|---|---|---|
| `Baseline` | Simple linear regression on `carat` only | 1,532 | 0.846 |
| `Ordinal_Robust` | All predictors, ordinal encoding + robust scaling | 1,217 | 0.903 |
| `OHE_MinMax` | One-hot encoding + Min-Max scaling | 1,143 | 0.914 |
| **`PolynomialDegree2`** | Degree-2 polynomial features on top of `OHE_MinMax` | **749** | **0.963** |

The polynomial model cuts the baseline error roughly in half.

## Approach

1. **Cleaning.** 53,916 rows loaded, 145 duplicates removed, no missing values.
2. **Exploration.** Univariate distributions, price by color grade, and price against weight by cut quality.
3. **Redundancy reduction.** The dimensions `x`, `y`, and `z` are all correlated above 0.95 with `carat`, so they were dropped and `carat` was kept as the single size variable.
4. **Modeling.** scikit-learn pipelines with `ColumnTransformer`, so encoding and scaling are fitted on the training data only. The polynomial expansion turns 20 preprocessed columns into 230 features.
5. **Interpretation.** The ten largest coefficients of the best model were extracted and analyzed.

## Key takeaways

- **Weight drives price, and not linearly.** `carat²` is the largest coefficient: price grows more than proportionally with size.
- **Clarity multiplies the value of weight.** The `carat × clarity` interactions carry large positive coefficients, so a diamond that is both large and clean commands a strong premium.
- **A confounder hides the effect of color.** In the raw data, worse color grades show *higher* median prices, because those diamonds are heavier on average (0.66 carats for grade D against 1.16 for grade J). Once the model controls for weight, poor color lowers the price, as expected.

## Limitations

- Results come from a single 80/20 train/test split (`random_state=1`); no cross-validation was run.
- Prediction error grows for high-priced diamonds, where there are fewer examples.
- The decision to drop `x`, `y`, and `z` was made on the full dataset, before the train/test split.

## How to run

```bash
pip install -r requirements.txt
jupyter notebook diamond_price_regression.ipynb
```

The notebook expects the dataset at `data/diamonds_dataset.csv`.

## Data

The `diamonds` dataset distributed with the [ggplot2](https://ggplot2.tidyverse.org/reference/diamonds.html) R package. The copy used here has 53,916 rows.

## Tools

Python · pandas · NumPy · scikit-learn · Matplotlib · seaborn

## Context and credits

- Academic team project (4 members), completed during the M.Sc. in Applied Artificial Intelligence at Tecnológico de Monterrey.
- **My role:** TODO — one or two lines on what you personally did.
- **AI-use disclosure:** as stated at the end of the notebook, Gemini was used for code optimization and debugging.
