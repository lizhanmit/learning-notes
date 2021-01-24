# Data Science Note

- [Data Science Note](#data-science-note)
  - [Books](#books)
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

---

## Books

- [Top 20 Best Data Science Books You Should Read](https://solutionsreview.com/business-intelligence/best-data-science-books-you-should-read/)
- [17 BEST Data Science Books (2021 Update)](https://www.guru99.com/data-science-books.html)

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

