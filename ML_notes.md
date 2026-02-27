# Machine Learning notes for miRNA target scanner project

## Classification vs Regression (plain explanation)

Regression predicts a number/score

Examples:
    1. number of targets per miRNA
    2. mean context++ score

    3. minimum context++ score

Output: continuous value
(e.g. −0.35, 12, 0.72)

Typical questions:
    1. How strong is this miRNA as a regulator?
    2. How many targets does it tend to have?

Common models:
    1. Linear regression
    2. Ridge / Lasso
    3. Random forest regressor

Classification predicts a category/class

Examples:
    1. high-targeting vs low-targeting miRNA
    2. strong vs weak regulator (binary)

Output: discrete label (e.g. 0 / 1, yes / no)

Typical questions:
    1. Is this miRNA likely to be a strong regulator?
    2. Does this miRNA belong to a high-impact group?

Common models:
    1. Logistic regression
    2. Random forest classifier
    3. XGBoost classifier

## Fetures vs. labels

1. Features: in this context, we are talking about intrinsic properties (e.g. sequence-derived properties such as seed, GC-content)
2. Labels: extrinsic properties, such as targeting strength 


# Linear Model Anatomy – Conceptual and Mathematical Notes

## 1. Supervised Learning Setup

In supervised regression, we aim to model:

y = f(X) + ε

Where:

- y = response variable (e.g., mean context++ score)
- X = matrix of input features
- f(X) = systematic relationship between features and response
- ε = noise (unexplained variation)

Noise represents biological variability, measurement uncertainty, and missing predictors.

---

## 2. Linear Regression (OLS)

### Model Form

In linear regression, we assume:

y = β₀ + β₁x₁ + β₂x₂ + ... + βₚxₚ + ε

Where:

- β₀ = intercept
- βᵢ = coefficient for feature xᵢ
- p = number of predictors

The goal is to estimate β values that minimize:

Sum of Squared Errors (SSE)

SSE = Σ (yᵢ − ŷᵢ)²

OLS finds the coefficients that minimize this quantity.

---

## 3. Matrix Notation (Explained Clearly)

The linear model can be written compactly as:

β̂ = (XᵀX)⁻¹ Xᵀy

Where:

- X = feature matrix (rows = samples, columns = features)
- Xᵀ = transpose of X (rows and columns swapped)
- XᵀX = feature covariance structure
- (XᵀX)⁻¹ = inverse matrix
- y = response vector
- β̂ = estimated coefficients

Key intuition:

XᵀX describes how features relate to each other.  
If features are highly correlated, XᵀX becomes unstable to invert.

---

## 4. Multicollinearity

Multicollinearity occurs when predictors are correlated.

Effect:

- Model struggles to uniquely assign effect to each feature.
- Coefficients become unstable.
- Small changes in training data → large coefficient swings.

Important distinction:

- Prediction accuracy may remain reasonable.
- Interpretability and stability suffer.

---

## 5. Ridge Regression (L2 Regularization)

### Modified Objective

Ridge modifies the OLS objective:

Minimize:

SSE + λ Σ βⱼ²

Where:

- λ (lambda) = regularization strength
- Σ βⱼ² = squared magnitude of coefficients

Effect:

- Penalizes large coefficients
- Shrinks coefficients toward zero
- Reduces instability

---

### Matrix Form of Ridge

β̂_ridge = (XᵀX + λI)⁻¹ Xᵀy

Where:

- I = identity matrix (diagonal matrix with 1’s on diagonal)
- λI increases diagonal values
- This stabilizes the matrix inversion

---

## 6. Bias–Variance Tradeoff

Increasing λ:

- Increases bias (model underfits slightly)
- Decreases variance (coefficients more stable)

OLS:
- Low bias
- High variance (especially with correlated predictors)

Ridge:
- Slightly higher bias
- Lower variance

Model performance improves if variance reduction outweighs bias increase.

---

## 7. R² (Coefficient of Determination)

R² measures proportion of variance explained:

R² = 1 − (Residual Sum of Squares / Total Sum of Squares)

Interpretation:

- R² = 0 → model explains none of the variance
- R² = 1 → perfect fit
- R² ≈ 0.38 → model explains 38% of outcome variability

Remaining variance is due to noise and missing predictors.

---

## 8. Cross-Validation

Cross-validation estimates generalization performance.

Procedure:

- Split data into k folds
- Train on k−1 folds
- Test on remaining fold
- Repeat

Repeated K-Fold:

- Repeat process multiple times with different splits
- Reduces randomness in performance estimation

Important distinction:

- Ridge stabilizes coefficients.
- Repeated CV stabilizes performance estimates.

---

## 9. Standardization

Features were standardized before Ridge:

x_scaled = (x − mean) / standard deviation

Reason:

- Prevents features with larger numeric scale from dominating penalty.
- Makes coefficients comparable in magnitude.
- Allows interpretation per 1 standard deviation increase.

---

## 10. Practical Interpretation in This Project

Key finding:

Seed GC content showed the strongest standardized coefficient.

This aligns with biological expectations:

- Seed region drives binding specificity.
- GC-rich seeds form stronger base pairing.
- Thermodynamic stability increases repression likelihood.

The model captures biologically meaningful signal but leaves substantial unexplained variance.

This is consistent with the complex nature of miRNA-mediated repression.