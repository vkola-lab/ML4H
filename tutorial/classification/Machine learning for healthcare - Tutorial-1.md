# Topic: Build a classification decision tree

> [!tip] Find more about our research
> [Kolachalama Lab](https://vkola-lab.github.io/)


In machine learning, a classification task is a type of supervised learning problem where the goal is to predict a categorical label or class that an instance belongs to, based on its features or characteristics. In other words, the model is trained to assign a label or category to a given input data point, from a predefined set of labels or categories.

💻 Download and play around with the code here: [link](https://github.com/vkola-lab/ML4H/blob/main/tutorial/classification/dementia_classification.ipynb)

In this module, we illustrate a simple introduction to binary classification (🤔 *What about multi-class? Something to ask [PodGPT](https://podgpt.org/)* 👨🏻‍💻). We take a simple table of $16959$ patients from [NACC](https://naccdata.org/requesting-data/nacc-data) dataset to dig into this algorithm.
- The dataset looks like this - (download [here](https://github.com/vkola-lab/ML4H/blob/main/tutorial/classification/dementia.csv))

| Age  | MMSE Score | BOSTON | Label |
| ---- | ---------- | ------ | ----- |
| 87.0 | 19.0       | 6.0    | AD    |
| 78.0 | 18.0       | 24.0   | AD    |
| 86.0 | 16.0       | 6.0    | AD    |
| 92.0 | 30.0       | 23.0   | NC    |
| 84.0 | 20.0       | 26.0   | AD    |

> [!Try out!] Prompt: What is multi-classification in machine learning for healthcare?
> Follow up with more questions:
> - Can we determine which patients are in need of immediate care?
> - What is a tree based classifier?

### Data
Now, that we have the dataset, we can use the `pandas` library to read it. How? Here's a small snippet to do that.
```
import pandas as pd

dementia = pd.read_csv("./dementia.csv")
dementia.columns = ["Age", "MMSE Score", "BOSTON", "Label"]
feature_columns = ["Age", "MMSE Score"]
target_column = "Label"
```

> [!NOTE] More on `pandas` ...
> Install in your local machine:
> - Ask [PodGPT](https://podgpt.org/): How to install pandas in my Windows (or Linux) machine?
> What if your dataset does not end with `csv` but with `xlsx`/`xls`?
> - [PodGPT](https://podgpt.org/) to help: How do I read a xlsx/xls file using pandas in python?

### Exploratory Analysis
Let's take a look at few rows of the dataset and their stats:
```
print("The first 5 patient features and diagnosis:\n")
print(dementia.head())
print("\nThe patient features' statistics:\n")
print(dementia.describe())
```
Well, the `Label` column contains the diagnosis `{NC, AD}` but that is difficult for our machine/model to understand. So to simplify we can encode them into numbers, say `{NC: 0, AD: 1}`.
```
dementia['Label'].replace(['NC', 'AD'], [0, 1], inplace=True)
```

🤔 Does the data suggest: "Age and MMSE score are correlated? Can they help predict Alzheimer's diagnosis?". Let's plot 📊 them:
```
dementia.plot.scatter(x='Age', y='MMSE Score')
```
![[age_mmse_scatter.png]]
There looks like a MMSE score which can separate out the two classes, but how do we determine this value?
> [!NOTE] Verify with [PodGPT](https://podgpt.org/) and ask more
> - Is age and MMSE score correlated in dementia diagnosis?
> - 💡 How do we determine which one is more important for the classification model to correctly diagnose?
> - What are the different ways of encoding categorical data?

### Train/Test
First, we split the data into two subsets to investigate how our models predict values based on unseen data. To do this, we need a new library `scikit-learn`/`sklearn`. We learn our models on the training data, and then test it on the unseen data.
```
from sklearn.model_selection import train_test_split

data, target = dementia[feature_columns], dementia[target_column]
data_train, data_test, target_train, target_test = train_test_split(data, target, random_state=42)
```

> [!NOTE] More on `scikit-learn` ...
> Install in your local machine:
> - Ask [PodGPT](https://podgpt.org/[]()): How to install scikit-learn in my Windows (or Linux) machine?
> - What does the train_test_split function do in scikit-learn?

### Linear Classification
Let's start with a simple linear classifier - **Logistic Regression**. Linear classifiers define a linear separation to split classes using a linear combination of the input features. In our 2-dimensional feature space, it means that a linear classifier finds the oblique lines that best separate the classes. This is still true for multi-class problems, except that more than one line is fitted.
```
from sklearn.linear_model import LogisticRegression

linear_model = LogisticRegression()
linear_model.fit(data_train, target_train)
```

> [!NOTE] Wanna know more about how logistic regression works?
> Ask [PodGPT](https://podgpt.org/): What is the mathematical form of logistic regression? How does it work?

We can visualise how the data is divided into boundaries to understand the model's behaviour. We can use `DecisionBoundaryDisplay` to do that.
```
import matplotlib.pyplot as plt
import matplotlib as mpl
import seaborn as sns

from sklearn.inspection import DecisionBoundaryDisplay
  
tab10_norm = mpl.colors.Normalize(vmin=-0.5, vmax=8.5)
# create a palette to be used in the scatterplot
palette = ["tab:blue", "tab:green", "tab:orange"]

dbd = DecisionBoundaryDisplay.from_estimator(
	linear_model,
	data_train,
	response_method="predict",
	cmap="tab10",
	norm=tab10_norm,
	alpha=0.5,
)

sns.scatterplot(
	data=dementia,
	x=feature_columns[0],
	y=feature_columns[1],
	hue=target_column,
	palette=palette,
)

# put the legend outside the plot
plt.legend(bbox_to_anchor=(1.05, 1), loc="upper left")
_ = plt.title("Decision boundary using a logistic regression")
```
![[age_mmse_lr_dbd.png]]
> [!NOTE] Curious about the bunch of functions and their parameters?
> Ask [PodGPT](https://podgpt.org/): What is DecisionBoundaryDisplay function?

Looks like the logistic regression does a pretty good job in finding a horizontal line that can separate the two classes. Let's take a look at the accuracy of our model in the test data that we had kept aside.
```
linear_model.fit(data_train, target_train)
test_score = linear_model.score(data_test, target_test)
print(f"Accuracy of the LogisticRegression: {test_score:.2f}")
```
Ah! We get $90\%$ accurate results. That's not bad. But let's look at a tree based model now.

### Tree Classifier - Decision Tree
Unlike linear models, the decision rule for the decision tree is not controlled by a simple linear combination of weights and feature values.
Instead, the decision rules of trees can be defined in terms of
- the feature index used at each split node of the tree,
- the threshold value used at each split node,
- the value to predict at each leaf node.

Decision trees partition the feature space by considering a single feature at a time. The number of splits depends on both the hyperparameters and the number of data points in the training set: the more flexible the hyperparameters and the larger the training set, the more splits can be considered by the model.

As the number of adjustable components taking part in the decision rule changes with the training size, we say that decision trees are non-parametric models.

> [!NOTE] More about tree? What about forests?
> Ask [PodGPT](https://podgpt.org/): 
> - What is a decision tree? 
> - What are the different algorithm based on trees? What are their limitations?
> - Are trees better to interpret? Are they preferred in healthcare problems?

Let’s now visualise the shape of the decision boundary of a decision tree when we set the `max_depth` hyperparameter to only allow for a single split to partition the feature space.
```
from sklearn.tree import DecisionTreeClassifier

# Fit the Decision Tree
tree = DecisionTreeClassifier(max_depth=1)
tree.fit(data_train, target_train)

# Display the boundaries
DecisionBoundaryDisplay.from_estimator(
	tree,
	data_train,
	response_method="predict",
	cmap="tab10",
	norm=tab10_norm,
	alpha=0.5,
)

sns.scatterplot(
	data=dementia,
	x=feature_columns[0],
	y=feature_columns[1],
	hue=target_column,
	palette=palette,
)

plt.legend(bbox_to_anchor=(1.05, 1), loc="upper left")
_ = plt.title("Decision boundary using a decision tree")
```
![[age_mmse_dt_dbd.png]]
The partitions found by the algorithm, similar to the logistic regression, separates the data along the axis “MMSE”, discarding the feature “Age”. Thus, it highlights that a decision tree is not using a combination of features when making a single split. Looking into the tree structure - 
```
from sklearn.tree import plot_tree

_, ax = plt.subplots(figsize=(8, 6))
_ = plot_tree(
	tree,
	feature_names=feature_columns,
	class_names=["NC", "AD"],
	filled=True,
	impurity=False,
	ax=ax,
)
```

![[dt_model.png]]
The tree found with a maximum depth of 1 tries to separate the two labels with a threshold of $26.5$ on the MMSE Score. With this logic the model accuracy is comparable when compared to the linear model.

```
tree.fit(data_train, target_train)
test_score = tree.score(data_test, target_test)
print(f"Accuracy of the DecisionTreeClassifier: {test_score:.2f}")
```
