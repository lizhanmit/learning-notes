# Machine Learning Note

- [Machine Learning Note](#machine-learning-note)
  - [Linear Regression](#linear-regression)
  - [Classification](#classification)
    - [Naive Bayes Classifier](#naive-bayes-classifier)
  - [Clustering](#clustering)
  - [Coding](#coding)
    - [General Processes](#general-processes)

---

## Linear Regression 

- Never a bad place to start. 
- Can even think about linear regression as a form of Exploratory Data Analysis (EDA). 
- Even against some non-linear data it performs ok (can be the first guess). 

Biggest takeaways from classic linear regression: 

- Use cross-fold validation techniques. 
- Judge performance using metrics like RMSE (Root Mean Square Error) vs R-Squared and P-Value. 

There is no guarantee that cross-validation will improve the accuracy of the model. But it might. 

---

## Classification 

One of the classifiers that is commonly used in spam filters is Naive Bayes classifier.

### Naive Bayes Classifier

The probability of a label given some observed features. 

---

## Clustering

---

## Coding

### General Processes

Take linear regression as an example.

```python
# 1. Find N/A
df.shape

df.isnull().values.any()

# if the result of the above is True
df = df.dropna()
df.isnull().values.any()  # result should be False

# see how many rows were dropped
df.shape

# 2. Clean
df.rename(index=str, columns={"Height(inches)": "Height", "Weight(pounds)": "Weight"}, inplace=True)

df.head()

# 3. Exploratory Data Analysis (EDA)
df.describe()

# 4. Model
from sklearn import linear_model
from sklearn.model_selection import train_test_split

## 4.1 Create Features
var = df['Weight'].values
var.shape()

y = df['Weight'].values # Target
y = y.reshape(-1, 1)
X = df['Height'].values # Feature(s)
X = X.reshape(-1, 1)

## 4.2 Split Data
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

## 4.3 Fit the Model
lm = linear_model.LinearRegression()
model = lm.fit(X_train, y_train)
predictions = lm.predict(X_test)

# 5. Plot Predications
from matplotlib import pyplot as plt

plt.scatter(y_test, predictions) 
plt.xlabel("Actual Weight")
plt.ylable("Predicted Weight")

# 6. Print Accuracy of Linear Regression Model
model.score(X_test, y_test)

# 7. Use Cross-validation 
from sklearn.model_selection import cross_val_score, cross_val_predict
from sklearn import metrics

scores = cross_val_score(model, X, y, cv=6)
scores

## 7.1 Plot Cross-validation Predictions 
predictions = cross_val_predict(model, X, y, cv=6)
plt.scatter(y, predictions)

## 7.2 Print Accuracy 
accuracy = metrics.r2_score(y, predictions)
accuracy
```





