# Machine Learning Note

- [Machine Learning Note](#machine-learning-note)
  - [Linear Regression](#linear-regression)
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


```





