- chapter to continue: modelling
- [Google Collab](https://colab.research.google.com/drive/1DG79gBlfI3_rwPX4eL3Jj-7j0fxGdzDq#scrollTo=L2I0R71EdDID) explaining the whole process with code examples
# What is CRISP-DM process?
- = Cross-Industry Standard Process for Data Mining
	- a process/framework for data mining or machine learning projects which consists of 6 stages and we repeatedly iterate through it
	- it's flexible, iterative (not linear)
![[Pasted image 20251014120404.png|500]]
### Business understanding
- understand the goal of the project, business objectives etc.
- define the success criteria, resources, contraints and risks
- the fun part: transforming the business problem into the data science problem!
	- all about creativity
### Data understanding/exploration
- collecting all initial data and understanding their structure
- exploring data quality issues, complexity, initial insights etc.
### Data preparation
- building/selecting/formatting the final dataset for modelling 
- cleaning data, handling missing values, outliers etc.
	- ! garbage in = garbage out (the same principle is with LLMs)
- feature engineering (= constructing new features)
- what are proxy data?
	- data collected for reasons other than our data science problem or just data collected for no reason at all
	- those data are often not in the right form we want it to be -> so a lot of data preparation
- quality data with labels are often costly
- dataset preprocessing: sampling, encoding, missing values, outliers
##### Sampling
- we define "population" - which is all possible data (e.g. all people on the world, all possibilities etc.)
	- and each dataset is a sample of population, and by sampling, we want to sample it even more (= pick specific data samples)
		- it has to be representative sample
- by sampling, we reduce computational costs and it allows us to focus on the business objective
	- focus on the future (by sampling only recent data, not long ago in the past)
	- focus on the positive class (bank_fraud = yes) that is really rare in the negative class (bank_fraud = no)
- strategies:
	- stratified sampling = divide population into subgroups and then sample proportionally from each subgroup
	- temporal sampling = sampling based on the time window (season sampling, recent data only sampling etc.)
	- random sampling = select instances uniformly at random
	- oversampling the positive class = when training for positive class detection and the positive class is really rare in the data
- problem of selective labels:
	- in some cases, we are sampling from an already biased dataset where labels only exist for a selected subset 
	- for example, when we train the model for predicting the job performance to help with the hiring decisions
		- we have data on job performance (and therefore samples) only on the hired applicants, which skews the data
			- we don't know, how the rejected applicants would perform at the job if they had been be hired
		- the outcome (=label) is only observable for the selected group (hired applicants)
		- outcome - the model learns only on the hired employees, but in practice, it must evaluate all applicants (including those similar to previously rejected applicants)
	- so we need to be aware of this problem - we cannot have good generalization with this biased data
		- there are techniques to resolve this (causal inference methods, propensity score weighting etc.)
##### Encoding
- turning features into more machine readable format
- examples:
	- continuous numerical features -> normalization (into some defined range)
		- could be min/max normalization OR standardization (mean and standard deviation)
	- continuous numerical features -> discretiazation to bins
		- equal-width partitioning OR equal-frequency partitioning
- categorical features
	- nominal features - encoding the categories that do not need to be sorted
		- e.g. one-hot encoding
	- ordinal features - encoding the features that have to be sorted (they have an order)
		- assign each category an unique integer based on it's order
	- beware of "high cardinality variables", a nominal variables with a large number of unique values (40+ unique values)
		- can be tackled with one-hot encoding, but that severaly increases the dimensionality
		- better solution: convert discrete variables into numeric values
			- tackled with Weight of Evidence approach
				- it connects the category values with the target values
				- so if I have a feature with 1000+ cities, for a concrete city = "Prague", I would calculate the occurence of "Prague" with target = 1 and the occurence of "Prague" with target = 0 and divide it inside a logartihm
					- then I get a useful numerous information instead of X city names
			- or another approach: Supervised ratio
				- just the mean of the target variable for this category
					- needs smoothing because of overfitting risk
##### Missing values
- "the fact that the value is missing can be informative"
- strategies:
	- keep - missing value is imporant information
		- we can add a boolean feature indicating if the value in other feature (= column) is missing or not
	- replace
		- compute the missing value (mean, median, mode (modus, for categorical features)) or predict with regression
		- could create bias in the data
		- the statistical values (mean, median etc.) should be calculated from the training set (not the test set) to avoid data leakage
	- delete
		- if the sample/feature has a lot of missing values, delete is
		- results in data loss
##### Outliers
- = values that are outside of the expected range
- could be found using boxplots or computing the z-score (the standardization (mean and deviation)) and outliers have `|z > 3|` 
### Modelling
- selecting the right evaluation model for the data (clustering, classification, regression, ANN, GBDT, anomaly detection etc.)
- tips:
	- using `GridSearchCV` or `RandomizedGridSearchCV` (not better, but faster) for systematic tuning of the parameters
		- each model has more and less important parameters, we should always focus on the more important ones first
		- start with broad selection of values for the first iteration, like `C= [0.0001, 0.001, 0.01, 0.1, 1, 10, 100, 1000]` and if I get `C=100`, then I run again with values closer to 100
	- for validation, use `Cross validation`, do not use the test set
	- use pipelines for standardized and reusable data preprocessing from raw data to modelling-ready data
	- watch data importance to see, which features contribute to the prediction or not
### Evaluation
- checking if the learned data and model outputs are valid and reliable
### Deployment
- deploying the model in production
- done by machine learning engineers