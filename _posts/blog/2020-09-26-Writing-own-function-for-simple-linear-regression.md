---
author: albert
tag: linear-regression
---
Having a set of data pairs (*x, y*), where *y* shows a linear dependence on *x*, the line of best fit can be found with the [simple linear regression]({% link _posts/handbook/statistics/2020-09-16-simple-linear-regression.md %}) model. In this post, I present Pyhton code solving the problem of simple linear regression.
<!--more-->

## Imports and data preparation

First, let's import stuff. Numpy for arrangement and easy processing of numerical data, Matplotlib for visualization, and in the and stats module from Scipy to test if the implemented solution works properly.


```python
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats
```

Now, we can arrange an array of independent variable *X*, and calculate values of dependent variable *Y*, assuming some linear dependence. We don't want to get straight line, so a bit of noise is introduced onto *Y*.


```python
X = np.arange(0, 100)
noise = 20 * np.random.normal(size=100)
Y = (3*X+14) + noise
```

Time to see what we've got.


```python
plt.scatter(X, Y)
plt.xlabel('X')
_ = plt.ylabel('Y')
```


![png](/assets/images/blog/2020-09-26/raw_data.png)


Looks fine. There is clearly linear dependence, however points don't create a straight line (due to introduced noise).

## Writing the algorithm

The slope for the line of best fit is given with equation

![a](/assets/images/handbook/statistics/simple_linear_regression_a.gif)

The below function will calculate the value of *a*.


```python
def get_slope(x, y):
    n = len(x)
    xy = x * y
    xx = x * x
    
    sum_x = x.sum()
    sum_y = y.sum()
    sum_xy = xy.sum()
    sum_xx = xx.sum()
    sum_x_sq = sum_x * sum_x
    
    a_output = (n * sum_xy - sum_x * sum_y) / (n * sum_xx - sum_x_sq)
    
    return a_output
```

The value of *b* can be calculated using the slope, which is the output of function ```get_slope```

![b](/assets/images/handbook/statistics/simple_linear_regression_b_by_means.gif)


```python
def get_intercept(x, y, slope):
    return y.mean() - slope * x.mean()
```

The next three functions are used to calculate the coefficient of determination *R<sup>2</sup>*.


```python
def get_residual_sum_of_squares(y, y_fit):
    squares = (y - y_fit) ** 2
    return squares.sum()

def get_total_sum_of_squares(y):
    squares = (y - y.mean()) ** 2
    return squares.sum()

def get_r_squared(y, y_fit):
    ss_res = get_residual_sum_of_squares(y, y_fit)
    ss_tot = get_total_sum_of_squares(y)
    return (1 - ss_res / ss_tot)
```

Finally, we can put everything together and close it in one function ```linear_regression```. The function will return the dictionary with short summary (containing the value of the slope, intercept, and R-squared) and set of Numpy arrays with the result of the fitting.


```python
def linear_regression(x, y):
    slope = get_slope(x, y)
    intercept = get_intercept(x, y, slope)
    y_fit = slope * x + intercept
    r_squared = get_r_squared(y, y_fit)
    
    summary = {'Slope': slope, 
               'Intercept': intercept,
               'R-squared': r_squared}
    
    return summary, (x, y_fit)
```

## Running the algoritm

Let's see if the implemented code works.


```python
summary, fit = linear_regression(X, Y)
summary
```




    {'Slope': 2.959611239086049,
     'Intercept': 13.001211160835936,
     'R-squared': 0.9529103935021158}



Time to plot the input data and the line of best fit together in one graph.


```python
plt.scatter(X, Y)
plt.plot(fit[0], fit[1], c='r', linewidth=3)
plt.legend(['fit', 'data'])
plt.xlabel('X')
_ = plt.ylabel('Y')
```


![png](/assets/images/blog/2020-09-26/data_and_fit.png)


Looks really good :-)

## Final test

We can use ```scipy.stats``` to do linear regression on the same set of data and compare the result with the result received with the implemented model. This will be the final check if everything is correct.


```python
slope, intercept, r_value, *_ = stats.linregress(X, Y)
r_squared = r_value ** 2
summary_stats = {'Slope': slope, 
                 'Intercept': intercept, 
                 'R-squared': r_squared}
```

Let's put everything  to Pandas DataFrame, so it is easier to compare the results.


```python
import pandas as pd

pd.DataFrame(data=[summary, summary_stats], 
             index=['My model', 'Scipy model'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Slope</th>
      <th>Intercept</th>
      <th>R-squared</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Implemented model</th>
      <td>2.959611</td>
      <td>13.001211</td>
      <td>0.95291</td>
    </tr>
    <tr>
      <th>Scipy model</th>
      <td>2.959611</td>
      <td>13.001211</td>
      <td>0.95291</td>
    </tr>
  </tbody>
</table>
</div>



It works! :D
