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