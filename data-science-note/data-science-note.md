# Data Science Note

- [Data Science Note](#data-science-note)
  - [Basics](#basics)
    - [Quick Look](#quick-look)
    - [Supervised Learning](#supervised-learning)
    - [Unsupervised Learning](#unsupervised-learning)
    - [Reinforcement Learning](#reinforcement-learning)
    - [Model Hyperparameters](#model-hyperparameters)
  - [Regression](#regression)
    - [The Method of Least Squares](#the-method-of-least-squares)
    - [Logarithmic Transformations of Variables](#logarithmic-transformations-of-variables)
    - [Correlation Matrices](#correlation-matrices)
  - [Python Data Science Libraries](#python-data-science-libraries)
    - [NumPy](#numpy)
    - [Pandas](#pandas)
    - [Scikit-Learn (sklearn)](#scikit-learn-sklearn)

---

## Basics

### Quick Look

- Random forest: multiclass classification 
- K-means: clustering 

### Supervised Learning 

- Regression 
- Classification

### Unsupervised Learning

- Clustering 

### Reinforcement Learning

This type of algorithm learns how to act in a specific environment based on the feedback it receives.

Reinforcement learning techniques are being used to teach the agent how to act in the game based on the rewards or penalties it receives from the game.

### Model Hyperparameters

Hyperparameters cannot be learned by the model. They are set by data scientists in order to define some specific conditions for the algorithm learning process. 

These hyperparameters are different for each family of algorithms and they can, for instance, help fast-track the learning process or limit the risk of overfitting.

---

## Regression

### The Method of Least Squares

Residual (ϵi): Calculating the difference between the actual dependent variable value and the predicted dependent variable value gives an error. 

Error Sum of Squares (ESS): Square the residual (ϵi) for every data point, and add them together.

The Method of Least Squares is a common method used to determine the regression line, which seeks to minimize the ESS.

Y ≈ β0 + β1·X1 + β2·X2 + ...

Model coefficients or parameters: β0, β1, β2 ...

### Logarithmic Transformations of Variables

Sometimes the relationship between the dependent and independent variables is not linear.

Depending on the nature of the relationship, the logarithm function can be used to transform the variable of interest. Then the transformed variable tends to have a linear relationship with the other untransformed variables.

### Correlation Matrices

One of the ways of visualizing the linear relationship between variables is with a correlation matrix.

A correlation matrix can be converted to a form of "heatmap" so that the correlation between variables can easily be observed using different colors.

We can use the findings from the correlation matrix as the starting point for further regression analysis. The heatmap gives us a good overview of relationships in the data and can show us which variables to target in our investigation.

---

## Python Data Science Libraries 

### NumPy

- ndarray
- Given two lists, use NumPy to do element-wise calculations. 
- Do not need to iterate the list. You can operate all elements based on array.

```python
# import module
import numpy as np

# transfer a list to a numpy array 
array_name = np.array(list_name)

# subsetting 
# get elements that are greater than 10 
subset_array_name = array_name[array_name > 10]
```

### Pandas 

- DataFrame is composed of pandas Series, which are 1-dimensional arrays.
- Series is basically a single column in a DataFrame. 
- A DataFrame stores and manipulates tabular data in rows of observations and columns of variables.

```python
# import module 
import pandas as pd

country_dict = {"country": ["Brazil", "Russia", "India", "China", "South Africa"],
       "capital": ["Brasilia", "Moscow", "New Dehli", "Beijing", "Pretoria"],
       "area": [8.516, 17.10, 3.286, 9.597, 1.221],
       "population": [200.4, 143.5, 1252, 1357, 52.98] }
# transfer a dictionary to a data frame
country_data_frame = pd.DataFrame(country_dict)

# change default numerial index to customized index 
country_data_frame.index = ["BR", "RU", "IN", "CH", "SA"]


# transfer a .csv file to a data frame 
data_frame_name = pd.read_csv('file_name.csv')

# get the observation of a specific row based on index
data_frame_name.iloc[index_number]

# get the observation of a specific row based on label
data_frame_name.loc['label_value']
```

By default, `pd.read_excel()` loads the first sheet of an Excel spreadsheet.

Extract the response variable: `target = <df_name>.pop("<column_name>")`

### Scikit-Learn (sklearn)

With sklearn, there are four main steps to train a machine learning model: 

1. Instantiate a model with specified hyperparameters: this will configure the machine learning model you want to train.
2. Train the model with training data: during this step, the model will learn the best parameters to get predictions as close as possible to the actual values of the target.
3. Predict the outcome from input data: using the learned parameter, the model will predict the outcome for new data.
4. Assess the performance of the model predictions: for checking whether the model learned the right patterns to get accurate predictions.

Each algorithm will have its own specific hyperparameters that can be tuned. If you leave the hyperparameters blank, the model will use the default values specified by sklearn.

**Recommend**: at least set the `random_state` hyperparameter in order to get reproducible results every time that you have to run the same code.