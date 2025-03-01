title: Intermediate Machine Learning with scikit-learn: Evaluation, Calibration, and Inspection
use_katex: True
class: title-slide

# Intermediate Machine Learning with scikit-learn
## Evaluation, Calibration, and Inspection

![:scale 30%](images/scikit-learn-logo-notext.png)

.larger[Thomas J. Fan]<br>
@thomasjpfan<br>
<a href="https://www.github.com/thomasjpfan" target="_blank"><span class="icon icon-github icon-left"></span></a>
<a href="https://www.twitter.com/thomasjpfan" target="_blank"><span class="icon icon-twitter"></span></a>
<a class="this-talk-link", href="https://github.com/thomasjpfan/ml-workshop-intermediate-2-of-2" target="_blank">
This workshop on Github: github.com/thomasjpfan/ml-workshop-intermediate-2-of-2</a>

---

name: table-of-contents
class: middle, larger

# Table of Contents
.g[
.g-6[
1. [Model Evaluation](#evaluation)
1. [Model Calibration](#calibration)
1. [Model Inspection](#inspection)
]
.g-6.g-center[
![](images/scikit-learn-logo-notext.png)
]
]

---

# Scikit-learn API

.center[
## `estimator.fit(X, [y])`
]

.g[
.g-6[
## `estimator.predict`
- Classification
- Regression
- Clustering
]
.g-6[
## `estimator.transform`
- Preprocessing
- Dimensionality reduction
- Feature selection
- Feature extraction
]
]

---

# Data Representation

![:scale 80%](images/data-representation.svg)

---

# Supervised ML Workflow

![:scale 100%](images/ml-workflow-sklearn.svg)

---

name: evaluation
class: chapter-slide

# 1. Model Evaluation

.footnote-back[
[Back to Table of Contents](#table-of-contents)
]

---

# How we were evaluating so far?
## Classification uses accuracy

```py
from sklearn.linear_model import LogisticRegression

lr = LogisticRegression().fit(X_train, y_train)

*lr.score(X_test, y_test)
```

## Regression uses coefficient of determination (R^2)

```py
from sklearn.tree import DecisionTreeRegressor

tree = DecisionTreeRegressor().fit(X_train, y_train)

*tree.score(X_test, y_test)
```

---

class: chapter-slide

# Metrics for Binary Classification

---

# Positive or Negative class?

.g.g-middle[
.g-4[
![:scale 100%](images/spam.jpg)
]
.g-4[
![:scale 100%](images/cat-dog.jpg)
]
.g-4[
![:scale 100%](images/national-cancer-institute.jpg)
]
]

## Arbitrary decision but is often the minority class

---

class: center

# Confusion Matrix

![:scale 70%](notebooks/images/confusion_matrix.png)

$$
\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}
$$

---

# Confusion Matrix in scikit-learn

```python
from sklearn.metrics import confusion_matrix

lr = LogisticRegression().fit(X_train, y_train)
y_pred = lr.predict(X_test)

*confusion_matrix(y_test, y_pred)

# array([[50,  3],
#        [ 3, 87]])
```

---

# Plotting the confusion matrix

```python
from sklearn.metrics import ConfusionMatrixDisplay

*ConfusionMatrixDisplay.from_estimator(lr, X_test, y_test)
```

![:scale 50%](notebooks/images/confusion_matrix_normal.png)

---

### Confusion matrix on wikipedia

![:scale 100%](images/wiki-zoo.png)

### confusion matrix in scikit-learn
![:scale 25%](notebooks/images/confusion_matrix_normal.png)

---

# Scikit-learn defaults to accuracy

### Data with 90% True Negatives

![:scale 100%](notebooks/images/confusion_matrix_90_neg.png)

---

# Other Common Metrics
## Precision, Recall, f-score

.g[
.g-4[
$$
\text{precision} = \frac{TP}{TP + FP}
$$

$$
\text{recall} = \frac{TP}{TP + FN}
$$

$$
\text{F} = 2 \frac{\text{precision}\cdot\text{recall}}{\text{precision} + \text{recall}}
$$
]
.g-8[
![:scale 100%](notebooks/images/confusion_matrix.png)
]
]

---

# Normalizing the confusion matrix

.g[
.g-6[
```py
confusion_matrix(y_true, y_pred,
                 normalize='true')
```
![:scale 100%](notebooks/images/confusion_matrix_norm_true.png)
]
.g-6[
```py
confusion_matrix(y_true, y_pred,
                 normalized='pred')
```
![:scale 100%](notebooks/images/confusion_matrix_norm_pred.png)
]
]

---

# Averaging strategies

```py
y_true = [1, 0, 0, 0, 0, 0, 0, 1]
y_pred = [1, 1, 1, 1, 0, 0, 1, 0]
```

```py
recall_score(y_true, y_pred, pos_label=1)
# 0.5

recall_score(y_true, y_pred, pos_label=0)
# 0.333333333333
```

.g[
.g-6[
## `macro`

$\frac{1}{2}\left(\frac{1}{2} + \frac{1}{3}\right) = \frac{5}{12}$
```py
recall_score(y_true, y_pred,
             average='macro')
# 0.416666666
```
Interested in the minority class
]
.g-6[
## `weighted`
$\frac{1}{8}\left(2\cdot\frac{1}{2} + 6\cdot\frac{1}{3}\right) = \frac{3}{8}$
```py
recall_score(y_true, y_pred,
             average='weighted')
# 0.375
```
Adds more weight the the majority class
]
]

---

# Classification Report

```py
from sklearn.metrics import classification_report

y_true = [1, 0, 0, 0, 0, 0, 0, 1]
y_pred = [1, 1, 1, 1, 0, 0, 1, 0]

print(classification_report(y_true, y_pred))
```

```
              precision    recall  f1-score   support

           0       0.67      0.33      0.44         6
           1       0.20      0.50      0.29         2

    accuracy                           0.38         8
   macro avg       0.43      0.42      0.37         8
weighted avg       0.55      0.38      0.40         8
```

---

class: chapter-slide

# Notebook 📓!
## notebooks/01-model-evaluation.ipynb

---

# Scikit-learn prediction API

### Predicting classes

```py
log_reg.predict(X_test)
# [0, 1, 0, 0, ...]
```

### Predicting probabilities

```py
log_reg.predict_proba(X_test)
# array([[0.7, 0.3],
#        [0.1, 0.9],
#        [0.8, 0.2]])
```

### Decision function

```py
log_reg.decision_function(X_test)
# array([-1.203, 4.291, -2.745]
```

---

# Changing Thresholds

.g[
.g-8[
.smaller-x[
```py
y_pred = log_reg.predict(X_test)
print(classification_report(y_test, y_pred))
```

```
              precision    recall  f1-score   support

       False       0.99      1.00      0.99      2731
        True       0.75      0.37      0.49        65

    accuracy                           0.98      2796
   macro avg       0.87      0.68      0.74      2796
weighted avg       0.98      0.98      0.98      2796
```

```py
y_pred_20 = log_reg.predict_proba(X_test)[:, 1] > 0.25
print(classification_report(y_test, y_pred_20))
```

```
              precision    recall  f1-score   support

       False       0.99      0.99      0.99      2731
        True       0.63      0.55      0.59        65

    accuracy                           0.98      2796
   macro avg       0.81      0.77      0.79      2796
weighted avg       0.98      0.98      0.98      2796
```
]
]
.g-4[
$$
\text{precision} = \frac{TP}{TP + FP}
$$

$$
\text{recall} = \frac{TP}{TP + FN}
$$
]
]

---

# Precision recall curve

```py
from sklearn.metrics import PrecisionRecallDisplay
PrecisionRecallDisplay.from_estimator(
    log_reg, X_test, y_test, name="LogisticRegression")
```

.g[
.g-8[
![:scale 100%](notebooks/images/plot_precision_recall_curve.png)
]
.g-4[
$$
\text{precision} = \frac{TP}{TP + FP}
$$

$$
\text{recall} = \frac{TP}{TP + FN}
$$
]
]

---

# Average Precision

$$
\text{AveP} = \sum_{k=1}^n P(k)\Delta r(k)
$$

.center[
![:scale 60%](notebooks/images/plot_precision_recall_curve.png)
]

---

# ROC Curve (Receiver Operating Characteristic)

```py
from sklearn.metrics import RocCurveDisplay
RocCurveDisplay.from_estimator(
    log_reg, X_test, y_test, name="LogisticRegression")
```

.g[
.g-8[
![:scale 100%](notebooks/images/plot_roc_curve.png)
]
.g-4[
$$
\text{TPR} = \frac{TP}{TP + FN}
$$
$$
\text{FPR} = \frac{FP}{FP + TN}
$$
]
]

---

# Both Curves

![:scale 100%](notebooks/images/both_plots.png)

---

class: chapter-slide

# Notebook 📓!
## notebooks/01-model-evaluation.ipynb

---

# Multi-class Metrics

![:scale 60%](notebooks/images/confusion_matrix_multiclass.png)

---

# Classification report

```py
print(classification_report(y_test, y_pred))
```

```

              precision    recall  f1-score   support

           0       0.98      0.96      0.97        45
           1       0.93      0.93      0.93        46
           2       1.00      0.98      0.99        44
           3       0.94      1.00      0.97        46
           4       0.96      1.00      0.98        45
           5       0.98      0.96      0.97        46
           6       1.00      0.98      0.99        45
           7       0.94      1.00      0.97        45
           8       0.95      0.86      0.90        43
           9       0.93      0.93      0.93        45

    accuracy                           0.96       450
   macro avg       0.96      0.96      0.96       450
weighted avg       0.96      0.96      0.96       450
```

---

# Multi-class ROC AUC

### Hand & Till, 2001, One vs One

```py
from sklearn.metrics import roc_auc_score
roc_auc_score(y_test, rf_y_pred_proba, multi_class='ovo')
# 0.998798
```


### Provost & Domingo, 2000, One vs Rest

```py
from sklearn.metrics import roc_auc_score
roc_auc_score(y_test, rf_y_pred_proba, multi_class='ovo')
# 0.998800
```

---

# Metrics for regression models

- $R^2$ : default metric for scikit-learn regressors

```py
from sklearn.metrics import r2_score
```

- Mean Squared Error (MSE)

```py
from sklearn.metrics import mean_squared_error
```

- Mean Absolute error, Median Absolute error

```py
from sklearn.metrics import mean_absolute_error
from sklearn.metrics import median_absolute_error
```

---

# Metrics Covered Today

## Classification
- recall
- precision
- roc auc
- average precision

## Regression
- r2 score
- mean squared error
- mean absolute error
- median absolute error

There are many metrics in scikit-lean that can be found
[here](https://scikit-learn.org/stable/modules/model_evaluation.html#metrics-and-scoring-quantifying-the-quality-of-predictions)

---

class: middle

# Picking metrics

- Accuracy is only useful for balanced problems
- What part of the confusion matrix is important to you?
- Align metrics with your business problem

<br>

![:scale 100%](images/metrics-business.svg)

---

name: calibration
class: chapter-slide

# 2. Model Calibration

.footnote-back[
[Back to Table of Contents](#table-of-contents)
]


---

# Calibration

## What would you prefer?

> The model predicted you do not have cancer.

### Or

> The model predicted you are 30% likely to have cancer.

---

# Calibration Curve

.g[
.g-6[
![:scale 100%](images/calibration-classifier.svg)
]
.g-6[
![:scale 100%](images/caligration_classifier.png)
]
]

---

# Calibration curve in sklearn

```py
from sklearn.calibration import calibration_curve

lr_proba = lr.predict_proba(X_test)

prob_true, prod_pred = calibration_curve(y_test, lr_proba[:, 1], n_bins=5)

prob_true
# [0.05618839 0.29477612 0.51824088 0.71928429 0.94240662]
prod_pred
# [0.05863318 0.29295884 0.50067089 0.70591316 0.94189604]
```

## Brier Score
### Mean squared error for probability estimates

```py
from sklearn.metrics import brier_score_loss
```

$$
\text{Brier score loss} = \frac{\sum_{i=1}^{n}(\hat{p}(y_i) - y_i)}{n}
$$

---

# Number of bins

![:scale 100%](notebooks/images/compare_calibration_n_bins.png)

---

# Comparing estimators

![:scale 100%](notebooks/images/compare_calibration_estimators.png)

---

# Calibrating a classifier

- Build another model mapping from classifier probabilities to better probabilities
- 1d model
$$
f_{calib}(s(x)) \approx p(y)
$$
- s(x) is the score given by the model
    - Can either be a decision function or probability

---

class: middle

# Platt Scaling

- Use a logistic sigmoid for $f_{calib}$

$$
f_{platt} = \frac{1}{1 + \exp(-w\cdot s(x) + b)}
$$

---

# Isotonic Regression

.g.g-middle[
.g-6[
- Non-parametric mapping
- Learns monotonically increasing step-functions in 1d
- Groups data into constant parts
]
.g-6[
![:scale 100%](images/isotonic_regression.png)
]
]

---

# Fitting the calibration model

![:scale 80%](images/calibration_val_scores.png)

---

# Fitting the calibration model

![:scale 80%](images/calibration_val_scores_fitted.png)

---

# Calibration in scikit-learn

```py
from sklearn.calibration import CalibratedClassifierCV
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier()

cal_rf = CalibratedClassifierCV(rf, method="isotonic")
cal_rf.fit(X_train, y_train)
```

- By default, splits the training dataset into 5 folds
- Trains 5 (classifier, regressor) pairs

---

class: chapter-slide

# Notebook 📔!
## notebooks/02-model-calibration.ipynb

---

name: inspection
class: chapter-slide

# 3. Model Inspection

.footnote-back[
[Back to Table of Contents](#table-of-contents)
]
---

class: middle

# Post-hoc Model Interpretation

- Not inference
- Not causality
- Explaining the model != explaining the data
- Model inspection only tells you about the model
- Useful only if the model is good

---

class: middle

# Post-hoc Model Interpretation

## Inspection based on model
- Interpreting the coefficients in a linear model: `coef_`
- `feature_importances_` of a random forest

## Model-agnostic
- Permutation importance
- Partial dependence curves

---

class: chapter-slide

# Notebook 📕!
## notebooks/03-model-inspection.ipynb

---

# Permutation Feature Importance 👑 (Pt 1)

```py
X_train = [[0, 1, 2],
           [1, 2, 3],
           [2, 1, 4],
           [3, 1, 9],
           [4, 3, 1]]
y_train = [1, 0, 1, 1, 0]

model.fit(X_train, y_train)
model.score(X_train, y_train)
# 0.90
```

---

# Permutation Feature Importance 👑 (Pt 2)

```py
X_train_perm_1 = [
    [1, 1, 2],
    [0, 2, 3],
    [2, 1, 4],
    [4, 1, 9],
    [3, 3, 1]
]
model.score(X_train_perm_1, y_train)
# 0.70
```

---

# Permutation Feature Importance 👑 (Pt 3)

```py
X_train_perm_2 = [
    [1, 1, 2],
    [3, 2, 3],
    [4, 1, 4],
    [2, 1, 9],
    [0, 3, 1]
]
model.score(X_train_perm_1, y_train)
# 0.73
```

---

# Permutation Feature Importance 👑 (Pt 4)

```py
model.score(X_train_perm_3, y_train)
# 0.80
```

- Remember: `model.score(X_train, y_train) = 0.90`
- permutation feature importance for the 1st feature:

```py
[0.90 - 0.70, 0.90 - 0.73, 0.90 - 0.80]
# [0.20, 0.17, 0.10]
```

---

# Permutation Feature Importance 👑 (Pt 5)

```py
from sklearn.inspection import permutation_importance

result = permutation_importance(model, X, y, n_repeats=3)

result['importances']
# [[0.20, 0.17, 0.10], [0.5, 0.4, 0.6], ...]
result['importances_mean']
# [ 0.157, 0.5, ...]
result['importances_std']
# [0.0419 0.0816, ...])
```

---

# Partial Dependence Plots (Pt 1)

```py
X_train = [
    [0, 1, 2],
    [1, 2, 3],
    [2, 4, 4],
    [3, 1, 9],
    [4, 3, 1]
]
y_train = [0.1, 0.2, 0.3, 0.4, 0.5]

model.fit(X_train, y_train)
```

---

# Partial Dependence Plots (Pt 2)

```py
X_train_0 = [
    [0, 1, 2],
    [0, 2, 3],
    [0, 4, 4],
    [0, 1, 9],
    [0, 3, 1]
]
model.predict(X_train_0).mean()
# 0.1
```

---

# Partial Dependence Plots (Pt 3)

```py
X_train_1 = [
    [1, 1, 2],
    [1, 2, 3],
    [1, 4, 4],
    [1, 1, 9],
    [1, 3, 1]
]
model.predict(X_train_1).mean()
# 0.24
```

---

# Partial Dependence Plots (Pt 4)

.g[
.g-6[
```py
model.predict(X_train_0).mean()
# 0.1

model.predict(X_train_1).mean()
# 0.24

model.predict(X_train_2).mean()
# 0.5

...
```
]
.g-6[
![:scale 100%](notebooks/images/partial_dependence_example.png)
]
]

---

# Partial Dependence Plots in scikit-learn

```py
from sklearn.inspection import PartialDependenceDisplay

PartialDependenceDisplay.from_estimator(
    estimator, X, features)
```

![:scale 65%](notebooks/images/partial_dependence_multiple.png)

---

class: chapter-slide

# Notebook 📘!
## notebooks/03-model-inspection.ipynb

---

class: title-slide, left

# Closing

.g.g-middle[
.g-7[
![:scale 30%](images/scikit-learn-logo-notext.png)
1. [Model Evaluation](#evaluation)
1. [Model Calibration](#calibration)
1. [Model Inspection](#inspection)
]
.g-5.center[
<br>
.larger[Thomas J. Fan]<br>
@thomasjpfan<br>
<a href="https://www.github.com/thomasjpfan" target="_blank"><span class="icon icon-github icon-left"></span></a>
<a href="https://www.twitter.com/thomasjpfan" target="_blank"><span class="icon icon-twitter"></span></a>
<a class="this-talk-link", href="https://github.com/thomasjpfan/ml-workshop-intermediate-2-of-2" target="_blank">
This workshop on Github: github.com/thomasjpfan/ml-workshop-intermediate-2-of-2</a>
]
]
