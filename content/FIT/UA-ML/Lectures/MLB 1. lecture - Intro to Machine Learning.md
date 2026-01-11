### Different areas
- **Data science** - process of extracting knowledge and insights from data (using statistics, computer science and domain expertise)
	- the broadest topic in MLB
- **Data analytics** - process of examining, cleaning, transforming and modeling data in order to discover useful information and support decision-making
	- most manual of all
- **Data mining** - process of discovering patterns, anomalies and relationships in large datasets
- **Machine learning** - process of building algorithms that can learn from data and predict the future values without being explicitly programmed to do so
- **Artificial intelligence** - the art of creating systems to perform tasks where human intelligence is required (reasoning, learning and perception)
- **General AI** - theoretical form of AI that can perform any task a human can with general reasoning, learning abilities across domains
- **Big Data** - see [[Big Data]]
- **Data engineering** - discipline of designing and building systems that can collect, store and process big data efficiently and reliably
	- really important for any data analysis or machine learning
- **Machine learning engineering** - the practice of implementing, deploying and maintaining ML models in production environments
- **Data governance** - set of policies, processes and standards that ensure data is accurate, secure and used responsibly across the whole organization
***
### Structured data
- stored in tables (rows, columns)
- relational database
### Unstructured data
- data primarily meant for humans, not machines (images, videos, audio, text)
- needs to be preprocessed before use in a classifier
- 80 % of the data on the internet
### Semi-structured data
- half way in the middle :) 
- it gives some structure to unstructured data (e.g. text labelling)
	- but the structure is not that rigid as structured data
- XML, HTML, JSON, YAML
***
### Key concepts in ML
- features
	- = input variables, could be boolean features, categorical features etc.
	- used as input variables to predict the outcome I am interested in
- instance/sample
	- individual observation in the dataset, described by a set of features 
- labels/targets
	- the predicted/outcome variable
	- label in classification, target in regression
***
### Supervised learning
- predicting the output variable (target/label) based on previous (already labelled data)
- labelling past data sometimes requires human labour
##### Classification
- predicting the category or class label based on previously labelled data
- we have binary classification (1 - is sick, 0 - is not sick) or multi-label classification
##### Regression
- predicting the continuous numerical value (based on previous data with continuous numerical value outcomes)
### Unsupervised learning
- we have only the matrix X of samples and features (no vector of outcomes Y)
- for finding patterns and relationships
##### Clustering
- grouping data points together based on similar features
- [[MLB 7. lecture - Similarity, Neighbors and Clusters#Agglomerative/hierarchical clustering]]
- [[MLB 7. lecture - Similarity, Neighbors and Clusters#k-Means clustering]]
##### Pattern mining
- for finding patterns, associations, relationships in large datasets
- e.g. finding items that are often purchased together
- [[MLB 8. lecture - Text and Association rules mining#Association rule mining]]