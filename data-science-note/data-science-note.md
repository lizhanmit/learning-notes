# Data Science Note

Multiclass classification - random forest

K-means - clustering 

## Basics

### Supervised Learning 

- Regression 
- Classification
- 

### Unsupervised Learning

- Clustering 

### Reinforcement Learning

This type of algorithm learns how to act in a specific environment based on the feedback it receives.

Reinforcement learning techniques are being used to teach the agent how to act in the game based on the rewards or penalties it receives from the game.

### Model Hyperparameters

Hyperparameters cannot be learned by the model. They are set by data scientists in order to define some specific conditions for the algorithm learning process. 

These hyperparameters are different for each family of algorithms and they can, for instance, help fast-track the learning process or limit the risk of overfitting.



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