## The accuracy of a classifier is calculated as…
- A) The number of positive outcomes divided by the number of negative outcomes.
- **B) Number of correctly classified samples / Total number of samples classified.** ✅
- C) The number of true positives divided by the total number of positive samples.
- D) The sum of the true positives and true negatives divided by the number of false positives and false negatives.
- **E) The total number of samples minus the number of misclassified samples divided by the total number of samples.** ✅
- why? See [[MLB 5. lecture - How to choose the best model a visualize it's performance#Accuracy]]
	- B and E are mathematically equivalent (an error mentioned in Errata)
- C describes recall (how many of the true positives were actually recalled?)
*** 
### To apply the Apriori algorithm to mine association rules from a dataset, we need to compute:
- A) Support and lift for each possible association rule
- B) Support for each possible association rule
- C) Lift and confidence for each possible association rule
- D) Lift for each possible association rule
- **E) Support and confidence for each possible association rule** ✅
- why? See [[MLB 8. lecture - Text and Association rules Mining#Apriori algorithm]]
- the Lift calculation is for evaluation of the rule quality (but it is not needed for an Apriori algorithm)
***
### Question What will the following code output?
```python
ages = [21, 24, 20, 26, 27, 19]
print(ages[0:6:3]) 
```
- A) `[21, 20, 27]`
- **B) `[21, 26]`** ✅
- C) `[21, 24, 20, 26, 27, 19]` 
- D) `[26]` 
- E) `[21, 27]`
- why? The slice begins from index 0, ends before index 6 and the step is 3
***
### Consider the market basket data presented in table below:

| Transaction | Items |
|-------------|-------|
| 1 | plums, lettuce, tomatoes |
| 2 | celery, confectionery |
| 3 | confectionery |
| 4 | apples, carrots, tomatoes, potatoes, confectionery |
| 5 | apples, oranges, lettuce, tomatoes, confectionery |
| 6 | peaches, oranges, celery, potatoes |
| 7 | beans, lettuce, tomatoes |
| 8 | oranges, lettuce, carrots, tomatoes, confectionery |
| 9 | apples, bananas, plums, carrots, tomatoes, onions, confectionery |
| 10 | apples, potatoes |

Which of the following statements about this data is true?

- A) {lettuce, tomatoes} is a transaction in the dataset.
- **B) {lettuce, tomatoes} has a support of 0.4.** ✅
- C) {lettuce, tomatoes} has a confidence of 1.
- D) {lettuce} → {tomatoes} has a confidence of 0.4.
- E) None of the above.
- why? See [[MLB 8. lecture - Text and Association rules Mining#Association rule]]
***
### Which of the following code snippets fails to output Kevin G.'s major and minor correctly?

- A)
```python
name = 'Kevin G.'
print(f'Student {name} has {grades[name][1]} as major and {grades[name][2]} as minor')
```

- **B)** ✅
```python
name = 'Kevin G.'
print(f"Student {name} has {grades[name].get(1)} as major and {grades[name].get(2)} as minor")
```

- C)
```python
name = 'Kevin G.'
print('Student {} has {} as major and {} as minor'.format(name, grades[name][1], grades[name][2]))
```
- why? `.get()` function is for dictionaries, not lists 
	- I could use `grades.get(name)`, but not `grades[name].get(1)`
***
### Determine the sentence differences
You would like to determine how different the sentence "Do you get déjà vu when she's with you?" is from "Do you get déjà vu? Oh". Assume the sentences have been tokenized and vectorized into word embeddings. Which similarity metric would you use? 
- A) Euclidean distance 
- B) Manhattan distance 
- **C) Cosine distance** ✅
- D) Jaccard distance 
- E) Hamming distance
- why? Euclidean and Mannhattan distances are sensitive to vector lenghts (the sentences are not of equal length and it may cause inprecisions), Jaccard distance is for comparing sets and Hamming is used for comparing binary strings or sets of equal length
- Cosine distance measures the angle between vectors, capturing the semantic orientation rather than absolute position in vector space
- see [[MLB 7. lecture - Similarity, Neighbors and Clusters]]
***
### kNN classification
You are using the k-nearest neighbors (k-NN) algorithm for classification. You evaluate various classifiers with different values of k. Which of the following statements about the relationship between k and the bias-variance tradeoff in k-NN is true?

- A) Increasing k decreases both bias and variance, improving model performance.
- B) Decreasing k increases bias and decreases variance, leading to underfitting.
- **C) Increasing k decreases variance but increases bias, leading to a smoother decision boundary.** ✅
- **D) Decreasing k decreases bias and increases variance, leading to a more complex decision boundary.** ✅
- E) Increasing k has no impact on the bias-variance tradeoff, it only affects the runtime of the algorithm.
- see [[MLB 7. lecture - Similarity, Neighbors and Clusters#kNN]]
- see [[MLB 3., 4. and 5. lecture - Classification#Bias-variance trade-off]]
	- high bias = oversimplification of the model, not capturing all important features, underfitting
	- high variance = random noise modelling, overfitting, not generalizing enough
	- we want low bias and low variance (but it's a trade-off)
	- in this example:
		- small k = model looks on near neighbors only, decision boundary is very complex
			- small bias (captures local patterns), but high variance (low generalization)
		- high k = model averages (majority) on a lot of neighbors, decision boundary is smooth
			- high bias (can ignore important local patterns), low variance (good generalization)
- C and D are both correct, they say the same thing
***
### What is the issue with using `fit_transform` on the test set using scikit-learn's data preprocessing method here?
```python
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.fit_transform(X_test)
```
- A) It will lead to overfitting on the test set.
- B) It will result in an error since `fit_transform` can only be applied to the training set.
- C) There is no issue, this is a recommended practice for ensuring consistency between the training and test sets.
- **D) It will cause data leakage and compromise the model's generalization.** ✅
- E) It will amplify outliers in the test set, leading to biased predictions.
- why?
	- we want to fit and transform the training dataset, that's correct
	- but we want to only transform the testing dataset (so it is transformed only on statistical data from training dataset)
	- the example shows data leakage (statistical information from testing "leaks" to the training) and also the `MinMaxScaler` "forgets" statistics from the training data and applies only the test training data statistics