```python
import pandas as pd
import numpy as np

import matplotlib.pyplot as plt
import seaborn as sns

from sklearn.model_selection import train_test_split, cross_val_score
from sklearn.preprocessing import OneHotEncoder
from sklearn.linear_model import LinearRegression
from sklearn.ensemble import RandomForestRegressor
```

# Predictive Regression Modeling Workflow

## Students Will Be Able To
 - Understand the basic outline of a predictive modeling workflow
 - Describe the similarities and differences between this predictive regression workflow and the previous (Phase 2) inferential regression workflow

## Business Understanding and Data Understanding

This dataset was downloaded from [Kaggle](https://www.kaggle.com/nehalbirla/vehicle-dataset-from-cardekho) and contains information about **used car sale listings**.  We are trying to **predict the price** associated with the listing.

### Features (as described on Kaggle)
 - `Car_Name`: The name of the car
 - `Year`: The year in which the car was bought
 - `Selling_Price`: The price the owner wants to sell the car at
 - `Present_Price`: The current ex-showroom price of the car
 - `Kms_Driven`: The distance completed by the car in km
 - `Fuel_Type`: The fuel type of the car (`Petrol`, `Diesel`, or Other)
 - `Seller_Type`: Whether the seller is a dealer or an individual
 - `Transmission`: Whether the car is manual or automatic
 - `Owner`: The number of owners the car has previously had

Looking at the original website, it looks like the prices are listed in lakhs, meaning hundreds of thousands of rupees.


```python
df = pd.read_csv("cars.csv")
```


```python
df.head()
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
      <th>Car_Name</th>
      <th>Year</th>
      <th>Selling_Price</th>
      <th>Present_Price</th>
      <th>Kms_Driven</th>
      <th>Fuel_Type</th>
      <th>Seller_Type</th>
      <th>Transmission</th>
      <th>Owner</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>ritz</td>
      <td>2014</td>
      <td>3.35</td>
      <td>5.59</td>
      <td>27000</td>
      <td>Petrol</td>
      <td>Dealer</td>
      <td>Manual</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>sx4</td>
      <td>2013</td>
      <td>4.75</td>
      <td>9.54</td>
      <td>43000</td>
      <td>Diesel</td>
      <td>Dealer</td>
      <td>Manual</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>ciaz</td>
      <td>2017</td>
      <td>7.25</td>
      <td>9.85</td>
      <td>6900</td>
      <td>Petrol</td>
      <td>Dealer</td>
      <td>Manual</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>wagon r</td>
      <td>2011</td>
      <td>2.85</td>
      <td>4.15</td>
      <td>5200</td>
      <td>Petrol</td>
      <td>Dealer</td>
      <td>Manual</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>swift</td>
      <td>2014</td>
      <td>4.60</td>
      <td>6.87</td>
      <td>42450</td>
      <td>Diesel</td>
      <td>Dealer</td>
      <td>Manual</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.describe()
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
      <th>Year</th>
      <th>Selling_Price</th>
      <th>Present_Price</th>
      <th>Kms_Driven</th>
      <th>Owner</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>301.000000</td>
      <td>301.000000</td>
      <td>301.000000</td>
      <td>301.000000</td>
      <td>301.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2013.627907</td>
      <td>4.661296</td>
      <td>7.628472</td>
      <td>36947.205980</td>
      <td>0.043189</td>
    </tr>
    <tr>
      <th>std</th>
      <td>2.891554</td>
      <td>5.082812</td>
      <td>8.644115</td>
      <td>38886.883882</td>
      <td>0.247915</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2003.000000</td>
      <td>0.100000</td>
      <td>0.320000</td>
      <td>500.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2012.000000</td>
      <td>0.900000</td>
      <td>1.200000</td>
      <td>15000.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2014.000000</td>
      <td>3.600000</td>
      <td>6.400000</td>
      <td>32000.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2016.000000</td>
      <td>6.000000</td>
      <td>9.900000</td>
      <td>48767.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2018.000000</td>
      <td>35.000000</td>
      <td>92.600000</td>
      <td>500000.000000</td>
      <td>3.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.isna().sum()
```




    Car_Name         0
    Year             0
    Selling_Price    0
    Present_Price    0
    Kms_Driven       0
    Fuel_Type        0
    Seller_Type      0
    Transmission     0
    Owner            0
    dtype: int64




```python
sns.pairplot(df);
```


![png](predictive_regression_files/predictive_regression_6_0.png)


### *Compare and Contrast: Business Understanding and Data Understanding*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Mostly the same...looked at what features we have, missing values, descriptive statistics
plots

Difference: we're trying to predict price, we don't necessarily care about the relationship
between the features and the target
"""
```

## Train-Test Split

Before performing any preprocessing or modeling, set aside a holdout test set


```python
X = df.drop("Selling_Price", axis=1)
y = df["Selling_Price"]

X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=42)
```

### *Compare and Contrast: Train-Test Split*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Difference: train and test sets separate

Similarity: identify, separate out the target variable
"""
```

## Baseline Model: Linear Regression with Numeric Features Only

We have four numeric features (`Year`, `Present_Price`, `Kms_Driven`, and `Owner`) and four non-numeric features (`Car_Name`, `Fuel_Type`, `Seller_Type`, `Transmission`).  Before doing any of the engineering work to be able to use those non-numeric features, let's just try using the numeric ones


```python
lin_reg_model = LinearRegression()

X_train_numeric = X_train[["Year", "Present_Price", "Kms_Driven", "Owner"]].copy()
```


```python
baseline_cross_val_score = cross_val_score(lin_reg_model, X_train_numeric, y_train)
baseline_cross_val_score
```




    array([0.67168559, 0.75626366, 0.88591659, 0.79241643, 0.84299344])



Ok, not too bad, we are getting somewhere between 0.67 and 0.89 r-squared for a linear regression with just the numeric features

### *Compare and Contrast: Baseline Model*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Differences:
- Not using all the data, just using training data
- Using cross validation, which we didn't do before
- Model evaluation is just r-squared
- Didn't check assumptions
- Not using StatsModels, using SciKit-Learn

Similarities:
- Using r-squared to evaluate
- Using linear regression
"""
```

## Add One-Hot Encoded Features

Let's see if adding in some of those non-numeric features helps


```python
X_train_all_features = X_train.copy()
```


```python
def encode_and_concat_feature_train(X_train_all_features, feature_name):
    """
    Helper function for transforming training data.  It takes in the full X dataframe and
    feature name, makes a one-hot encoder, and returns the encoder as well as the dataframe
    with that feature transformed into multiple columns of 1s and 0s
    """
    
    # make a one-hot encoder and fit it to the training data
    ohe = OneHotEncoder(categories="auto", handle_unknown="ignore", sparse=False)
    single_feature_df = X_train_all_features[[feature_name]]
    ohe.fit(single_feature_df)
    
    # call helper function that actually encodes the feature and concats it
    X_train_all_features = encode_and_concat_feature(X_train_all_features, feature_name, ohe)
    
    return ohe, X_train_all_features
```


```python
def encode_and_concat_feature(X, feature_name, ohe):
    """
    Helper function for transforming a feature into multiple columns of 1s and 0s. Used
    in both training and testing steps.  Takes in the full X dataframe, feature name, 
    and encoder, and returns the dataframe with that feature transformed into multiple
    columns of 1s and 0s
    """
    # create new one-hot encoded df based on the feature
    single_feature_df = X[[feature_name]]
    feature_array = ohe.transform(single_feature_df)
    ohe_df = pd.DataFrame(feature_array, columns=ohe.categories_[0], index=single_feature_df.index)
    
    # drop the old feature from X and concat the new one-hot encoded df
    X.drop(feature_name, axis=1, inplace=True)
    X = pd.concat([X, ohe_df], axis=1)
    
    return X
```


```python
# we will need each of these encoders later for transforming the test data

fuel_type_ohe, X_train_all_features = encode_and_concat_feature_train(X_train_all_features, "Fuel_Type")
seller_type_ohe, X_train_all_features = encode_and_concat_feature_train(X_train_all_features, "Seller_Type")
transmission_ohe, X_train_all_features = encode_and_concat_feature_train(X_train_all_features, "Transmission")
# putting car name at the end just because there are the most categories
car_name_ohe, X_train_all_features = encode_and_concat_feature_train(X_train_all_features, "Car_Name")
```


```python
X_train_all_features.columns
```




    Index(['Year', 'Present_Price', 'Kms_Driven', 'Owner', 'CNG', 'Diesel',
           'Petrol', 'Dealer', 'Individual', 'Automatic', 'Manual', '800',
           'Activa 3g', 'Bajaj  ct 100', 'Bajaj Avenger 150',
           'Bajaj Avenger 150 street', 'Bajaj Avenger 220',
           'Bajaj Avenger 220 dtsi', 'Bajaj Avenger Street 220',
           'Bajaj Discover 100', 'Bajaj Discover 125', 'Bajaj Dominar 400',
           'Bajaj Pulsar 135 LS', 'Bajaj Pulsar 150', 'Bajaj Pulsar 220 F',
           'Bajaj Pulsar NS 200', 'Bajaj Pulsar RS200', 'Hero  Ignitor Disc',
           'Hero Extreme', 'Hero Glamour', 'Hero Honda Passion Pro', 'Hero Hunk',
           'Hero Passion Pro', 'Hero Passion X pro', 'Hero Splender Plus',
           'Hero Splender iSmart', 'Hero Super Splendor', 'Honda Activa 4G',
           'Honda CB Hornet 160R', 'Honda CB Shine', 'Honda CB Unicorn',
           'Honda CB twister', 'Honda CBR 150', 'Honda Karizma', 'Hyosung GT250R',
           'KTM 390 Duke ', 'KTM RC200', 'KTM RC390', 'Royal Enfield Bullet 350',
           'Royal Enfield Classic 350', 'Royal Enfield Classic 500',
           'Royal Enfield Thunder 350', 'Royal Enfield Thunder 500',
           'Suzuki Access 125', 'TVS Apache RTR 160', 'TVS Apache RTR 180',
           'TVS Jupyter', 'TVS Sport ', 'TVS Wego', 'Yamaha FZ  v 2.0',
           'Yamaha FZ 16', 'Yamaha FZ S ', 'Yamaha FZ S V 2.0', 'alto 800',
           'alto k10', 'amaze', 'baleno', 'brio', 'camry', 'ciaz', 'city',
           'corolla altis', 'creta', 'dzire', 'elantra', 'eon', 'ertiga',
           'etios cross', 'etios g', 'etios gd', 'etios liva', 'fortuner',
           'grand i10', 'i10', 'i20', 'ignis', 'innova', 'jazz', 'land cruiser',
           'omni', 'ritz', 'swift', 'sx4', 'verna', 'wagon r', 'xcent'],
          dtype='object')




```python
X_train_all_features
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
      <th>Year</th>
      <th>Present_Price</th>
      <th>Kms_Driven</th>
      <th>Owner</th>
      <th>CNG</th>
      <th>Diesel</th>
      <th>Petrol</th>
      <th>Dealer</th>
      <th>Individual</th>
      <th>Automatic</th>
      <th>...</th>
      <th>innova</th>
      <th>jazz</th>
      <th>land cruiser</th>
      <th>omni</th>
      <th>ritz</th>
      <th>swift</th>
      <th>sx4</th>
      <th>verna</th>
      <th>wagon r</th>
      <th>xcent</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>129</th>
      <td>2017</td>
      <td>0.84</td>
      <td>5000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>232</th>
      <td>2015</td>
      <td>14.79</td>
      <td>12900</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>195</th>
      <td>2015</td>
      <td>0.32</td>
      <td>35000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>277</th>
      <td>2015</td>
      <td>13.60</td>
      <td>21780</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>280</th>
      <td>2015</td>
      <td>5.90</td>
      <td>14465</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>188</th>
      <td>2013</td>
      <td>0.57</td>
      <td>18000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>71</th>
      <td>2011</td>
      <td>12.48</td>
      <td>45000</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>106</th>
      <td>2014</td>
      <td>3.45</td>
      <td>16500</td>
      <td>1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>270</th>
      <td>2011</td>
      <td>10.00</td>
      <td>69341</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>102</th>
      <td>2017</td>
      <td>1.78</td>
      <td>4000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>225 rows × 96 columns</p>
</div>



### *Compare and Contrast: One-Hot Encoding*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Similarity:
- Dummying out categorical variables (our model still needs 100% numbers)

Differences:
- Didn't drop any columns when dummying categorical variables (not worried about multicollinearity)
- We added everything, didn't do much EDA beforehand or add features one at a time
"""
```

## Linear Regression with More Features


```python
lin_reg_model = LinearRegression()

print("Old:", baseline_cross_val_score)
print("New:", cross_val_score(lin_reg_model, X_train_all_features, y_train))
```

    Old: [0.67168559 0.75626366 0.88591659 0.79241643 0.84299344]
    New: [ 5.37672694e-01 -7.38091761e+12  9.16586477e-01  7.58859065e-01
      7.52699829e-01]


That looks worse.  What if we don't use the car name, and just use the categories with 1-3 values?


```python
X_train_all_except_car_name = X_train_all_features[[
                    "Year",
                    "Present_Price",
                    "Kms_Driven",
                    "Owner",
                    "CNG",
                    "Diesel",
                    "Petrol",
                    "Dealer",
                    "Individual",
                    "Automatic",
                    "Manual"
                ]].copy()
X_train_all_except_car_name
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
      <th>Year</th>
      <th>Present_Price</th>
      <th>Kms_Driven</th>
      <th>Owner</th>
      <th>CNG</th>
      <th>Diesel</th>
      <th>Petrol</th>
      <th>Dealer</th>
      <th>Individual</th>
      <th>Automatic</th>
      <th>Manual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>129</th>
      <td>2017</td>
      <td>0.84</td>
      <td>5000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>232</th>
      <td>2015</td>
      <td>14.79</td>
      <td>12900</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>195</th>
      <td>2015</td>
      <td>0.32</td>
      <td>35000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>277</th>
      <td>2015</td>
      <td>13.60</td>
      <td>21780</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>280</th>
      <td>2015</td>
      <td>5.90</td>
      <td>14465</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>188</th>
      <td>2013</td>
      <td>0.57</td>
      <td>18000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>71</th>
      <td>2011</td>
      <td>12.48</td>
      <td>45000</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>106</th>
      <td>2014</td>
      <td>3.45</td>
      <td>16500</td>
      <td>1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>270</th>
      <td>2011</td>
      <td>10.00</td>
      <td>69341</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>102</th>
      <td>2017</td>
      <td>1.78</td>
      <td>4000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>225 rows × 11 columns</p>
</div>




```python
lin_reg_model = LinearRegression()

print("Old:", baseline_cross_val_score)
print("New:", cross_val_score(lin_reg_model, X_train_all_except_car_name, y_train))
```

    Old: [0.67168559 0.75626366 0.88591659 0.79241643 0.84299344]
    New: [0.71773298 0.65008625 0.92349676 0.81151078 0.88264199]


Ok, adding these categories improved r-squared for 4 out of 5 subsamples compared to just having numeric features, so let's keep them for our linear regression model


```python
best_linreg_cross_val_score = cross_val_score(lin_reg_model, X_train_all_except_car_name, y_train)
```

### *Compare and Contrast: Linear Regression with More Features*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Differences:
- Just compared r-squared
- Used cross validation
"""
```

## Try a More Advanced Model

It depends on our business case whether these numbers are sufficient.  We are explaining approximately somewhere between 65% and 92% of the variance in the sale price.  But let's try a more complicated model.

First, just using the X_train values used in the linear regression:


```python
random_forest_regressor_model_1 = RandomForestRegressor(n_estimators=10, random_state=42)

print("Old:", best_linreg_cross_val_score)
print("New:", cross_val_score(random_forest_regressor_model_1, X_train_all_except_car_name, y_train))
```

    Old: [0.71773298 0.65008625 0.92349676 0.81151078 0.88264199]
    New: [0.83102658 0.66532476 0.90650579 0.81714334 0.92862009]


Ok, this more-sophisticated model is performing slightly better on 4 of 5 subsamples than the best linear regression score.  Let's see what happens if we add the car names back in:


```python
random_forest_regressor_model_2 = RandomForestRegressor(n_estimators=10, random_state=42)

print("Old:", cross_val_score(random_forest_regressor_model_1, X_train_all_except_car_name, y_train))
print("New:", cross_val_score(random_forest_regressor_model_2, X_train_all_features, y_train))
```

    Old: [0.83102658 0.66532476 0.90650579 0.81714334 0.92862009]
    New: [0.8120682  0.7237103  0.90434184 0.80154837 0.92665771]



```python
best_linreg_cross_val_score.mean()
```




    0.7970937532168529




```python
best_linreg_cross_val_score.std()
```




    0.10140355946252323



Only one of the subsamples improved with adding this feature, and everything else got worse

## Hyperparameter Tuning the More Advanced Model

Let's add some more "power" to the random forest regressor, since it's running reasonably quickly right now


```python
random_forest_regressor_model_3 = RandomForestRegressor(n_estimators=1000, random_state=42)

print("Old:", cross_val_score(random_forest_regressor_model_1, X_train_all_except_car_name, y_train))
print("New:", cross_val_score(random_forest_regressor_model_3, X_train_all_except_car_name, y_train))
```

    Old: [0.83102658 0.66532476 0.90650579 0.81714334 0.92862009]
    New: [0.85347083 0.72854389 0.90506631 0.83936817 0.93520377]


That marginally improved 4 of the 5 subsamples (but was significantly slower to run).  Let's try including the car name again:


```python
random_forest_regressor_model_4 = RandomForestRegressor(n_estimators=1000, random_state=42)

print("Old:", cross_val_score(random_forest_regressor_model_3, X_train_all_except_car_name, y_train))
print("New:", cross_val_score(random_forest_regressor_model_4, X_train_all_features, y_train))
```

    Old: [0.85347083 0.72854389 0.90506631 0.83936817 0.93520377]
    New: [0.82470832 0.71709424 0.89251424 0.8456903  0.93846238]


Again, that didn't really seem to help.  So if we're stopping right now, we can say that the third random forest regressor is the best model.

### *Compare and Contrast: Hyperparameter Tuning*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Similar:
- Adding more features isn't always better
- Sometimes the last model you build isn't the best
- We're iteratively building models until we run out of time, then picked the "best"

Different:
- Before, didn't have hyperparameters to tune, now we do
- Computational complexity is a concern
"""
```

## Model Evaluation

Now that we have chosen a best model, let's use the holdout set to see how well the final model does

### Preprocessing to Use Test Data
First, perform all of the same transformations on the test X that were performed on the train X


```python
X_test_all_except_car_name = X_test.reset_index().drop(["index", "Car_Name"], axis=1)

# fuel_type_ohe, seller_type_ohe, and transmission_ohe were fitted on the training data
X_test_all_except_car_name = encode_and_concat_feature(X_test_all_except_car_name, "Fuel_Type", fuel_type_ohe)
X_test_all_except_car_name = encode_and_concat_feature(X_test_all_except_car_name, "Seller_Type", seller_type_ohe)
X_test_all_except_car_name = encode_and_concat_feature(X_test_all_except_car_name, "Transmission", transmission_ohe)

X_test_all_except_car_name
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
      <th>Year</th>
      <th>Present_Price</th>
      <th>Kms_Driven</th>
      <th>Owner</th>
      <th>CNG</th>
      <th>Diesel</th>
      <th>Petrol</th>
      <th>Dealer</th>
      <th>Individual</th>
      <th>Automatic</th>
      <th>Manual</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016</td>
      <td>0.57</td>
      <td>24000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016</td>
      <td>13.60</td>
      <td>10980</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012</td>
      <td>9.40</td>
      <td>60000</td>
      <td>0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
      <td>0.57</td>
      <td>35000</td>
      <td>1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2013</td>
      <td>18.61</td>
      <td>40001</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>71</th>
      <td>2011</td>
      <td>8.01</td>
      <td>50000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>72</th>
      <td>2016</td>
      <td>7.90</td>
      <td>28569</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>73</th>
      <td>2015</td>
      <td>7.27</td>
      <td>40534</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>74</th>
      <td>2012</td>
      <td>4.43</td>
      <td>23709</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>75</th>
      <td>2016</td>
      <td>1.40</td>
      <td>35000</td>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>76 rows × 11 columns</p>
</div>



### Fitting and Scoring Final Model

Fit our best model on all of the training data


```python
random_forest_regressor_model_3.fit(X_train_all_except_car_name, y_train)
```




    RandomForestRegressor(bootstrap=True, ccp_alpha=0.0, criterion='mse',
                          max_depth=None, max_features='auto', max_leaf_nodes=None,
                          max_samples=None, min_impurity_decrease=0.0,
                          min_impurity_split=None, min_samples_leaf=1,
                          min_samples_split=2, min_weight_fraction_leaf=0.0,
                          n_estimators=1000, n_jobs=None, oob_score=False,
                          random_state=42, verbose=0, warm_start=False)



Score our best model on the test data


```python
random_forest_regressor_model_3.score(X_test_all_except_car_name, y_test)
```




    0.9706072528266274



That's pretty good!  We have a model that is able to explain 97% of the variance in the car sale list prices

### Fitting and Scoring Baseline Model

Let's compare that to the baseline model:


```python
lin_reg_model.fit(X_train_numeric, y_train)
```




    LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None, normalize=False)




```python
X_test_numeric = X_test[["Year", "Present_Price", "Kms_Driven", "Owner"]].copy()

lin_reg_model.score(X_test_numeric, y_test)
```




    0.8511933884353676



So, our r-squared has improved from a baseline of 85% to 97%.

### Other Metrics

To report something more applicable to a business audience, let's calculate the [root mean squared error](https://en.wikipedia.org/wiki/Root-mean-square_deviation) using the [metrics submodule of scikit-learn](https://scikit-learn.org/stable/modules/model_evaluation.html#regression-metrics)


```python
from sklearn.metrics import mean_squared_error
mse = mean_squared_error(y_test, random_forest_regressor_model_3.predict(X_test_all_except_car_name))
mse
```




    0.8075452595118476




```python
rmse = np.sqrt(mse)
rmse
```




    0.8986352204937483



To interpret this: on average, our prediction of `Selling_Price` is off (either too high or too low) by about 0.9 lakh, i.e. about 90,000 rupees (about 1200 USD)

Let's compare that to the baseline model:


```python
baseline_mse = mean_squared_error(y_test, lin_reg_model.predict(X_test_numeric))
np.sqrt(baseline_mse)
```




    2.021968848419496



So, the baseline model was off by about 2 lakh, i.e. about 200,000 rupees (about 2600 USD)

### Visualization of Model Performance

Also, here is a plot that shows the actual vs. predicted prices:


```python
fig, (ax1, ax2) = plt.subplots(ncols=2, figsize=(12, 6))

# Customize which model is actually being plotted
ax1.set_title("Baseline Regression Model Performance")
ax1.scatter(y_test, lin_reg_model.predict(X_test_numeric),
                alpha=0.5, label="model output", color="green")

ax2.set_title("Final Regression Model Performance")
ax2.scatter(y_test, random_forest_regressor_model_3.predict(X_test_all_except_car_name),
                alpha=0.5, label="model output")

# Same setup for both plots (x and y labels, line showing y=x)
y_equals_x = np.linspace(0, 25)
for ax in (ax1, ax2):
    ax.set_xlabel("True Price (Lakhs)")
    ax.set_ylabel("Predicted Price (Lakhs)")
    ax.plot(y_equals_x, y_equals_x, label="predicted = actual", color="black")
    ax.legend()
```


![png](predictive_regression_files/predictive_regression_66_0.png)


### *Compare and Contrast: Model Evaluation*

What (if anything) was similar between this process and the previous process?  What (if anything) was different?


```python
"""
Differences:
- Before we had a scatter plot with residuals, looking for heteroskedasticity
  Now we are just comparing the predicted vs. actual values
- No linear regression assumption check visualizations (or statistics), so no
  Q-Q plot
- Final evaluation that is different from what we were doing along the way
  - "Along the way" we didn't use the test data, we used cross-validation instead
  - At the end, we did use test data, didn't use cross-validation
  - The goal is to avoid "manually overfitting" by choosing based on test set performance

Similarities:
- Comparison to a baseline (showing improvement)

Overall process differences:
- We didn't stop to evaluate after adding each individual feature
"""
```

### Appendix 1

We got a question during lecture about why the `RandomForestRegressor` "performed better on the test than on the train".  This isn't quite a correct characterization, but it's worth thinking about

Let's look at the scores again from when we were choosing a "best model":


```python
print(cross_val_score(random_forest_regressor_model_3, X_train_all_except_car_name, y_train))
```

    [0.85347083 0.72854389 0.90506631 0.83936817 0.93520377]


And again, the code for the final evaluation of the "best model":


```python
random_forest_regressor_model_3.fit(X_train_all_except_car_name, y_train)
random_forest_regressor_model_3.score(X_test_all_except_car_name, y_test)
```




    0.9706072528266274



It's definitely true that the cross-validated scores range from 73% to 93%, whereas the final evaluation score is 97% — a good amount higher

But the important thing to be clear on is that these are _both_ scores on "test data", just different test sets.  For the cross-validated scores, those are scores from training on a subset and then testing on a subset of the training data.  Then the final score is created by training on the whole training set and testing on the test set.

If we scored the final model on the train set, it's actually even better:


```python
random_forest_regressor_model_3.score(X_train_all_except_car_name, y_train)
```




    0.9834297910857059



But, to go back to the original question, why is the final test score so much better than the cross-validated test scores?

First, a reminder that we are working with a pretty tiny dataset:


```python
X_train.shape
```




    (225, 8)




```python
# 5 splits for cross-validation
225 / 5
```




    45.0




```python
# 4 out of 5 splits used for training, remaining used for testing
45 * 4
```




    180




```python
X_test.shape
```




    (76, 8)



Since we are doing a 5-fold cross-validation, that means that the cross-validated scores are each being computed with:

 - 180 rows in the training set
 - 45 rows in the test set

By comparison, our overall model evaluation is using all of `X`, which means:

 - 225 rows in the training set
 - 76 rows in the test set

When we are working with such a small dataset, being able to train on 45 additional data points (and having the errors in the test data be spread out over 31 more data points) can naturally mean better performance.  It's typical for final results to be better than cross-validated results unless you have a very large dataset!

Separately, we might have just randomly chosen a "lucky" test set, that includes fewer outliers on average, or is more easily predicted by the features we have (rather than some unknown features we don't have). Because we used cross-validation to choose our final model (rather than evaluating on the test set to choose our final model), this random concern could just as easily be an "unlucky" test set as a "lucky" one.  But if we had used the test set for choosing a model, we could run into issues where we systematically overestimate our model's predictive abilities, because we didn't actually evaluate on unseen data.

### Appendix 2

We got a question during lecture about why the print-out of the `RandomForestRegressor` showed something different than mine showed

This is the result of a difference in the `__repr__` method between our versions of SciKit-Learn

You can see your version of SciKit-Learn with this code:


```python
import sklearn
```


```python
sklearn.__version__
```




    '0.22'



Here is an example of how the `__repr__` method is used.  The "repr" name is short for "representational string", and it defines what will appear below a cell when you have an object as the last line

First, a class without the `__repr__` method.  Let's use a variant of the Pokemon example from the code challenge:


```python
class Pokemon:
    def __init__(self, name, height):
        self.name = name
        self.height = height
```


```python
pikachu = Pokemon("pikachu", 7)
```

If we want to print out the attributes of pikachu, that works just fine, since those are instances of built-in types (string and int respectively):


```python
pikachu.name
```




    'pikachu'




```python
pikachu.height
```




    7



But if we want to print out the whole object at once, the default representation is not very readable, since it's just the class name and the object's location in memory:


```python
pikachu
```




    <__main__.Pokemon at 0x118140518>



Adding a `__repr__` method allows us to customize this printout:


```python
class Pokemon:
    def __init__(self, name, height):
        self.name = name
        self.height = height
        
    def __repr__(self):
        return f"Pokemon(name={self.name}, height={self.height})"
```


```python
pikachu = Pokemon("pikachu", 7)
```


```python
pikachu
```




    Pokemon(name=pikachu, height=7)



It looks like SciKit-Learn decided to change which attributes were included here.  So, for example, let's remove the `height` attribute:


```python
class Pokemon:
    def __init__(self, name, height):
        self.name = name
        self.height = height
        
    def __repr__(self):
        return f"Pokemon(name={self.name})"
```


```python
pikachu = Pokemon("pikachu", 7)
```


```python
pikachu
```




    Pokemon(name=pikachu)



That means that the height isn't being displayed any more, but it doesn't necessarily mean that the attribute isn't there any more


```python
pikachu.height
```




    7




```python

```
