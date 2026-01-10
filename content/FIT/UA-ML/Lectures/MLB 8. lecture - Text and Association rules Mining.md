- this has a lot of applications, because text is everywhere
# Text mining
### Structured vs. unstructured data
- structured (in databases, with data types, columns, rows etc.), are easy to process, retrieve and analyze, no need to preprocess them much before classifying
- unstructured data (text, audio, video etc.), meant for humans, not machines, 80 % of data out there, requires extensive preprocessing before classifying
### How to preprocess text?
- different names for the same thing, grammar mistakes, extensive use of punctuation, sarcasm etc. 
- common techniques:
	- text normalization = everything in lowercase
	- stemming = reducing words to their word stem or base form
		- disappointing, dissapoint, disappointment -> disappoint
	- lemmatization = reducing words to a lemma (dictionary form)
		- stemming: humbling, humbled -> humbl
		- lemmatization: humbling, humbled -> humble
	- stop-word removal = remove frequent words on a stoplist (the, a, an, of, to, if, and ...)
- terminology:
	- instances are documents, collections of documents is called a corpus
	- a document (which could be just one sentence) consists of terms/tokens
### BoG = Bag of Words 
- treats each document as literally "bag of words", a set of preprocessed words (lowercase, stemming etc.)
- tranformation to feature vectors:
	- 1) term occurence representation (for each word from corpus, mark 1 as word in this document)
	- 2) term count/frequency representation (each word has a number of occurences in the document)
		- can by by absolute numbers or normalized (divided by the total number of words in the document)
### N-gram
- an extension of bag of words that takes into account the order of words, storing groups of adjacent words 
- examples:
	- 1-gram: {The, quick, brown, fox, jumps, over, the, …}
	- 2-gram: {The quick, quick brown, brown fox, fox jumps, …}
	- 3-gram: {The quick brown, quick brown fox, brown fox jumps, …}
- problem: adding N-grams greatly increases the size of the feature set
	- solution: only add the most important (are frequent and have a high lift) bigrams and trigrams
### TF-IDF
- [[Vektorový vyhledávací model#Rozdělení váh termů]]
- TF = term frequency (in the current document)
- IDF = inverse term frequency (in the whole corpus)
	- words with IDF = 1 occur in all documents in the corpus
	- words with high IDF occur in one, two documents max (very rare words)
	- IDF = 1 + log(total number of documents in the corpus / number of documents containing term $t$)
- final score for term $t$: $TF(t, D)*IDF(t)$ 
- often the [[MLB 7. lecture - Similarity, Neighbors and Clusters#Cosine similarity|Cosine Similarity]] is used for determining the relevance of the search query to a corpus of documents
	- each document has a vector if TF-IDF scores for each term in that document
	- the query also has a TF-IDF vector and the similarity of those vectors is calculated
### Named entity recognition (NER)
- we want to identify the named entities (locations, countries, product names, political groups, dates etc) in the text
	- those terms are often very meaningful
- methods:
	- expertly maintained entity dictionaries
	- NER models trained for this purpose (on a labeled training dataset)
- open-source library for Python: SpaCy
### Topic models
- they form another layer next to documents and terms and they define topics for the whole corpus and then words could be related to some topics, which also enhanced the information quality over the corpus and documents
	- usually the corpus deals with a limited number of topics
- ![[Pasted image 20260109123631.png|600]]
### Word embeddings
- they model words as dense vector representations in a low-dimensional space
- the idea: words with similar meaning will have a similar representation
	- therefore, they will be closer together
- methods:
	- Word2Vec, GloVe etc.
- existing pre-trained word embedding models: BERT, fasttext, GloVe
	- beware of the biases there 
	- it often connects men to programmers and women to homemakers 
# Association rule mining
- examples: what products are purchased together, plagiarism detection, related concepts identification etc.
### Terminology
- itemset = a set of items 
	- which often occur together in individual transactions (also called frequent itemsets)
	- which are not together in any transaction
### Association rule
- it indicates an affinity between the antecedent itemset and consequent itemset
	- affinity = how strongly do these two items occur together
- it consists of two itemsets:
	- antecedent = left-hand side (LHS)
	- consequent = right-hand side (RHS)
- the translation is like this: IF antecendent happens -> then the consequent happens (usually) as well
- metrics:
	- 1) support = "how often do the antecedent and consequent appear together?"
		- if there are 100 transactions and `{milk, butter, apples}` are all in 20 transactions, the support is 20/100 = 0.2
	- 2) confidence = "when A occurs, how often does C occur as well?"
		- if there are 100 transactions, A (antecedent) is in 20 of them and C (consequent) is in 10 out of those 20
		- the confidence(A->C) = support($A\cup B$) / support(A) = (10/100) / (20/100) = 0.1/0.2 = 0.5
	- 3) lift = "how much likely is C when A is present, compared to C occuring randomly?"
		- measures the level of interestingness of the association rule
		- the lift(A->C) = confidence(A->C) / support(C)
		- if lift = 1 -> A and C are independent of each other
		- if lift > 0 -> C is more frequent in transactions that also contain A
		- this is not the same lift as in [[MLB 6. lecture - Naive Bayes + Support Vector Machines + Random Trees]]
### Apriori algorithm
- for generating frequent association rules that have support(A->C) $\gt$ minsup and confidence (A->C) $\gt$ minconf
	- the values of minsup and minconf are determined by a data scientist upfront
- the apriori property (must be satisfied): in all nonempty subsets of a frequent itemset must be also frequent:
	- $support(A) > support(A ∪ C)\ \&\ support(C) > support(A ∪ C)$
	- if  `{apples, confectionery, oranges}` is a frequent itemset, so are `{apples, confectionery}, {apples, oranges}, {oranges, confectionery}...` 
- the algorithm:
	- 1) find one-item-itemsets with a support of greater than the given minsup
	- 2) in a loop:
		- generate all possible itemsets of $k=2$ items using only the items from itemsets of 1
		- count, how often they occur in the dataset and save only those, which are above the minsup threshold
		- and again for $k=3$:
			- we must remember the rule that all nonempty subsets of the itemset must also be frequent (so above the threshold minsup)
	- 3) reuturn all frequent itemsets (of various lengths)
	- 4) for all itemsets of length 2 (= minimum lenght to generate an association rule) and generate all possible rules A->C (both must be non-empty)
	- 5) for all generated rules, determine, if the confidence(A->C) is above a specified minconf threshold
