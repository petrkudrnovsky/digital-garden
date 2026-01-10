- this is a summary from Claude Opus optimized for the multichoice exam
## Table of Contents
- [[#Chapter 1 - Introduction|Chapter 1 - Introduction]]
- [[#Chapter 2 - Basics in Python|Chapter 2 - Basics in Python]]
- [[#Chapter 3 - Machine Learning in Python|Chapter 3 - Machine Learning in Python]]
- [[#Chapter 4 - Tips, Tricks and Tools|Chapter 4 - Tips, Tricks and Tools]]

---
## Chapter 1 - Introduction

### Key Concepts
**Programming Languages**
- Python is favored for data mining due to readability, low learning threshold, and comprehensive modules
- Python is interpreted line-by-line (unlike C++ which compiles first)

**Benefits of Python**
- Easy to read and understand
- **Object-oriented** - everything is an object of a class
- Free and open-source
- Portable across operating systems
- Extensive libraries available

**Modules vs Packages**
- **Module** = single Python file containing code
- **Package** = collection of modules in a directory
- **Library** = collection of packages/modules for specific tasks

**IDE (Integrated Development Environment)**
- Visual Studio Code is recommended
- IDE ≠ programming language (Python is language, VS Code is IDE)
### Virtual Environments
- Isolated space for project-specific package versions
- Prevents conflicts between different projects
- Created with: `python -m venv venv`
- Activated with: `.\venv\Scripts\activate` (Windows) or `source venv/bin/activate` (Mac/Linux)

---

## Chapter 2 - Basics in Python
### Variable Types
#### Strings
```python
name = "Charlie"
age = "47"
# Concatenation
print("Name: " + name)
# F-strings (can include non-strings)
print(f"Name: {name}, Age: {age}")
# String operations
"abc" * 3  # "abcabcabc"
"hello".split()  # ["hello"]
"Antwerp" in "Belgium has Antwerp"  # True
```
#### Numeric Data
- **Integers**: unlimited precision, whole numbers
- **Floats**: limited precision, decimal numbers
```python
a / b   # Division (returns float)
a // b  # Floor division (returns integer)
a % b   # Modulo (remainder)
a ** b  # Exponentiation
```
#### Lists (Mutable)
```python
names = ["Ada", "Steve", "Mohammed"]
names[0]      # "Ada"
names[0:2]    # ["Ada", "Steve"]
names.append("New")
names.remove("Ada")
names.sort()

# List comprehension
squares = [x**2 for x in range(10)]
even_squares = [x**2 for x in range(10) if x % 2 == 0]
```
#### Tuples (Immutable)
```python
t = ("Belgium", "Sweden", "Germany")
t[0]  # "Belgium"
# Unpacking
(a, b, c) = t
```
#### Dictionaries
```python
user_age = {"Kenneth": 45, "Hassan": 23}
user_age["Kenneth"]  # 45
user_age.get("Unknown", "Not found")  # "Not found"
user_age.keys()
user_age.values()
del user_age["Kenneth"]
```
### Flow Control
#### If Statements
```python
if condition1:
    # code
elif condition2:
    # code
else:
    # code

# Operators: ==, !=, <, >, <=, >=, and, or, not, in
```
#### For Loops
```python
for i in range(start, stop, step):
    # code

# Keywords:
# continue - skip rest of current iteration
# break - exit loop entirely
# else - executed if loop doesn't break
```
#### While Loops
```python
while condition:
    # code
```
### Functions
```python
def function_name(param1, param2=default_value):
    """Docstring"""
    result = param1 + param2
    return result

# Multiple arguments
def func(*args):
    for arg in args:
        print(arg)
```

**Variable Scope**
- **Local variables**: defined inside function, only accessible there
- **Global variables**: defined outside, accessible everywhere
- Use `global var_name` to modify global variable inside function
### Objects and Classes
```python
class Student:
    def __init__(self, name, age):
        self.name = name  # attribute
        self.age = age
    
    def greet(self):  # method
        return f"Hello, I'm {self.name}"

s = Student("Alice", 23)
s.name       # attribute (no parentheses)
s.greet()    # method (with parentheses)
```
### Common Error Types

|Error|Cause|
|---|---|
|`SyntaxError`|Incorrect syntax (missing colon, brackets)|
|`IndentationError`|Inconsistent indentation|
|`NameError`|Undefined variable/function|
|`TypeError`|Wrong type for operation|
|`ValueError`|Right type, wrong value|
|`IndexError`|Index out of range|
|`KeyError`|Dictionary key not found|
|`AttributeError`|Non-existent attribute/method|
|`ImportError`|Failed import|

---
### NumPy
```python
import numpy as np

# Array creation
a = np.array([1, 2, 3, 4])
np.zeros((2, 3))
np.ones((2, 3))
np.random.random((2, 3))
np.arange(start, stop, step)
np.linspace(start, stop, num_points)

# Properties
a.ndim   # dimensions
a.shape  # size in each dimension
a.reshape(2, 2)

# Operations (element-wise)
a + b, a - b, a * b, a / b
a @ b  # matrix multiplication
np.dot(a, b)

# Aggregations
a.min(), a.max(), a.sum(), a.mean(), a.std()
a.sum(axis=0)  # sum columns
a.sum(axis=1)  # sum rows

# Indexing
a[1:5]
np.where(a == value)
np.argwhere(condition)
```
### Pandas
```python
import pandas as pd

# Load data
df = pd.read_csv('file.csv', sep=',', na_values=['NA'])

# Basic operations
df.head(), df.tail(), df.sample(5)
df.iloc[5:10]  # by index
df[["col1", "col2"]]  # select columns
df[df["col"] > value]  # filter rows

# Aggregation
df["col"].count()
df["col"].value_counts()
df["col"].sum(), .min(), .max(), .mean(), .median()
df.describe()
df.groupby(["col"]).mean()

# Merging
pd.merge(df1, df2, on="key", how="left")  # left, right, inner, outer

# Missing values
df["col"].fillna(value)
df.isna().sum()

# Sorting
df.sort_values("col", ascending=False)
df.reset_index()

# Save
df.to_csv("file.csv")
df.to_excel("file.xlsx")
```
### Matplotlib
```python
import matplotlib.pyplot as plt

# Basic plot
plt.figure(figsize=(10, 5))
plt.plot(x, y, "go", label="data")  # green dots
plt.title("Title")
plt.xlabel("X"), plt.ylabel("Y")
plt.axis([xmin, xmax, ymin, ymax])
plt.legend()
plt.show()

# Pandas plotting
df.hist(column="col", bins=15)
df.boxplot(column="col", by="group")
df["col"].value_counts().plot(kind="bar")
```
### Scikit-learn Basics
```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, roc_auc_score

# Split data
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, stratify=y
)

# Train model
model = LogisticRegression(C=0.01, max_iter=1000)
model.fit(X_train, y_train)

# Predict
labels = model.predict(X_test)
scores = model.predict_proba(X_test)[:, 1]

# Evaluate
accuracy = accuracy_score(y_test, labels)
auc = roc_auc_score(y_test, scores)
```

---
## Chapter 3 - Machine Learning in Python

> ⚠️ **This is the main focus chapter for the exam**
### CRISP-DM (Cross-Industry Standard Process for Data Mining)

Six phases forming a cyclical process:
1. **Business Understanding** → Define objectives, requirements, problem definition
2. **Data Understanding** → Explore data, discover insights, identify quality issues
3. **Data Preparation** → Transform raw data into final dataset
4. **Modeling** → Select and apply modeling techniques
5. **Evaluation** → Assess model against business objectives
6. **Deployment** → Put model into production

> The process is iterative - you often go back to previous phases

---
### 3.2 Business Understanding

**Key Questions:**
- What is the business problem?
- What are the project objectives?
- What defines success?

**German Credit Dataset Example:**
- Target: Classify loans as good (0) or bad (1) - did borrower default?
- Cost matrix: predicting wrong has different costs
    - False Negative (predict good, actually bad) = Cost 5
    - False Positive (predict bad, actually good) = Cost 1

---
### 3.3 Data Understanding

#### 3.3.1 Data Description
Answer these questions:
- Data format?
- Number of observations?
- Number of attributes?
#### 3.3.2 Data Exploration

**Univariate Statistics - Categorical Variables:**
```python
# Frequency distribution
df["col"].value_counts()
df["target"].value_counts(normalize=True)  # percentages
```

**Univariate Statistics - Continuous Variables:**
```python
df["col"].describe()  # min, max, mean, std, quartiles
df.hist(column="col")
df.boxplot(column="col")
```

**Check for Class Imbalance:**
- Extreme skewness (e.g., 1% fraud vs 99% non-fraud) causes problems
- Models may ignore minority class
- Solutions: cost-sensitive learning, oversampling, undersampling

**Missing Values:**
```python
df.isna().sum()  # count missing per column
df.isna().sum() / len(df) * 100  # percentage missing
```

**Outliers:**
- Valid outliers: unusual but possible values
- Invalid outliers: impossible values (e.g., negative age)
- Detection: boxplots, z-scores > 3

**Multivariate Statistics:**
```python
# Contingency table (crosstab)
pd.crosstab(df["target"], df["feature"], margins=True)
```

---
### 3.4 Data Preparation
#### 3.4.1 Train/Validation/Test Split
```python
from sklearn.model_selection import train_test_split

# First split: separate test set
X_temp, X_test, y_temp, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42, stratify=y
)

# Second split: training and validation
X_train, X_val, y_train, y_val = train_test_split(
    X_temp, y_temp, test_size=0.25, random_state=42, stratify=y_temp
)
```

**Purpose of each set:**
- **Training set**: Learn model parameters
- **Validation set**: Tune hyperparameters
- **Test set**: Final unbiased evaluation
#### 3.4.2 Missing Values
**Strategies:**
1. **Keep** - if algorithm handles missing values
2. **Delete** - remove rows (if few missing)
3. **Replace** - impute with:
    - Mode (most common) for categorical
    - Mean/median for continuous

```python
from sklearn.impute import SimpleImputer

# For categorical
imputer = SimpleImputer(strategy='most_frequent')

# For continuous
imputer = SimpleImputer(strategy='mean')

# IMPORTANT: Fit on training data only!
imputer.fit(X_train)
X_train = imputer.transform(X_train)
X_val = imputer.transform(X_val)
X_test = imputer.transform(X_test)
```

> ⚠️ **Data Leakage Warning**: Always calculate statistics (mean, mode) on training set only, then apply to validation/test sets.
#### 3.4.3 Variable Encoding
**One-Hot Encoding for Categorical Variables:**

```python
from sklearn.preprocessing import OneHotEncoder

encoder = OneHotEncoder(sparse=False, handle_unknown='ignore')
encoder.fit(X_train[categorical_cols])
encoded = encoder.transform(X_train[categorical_cols])
```

|Housing|A151|A152|A153|
|---|---|---|---|
|Rent|1|0|0|
|Own|0|1|0|
|Free|0|0|1|

**Binary variables**: Only need 1 dummy (not 2)

**Drop ID columns**: ClientID doesn't help prediction

#### 3.4.4 Normalization
**Why normalize?**
- Algorithms using distance (kNN, SVM) are affected by scale
- Features with larger ranges dominate distance calculations

**Z-score normalization:** $$z_i = \frac{x_i - \mu}{\sigma}$$
```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
scaler.fit(X_train)  # Fit on training only!
X_train_scaled = scaler.transform(X_train)
X_test_scaled = scaler.transform(X_test)
```

**Handling Outliers:**
- Z-score > 3 or < -3 indicates outlier
- Can clip to ±3
#### 3.4.5 Scikit-learn Pipelines

```python
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer

# Numeric pipeline
numeric_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='mean')),
    ('scaler', StandardScaler())
])

# Categorical pipeline
categorical_pipeline = Pipeline([
    ('imputer', SimpleImputer(strategy='most_frequent')),
    ('encoder', OneHotEncoder(handle_unknown='ignore'))
])

# Combined preprocessor
preprocessor = ColumnTransformer([
    ('num', numeric_pipeline, numeric_cols),
    ('cat', categorical_pipeline, categorical_cols)
])

# Full pipeline with model
full_pipeline = Pipeline([
    ('preprocessor', preprocessor),
    ('classifier', LogisticRegression())
])

full_pipeline.fit(X_train, y_train)
predictions = full_pipeline.predict(X_test)
```

---
### 3.5 Modeling and Evaluation
#### 3.5.1 Decision Tree

```python
from sklearn.tree import DecisionTreeClassifier
from sklearn.model_selection import GridSearchCV

# Basic model
dt = DecisionTreeClassifier(criterion='entropy', random_state=42)
dt.fit(X_train, y_train)

# Hyperparameter tuning
param_grid = {
    'max_depth': [3, 5, 7, 10, None],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 5, 10]
}

grid_search = GridSearchCV(dt, param_grid, cv=5, scoring='roc_auc')
grid_search.fit(X_train, y_train)
best_model = grid_search.best_estimator_
```

**Key Hyperparameters:**
- `criterion`: 'entropy' or 'gini'
- `max_depth`: Maximum tree depth (prevents overfitting)
- `min_samples_split`: Minimum samples to split a node
- `min_samples_leaf`: Minimum samples at leaf node
#### 3.5.2 Logistic Regression
```python
from sklearn.linear_model import LogisticRegression

lr = LogisticRegression(C=1.0, max_iter=1000, random_state=42)
lr.fit(X_train, y_train)

# Get coefficients
print(lr.intercept_)  # bias term
print(lr.coef_)       # feature weights

# Predict probabilities
proba = lr.predict_proba(X_test)[:, 1]
```

**Key Hyperparameters:**
- `C`: Regularization parameter (smaller = more regularization)
- `penalty`: 'l1', 'l2', 'elasticnet', or 'none'
- `max_iter`: Maximum iterations for solver
#### 3.5.3 Random Forest
```python
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier(
    n_estimators=100,      # number of trees
    max_depth=10,
    min_samples_leaf=5,
    random_state=42,
    n_jobs=-1              # use all CPU cores
)
rf.fit(X_train, y_train)

# Feature importances
importances = rf.feature_importances_
```

**Key Hyperparameters:**
- `n_estimators`: Number of trees (more = better but slower)
- `max_depth`, `min_samples_leaf`: Same as Decision Tree
- Random subset of features at each split reduces correlation between trees
#### 3.5.4 Support Vector Machine (SVM)
**Linear SVM:**
```python
from sklearn.svm import SVC

svm_linear = SVC(kernel='linear', C=1.0, probability=True)
svm_linear.fit(X_train, y_train)

# Get scores for AUC
scores = svm_linear.decision_function(X_test)
# or if probability=True:
scores = svm_linear.predict_proba(X_test)[:, 1]
```

**Non-linear SVM (RBF Kernel):**
```python
svm_rbf = SVC(kernel='rbf', C=1.0, gamma='scale', probability=True)

# Grid search for C and gamma
param_grid = {
    'C': [0.1, 1, 10, 100],
    'gamma': [0.001, 0.01, 0.1, 1]
}
```

**Key Hyperparameters:**
- `C`: Regularization (higher = less regularization, more complex boundary)
- `kernel`: 'linear', 'rbf', 'poly'
- `gamma`: RBF kernel coefficient (higher = more complex boundary)

#### 3.5.5 K-Nearest Neighbors (kNN)
```python
from sklearn.neighbors import KNeighborsClassifier

knn = KNeighborsClassifier(
    n_neighbors=5,
    weights='distance',  # 'uniform' or 'distance'
    metric='euclidean'
)
knn.fit(X_train, y_train)
```

**Key Hyperparameters:**
- `n_neighbors` (k): Number of neighbors to consider
    - k=1: Overfitting
    - k=N: Underfitting (predicts majority class)
- `weights`: 'uniform' (all equal) or 'distance' (closer = more weight)
- Requires normalized data!

**Grid Search for Optimal k:**
```python
from sklearn.model_selection import GridSearchCV

param_grid = {'n_neighbors': range(1, 101)}
grid_search = GridSearchCV(knn, param_grid, cv=5, scoring='roc_auc')
grid_search.fit(X_train, y_train)
optimal_k = grid_search.best_params_['n_neighbors']
```

---

### Model Evaluation Metrics
#### Confusion Matrix

|                     | Predicted Negative  | Predicted Positive  |
| ------------------- | ------------------- | ------------------- |
| **Actual Negative** | TN (True Negative)  | FP (False Positive) |
| **Actual Positive** | FN (False Negative) | TP (True Positive)  |

```python
from sklearn.metrics import confusion_matrix, classification_report

cm = confusion_matrix(y_test, y_pred)
print(classification_report(y_test, y_pred))
```
#### Accuracy

$$\text{Accuracy} = \frac{TP + TN}{TP + TN + FP + FN}$$

```python
from sklearn.metrics import accuracy_score
accuracy = accuracy_score(y_test, y_pred)
```

⚠️ Accuracy is misleading with imbalanced classes!
#### AUC-ROC
- **ROC Curve**: True Positive Rate vs False Positive Rate at various thresholds
- **AUC**: Area Under ROC Curve (0.5 = random, 1.0 = perfect)

```python
from sklearn.metrics import roc_auc_score, roc_curve

# Calculate AUC
auc = roc_auc_score(y_test, y_scores)

# Plot ROC curve
fpr, tpr, thresholds = roc_curve(y_test, y_scores)
plt.plot(fpr, tpr, label=f'AUC = {auc:.3f}')
plt.plot([0, 1], [0, 1], 'r--')  # diagonal
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.legend()
```

#### Cost-Based Evaluation
$$\text{Total Cost} = \text{Cost}_{FP} \times FP + \text{Cost}_{FN} \times FN$$

```python
cost_fp = 1  # Cost of False Positive
cost_fn = 5  # Cost of False Negative

tn, fp, fn, tp = confusion_matrix(y_test, y_pred).ravel()
total_cost = cost_fp * fp + cost_fn * fn
```

#### Lift Curve
- Measures how much better model is than random
- At top x%, how many positives captured vs random?

---
### 3.6 Model Comparison Summary

|Model|Pros|Cons|
|---|---|---|
|**Decision Tree**|Interpretable, handles non-linear|Overfits easily|
|**Logistic Regression**|Fast, interpretable coefficients|Assumes linearity|
|**Random Forest**|Robust, handles non-linear|Black box, slow|
|**SVM**|Effective in high dimensions|Slow, hard to tune|
|**kNN**|Simple, no training|Slow prediction, needs normalization|

---
## Chapter 4 - Tips, Tricks and Tools

### High-Cardinality Variables

**Problem**: Variables with many categories (40-100+) create too many dummies.

**Solutions:**

1. **Weight of Evidence (WoE):** $$WoE_i = \ln\left(\frac{Good_i / Total_{Good}}{Bad_i / Total_{Bad}}\right)$$
2. **Supervised Ratio:** $$Ratio_i = \frac{Good_i}{Bad_i}$$

```python
from feature_engine.encoding import WoEEncoder
encoder = WoEEncoder()
encoder.fit(X_train, y_train)
X_encoded = encoder.transform(X_train)
```
### K-Fold Cross-Validation

```python
from sklearn.model_selection import cross_val_score, KFold

kfold = KFold(n_splits=5, shuffle=True, random_state=42)
scores = cross_val_score(model, X, y, cv=kfold, scoring='roc_auc')
print(f"Mean AUC: {scores.mean():.3f} (+/- {scores.std():.3f})")
```
### GridSearch Best Practices

```python
from sklearn.model_selection import GridSearchCV

grid_search = GridSearchCV(
    estimator=model,
    param_grid=param_grid,
    cv=5,
    scoring='roc_auc',
    n_jobs=-1,      # Use all CPU cores
    verbose=2       # Show progress
)
```

**Tips:**
- Start with coarse grid (e.g., \[10, 100, 1000\]), then refine
- Use `RandomizedSearchCV` for large parameter spaces
- Don't tune unimportant parameters
### Ensemble Learning
Combine multiple classifiers:
```python
# Train multiple models, get their predictions
pred1 = model1.predict_proba(X)[:, 1]
pred2 = model2.predict_proba(X)[:, 1]
pred3 = model3.predict_proba(X)[:, 1]

# Stack as features for meta-model
stacked = np.column_stack([pred1, pred2, pred3])
meta_model.fit(stacked, y)
```
### Saving/Loading Models

```python
import pickle

# Save model
with open('model.pkl', 'wb') as f:
    pickle.dump(model, f)

# Load model
with open('model.pkl', 'rb') as f:
    loaded_model = pickle.load(f)
```
### Feature Importance

```python
# For tree-based models
importances = model.feature_importances_
feature_importance_df = pd.DataFrame({
    'feature': feature_names,
    'importance': importances
}).sort_values('importance', ascending=False)
```
### Project Architecture Recommendation

1. **Quick preprocessing** → Just make data usable
2. **Baseline models** → Train simple models without tuning
3. **Iterative improvement** → Improve preprocessing, check if scores improve
4. **Hyperparameter tuning** → Fine-tune best models
5. **Final evaluation** → Test on held-out test set
### Data Leakage Prevention

> ⚠️ **Critical**: Never use test data information during training!

- Calculate statistics (mean, mode, scaling parameters) on training data only
- Apply same transformations to validation/test data
- For final predictions: can retrain on full labeled data

---
## Quick Reference - Key sklearn Imports

```python
# Data splitting
from sklearn.model_selection import train_test_split, GridSearchCV, cross_val_score, KFold

# Preprocessing
from sklearn.preprocessing import StandardScaler, OneHotEncoder, LabelEncoder
from sklearn.impute import SimpleImputer
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer

# Models
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.svm import SVC
from sklearn.neighbors import KNeighborsClassifier

# Metrics
from sklearn.metrics import (
    accuracy_score, 
    roc_auc_score, 
    roc_curve,
    confusion_matrix,
    classification_report
)
```