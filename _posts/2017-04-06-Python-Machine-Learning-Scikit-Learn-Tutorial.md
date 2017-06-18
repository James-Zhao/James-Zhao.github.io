---
layout: post
title:  Python Machine Learning: Scikit-Learn Tutorial
date:   2017-04-06 22:17:00
---
这是一篇翻译的博客，原文链接在[这里](https://www.datacamp.com/community/blog/scikit-learn-cheat-sheet#gs.BkcjXqk)。这是我看的为数不多的介绍scikit-learn简介而全面的文章，特别适合入门。我这里把这篇文章翻译一下，英语好的同学可以直接看原文。

大部分喜欢用Python来学习数据科学的人，应该听过scikit-learn，这个开源的Python库帮我们实现了一系列有关机器学习，数据处理，交叉验证和可视化的算法。其提供的接口非常好用。

这就是为什么DataCamp(原网站)要为那些已经开始学习Python库却没有一个简明且方便的总结的人提供这个总结。（原文是cheat sheet，翻译过来就是小抄，我这里翻译成总结，感觉意思上更积极点）。或者你压根都不知道scikit-learn如何使用，那这份总结将会帮助你快速的了解其相关的基本知识，让你快速上手。

你会发现，当你处理机器学习问题时，scikit-learn简直就是神器。

这份scikit-learn总结将会介绍一些基本步骤让你快速实现机器学习算法，主要包括：读取数据，数据预处理，如何创建模型来拟合数据，如何验证你的模型以及如何调参让模型变得更好。

![](http://community.datacamp.com.s3.amazonaws.com/community/production/ckeditor_assets/pictures/294/content_button-cheatsheet-scikit_1.png)

总的来说，这份总结将会通过示例代码让你开始你的数据科学项目，你能立刻创建模型，验证模型，调试模型。（原文提供了pdf版的下载，内容和原文差不多）

## A Basic Example
```
>>> from sklearn import neighbors, datasets, preprocessing
>>> from sklearn.cross_validation import train_test_split
>>> from sklearn.metrics import accuracy_score
>>> iris = datasets.load_iris()
>>> X, y = iris.data[:, :2], iris.target
>>> X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=33)
>>> scaler = preprocessing.StandardScaler().fit(X_train)
>>> X_train = scaler.transform(X_train)
>>> X_test = scaler.transform(X_test)
>>> knn = neighbors.KNeighborsClassifier(n_neighbors=5)
>>> knn.fit(X_train, y_train)
>>> y_pred = knn.predict(X_test)
>>> accuracy_score(y_test, y_pred)
```
（补充，这里看不懂不要紧，其实就是个小例子，后面会详细解答）

## Loading The Data
你的数据需要是numeric类型，然后存储成numpy数组或者scipy稀疏矩阵。我们也接受其他能转换成numeric数组的类型，比如Pandas的DataFrame。
```
>>> import numpy as np
>>> X = np.random.random((10,5))
>>> y = np.array(['M','M','F','F','M','F','M','M','F','F','F'])
>>> X[X < 0.7] = 0
```

## Preprocessing The Data
### Standardization
```
>>> from sklearn.preprocessing import StandardScaler
>>> scaler = StandardScaler().fit(X_train)
>>> standardized_X = scaler.transform(X_train)
>>> standardized_X_test = scaler.transform(X_test)
```
### Normalization
```
>>> from sklearn.preprocessing import Normalizer
>>> scaler = Normalizer().fit(X_train)
>>> normalized_X = scaler.transform(X_train)
>>> normalized_X_test = scaler.transform(X_test)
```
### Binarization
```
>>> from sklearn.preprocessing import Binarizer
>>> binarizer = Binarizer(threshold=0.0).fit(X)
>>> binary_X = binarizer.transform(X)
```
### Encoding Categorical Features
```
>>> from sklearn.preprocessing import LabelEncoder
>>> enc = LabelEncoder()
>>> y = enc.fit_transform(y)
```
### Imputing Missing Values
```
>>>from sklearn.preprocessing import Imputer
>>>imp = Imputer(missing_values=0, strategy='mean', axis=0)
>>>imp.fit_transform(X_train)
```
### Generating Polynomial Features
```
>>> from sklearn.preprocessing import PolynomialFeatures)
>>> poly = PolynomialFeatures(5))
>>> oly.fit_transform(X))
```

## Training And Test Data
```
>>> from sklearn.cross_validation import train_test_split)
>>> X_train, X_test, y_train, y_test = train_test_split(X,y,random_state=0))
```

## Create Your Model
### Supervised Learning Estimators
### Linear Regression
```
>>> from sklearn.linear_model import LinearRegression)
>>> lr = LinearRegression(normalize=True))

```
### Support Vector Machines (SVM)
```
>>> from sklearn.svm import SVC)
>>> svc = SVC(kernel='linear'))
```
### Naive Bayes
```
>>> from sklearn.naive_bayes import GaussianNB)
>>> gnb = GaussianNB())
```
### KNN
```
>>> from sklearn import neighbors)
>>> knn = neighbors.KNeighborsClassifier(n_neighbors=5))
```

### Unsupervised Learning Estimators
### Principal Component Analysis (PCA)
```
>>> from sklearn.decomposition import PCA)
>>> pca = PCA(n_components=0.95))
```
### K Means
```
>>> from sklearn.cluster import KMeans)
>>> k_means = KMeans(n_clusters=3, random_state=0))
```
## Model Fitting
### Supervised learning
```
>>> lr.fit(X, y))
>>> knn.fit(X_train, y_train))
>>> svc.fit(X_train, y_train))
```
### Unsupervised Learning
```
>>> k_means.fit(X_train))
>>> pca_model = pca.fit_transform(X_train))
```

## Prediction
### Supervised Estimators
```
>>> y_pred = svc.predict(np.random.random((2,5))))
```
```
>>> y_pred = lr.predict(X_test))
```
```
>>> y_pred = knn.predict_proba(X_test))
```
### Unsupervised Estimators
```
>>> y_pred = k_means.predict(X_test))
```

## Evaluate Your Model's Performance
### Classification Metrics
### Accuracy Score
```
>>> knn.score(X_test, y_test))
>>> from sklearn.metrics import accuracy_score)
>>> accuracy_score(y_test, y_pred))
```

### Classification Report
```
>>> from sklearn.metrics import classification_report)
>>> print(classification_report(y_test, y_pred)))
```


### Confusion Matrix
```
>>> from sklearn.metrics import confusion_matrix)
>>> print(confusion_matrix(y_test, y_pred)))
```
### Regression Metrics
### Mean Absolute Error
```
>>> from sklearn.metrics import mean_absolute_error)
>>> y_true = [3, -0.5, 2])
>>> mean_absolute_error(y_true, y_pred))
```

### Mean Squared Error
```
>>> from sklearn.metrics import mean_squared_error)
>>> mean_squared_error(y_test, y_pred))
```
### R2 Score
```
>>> from sklearn.metrics import r2_score)
>>> r2_score(y_true, y_pred))
```

### Clustering Metrics
### Adjusted Rand Index
```
>>> from sklearn.metrics import adjusted_rand_score)
>>> adjusted_rand_score(y_true, y_pred))
```
### Homogeneity
```
>>> from sklearn.metrics import homogeneity_score)
>>> homogeneity_score(y_true, y_pred))
```
### V-measure
```
>>> from sklearn.metrics import v_measure_score)
>>> metrics.v_measure_score(y_true, y_pred))
```
### Cross-Validation
```
>>> print(cross_val_score(knn, X_train, y_train, cv=4))
>>> print(cross_val_score(lr, X, y, cv=2))
```

## Tune Your Model
### Grid Search
```
>>> from sklearn.grid_search import GridSearchCV
>>> params = {"n_neighbors": np.arange(1,3), "metric": ["euclidean", "cityblock"]}
>>> grid = GridSearchCV(estimator=knn,param_grid=params)
>>> grid.fit(X_train, y_train)
>>> print(grid.best_score_)
>>> print(grid.best_estimator_.n_neighbors)
```
### Randomized Parameter Optimization
```
>>> from sklearn.grid_search import RandomizedSearchCV
>>> params = {"n_neighbors": range(1,5), "weights": ["uniform", "distance"]}
>>> rsearch = RandomizedSearchCV(estimator=knn,
   param_distributions=params,
   cv=4,
   n_iter=8,
   random_state=5)
>>> rsearch.fit(X_train, y_train)
>>> print(rsearch.best_score_)
```

## Going Further
学习完上面的例子后，你可以通过[our scikit-learn tutorial for beginners](https://www.datacamp.com/community/tutorials/machine-learning-python#gs.mDi3ekM)来学习更多的例子。另外你可以学习matplotlib来可视化数据。

不要错过后续教程 [Bokeh cheat sheet](https://www.datacamp.com/community/blog/bokeh-cheat-sheet-python), [the Pandas cheat sheet](https://www.datacamp.com/community/blog/python-pandas-cheat-sheet#gs.0dYnfNg) or [the Python cheat sheet for data science](https://www.datacamp.com/community/tutorials/python-data-science-cheat-sheet-basics#gs.Eekdm2k).
