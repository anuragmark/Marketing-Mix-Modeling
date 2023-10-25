# Marketing Mix Modeling

### Objective
Develop a model to understand the relationship between different marketing channels on the overall sales. This project includes two models of varying complexity to develop a regression model to quantify the relationship

### Data
The model is build using the dataset available on the following link
https://raw.githubusercontent.com/Garve/datasets/4576d323bf2b66c906d5130d686245ad205505cf/mmm.csv

### Dataset Overview
The dataset contains the following fields for a period of 200 weeks from 2018 to 2021

![image](https://github.com/anuragmark/MarketingMixModeling/assets/135703523/b7909926-50d2-478b-ae6c-978e226ab4ec)


### Visualizing the Data

TV spend has the highest correlation with the total sales
![image](https://github.com/anuragmark/MarketingMixModeling/assets/135703523/2a423e51-11cd-4b80-94df-5b8dd7a17c1a)


Plotting the spend and sales across the period, show a similar trend for TV spend and the total sales
![image](https://github.com/anuragmark/MarketingMixModeling/assets/135703523/2b485e6a-a175-4de6-9b3d-b9bd0ea44159)


### a. Basic Model

Our initial model assumes a linear relationship between sales and marketing spends:
sales=base+a×TV+b×Radio+c×Banners

Using the LinearRegression from sklearn, we attempt to estimate the coefficients (a, b, c) that quantify the contribution of each marketing channel to total sales.

#### Output
mean absolute error: 1062.23
mean absolute percentage error: 0.11
r2: 0.78

#### Plots
![image](https://github.com/anuragmark/MarketingMixModeling/assets/135703523/e075aff0-fcbb-404b-aa26-ddbcfa10c61a)



### b. Improving the model- Adstock and Diminishing Returns

While the simple model provides a foundational understanding, it's vital to consider real-world complexities:

AdStock Effect: The impact of marketing activity can span over an extended period, rather than being immediate.
Diminishing Returns: There's a threshold beyond which pouring more money into marketing might not yield proportional returns.
To address these complexities, we incorporated AdStock transformations and diminishing return factors into our model.
#### Adding the features

```
def df_transformations(df,alpha_tv,beta_tv,alpha_radio,beta_radio,alpha_banners,beta_banners):
    for i in range(0,len(df)):
        if i == 0:
            df.loc[i,'TV_adstock'] = np.power(df.loc[i,'TV'],alpha_tv)
            df.loc[i,'Radio_adstock'] = np.power(df.loc[i,'Radio'],alpha_radio)
            df.loc[i,'Banners_adstock'] = np.power(df.loc[i,'Banners'],alpha_banners)
        else:
            df.loc[i,'TV_adstock'] = np.power(df.loc[i,'TV'] + df.loc[i-1,'TV_adstock'] * beta_tv,alpha_tv)
            df.loc[i,'Radio_adstock'] = np.power(df.loc[i,'Radio'] + df.loc[i-1,'Radio_adstock'] * beta_radio,alpha_radio)
            df.loc[i,'Banners_adstock'] = np.power(df.loc[i,'Banners'] + df.loc[i-1,'Banners_adstock'] * beta_banners,alpha_banners)
                      
    return df
```


#### Output
Cross Validation Data
- mean squared error: 431524.86
- mean absolute error: 529.26
- r2: 0.931

Test Data
- mean squared error: 414057.96
- mean absolute error: 494.32
- r2: 0.963


### Optimization
Using hyperopt, a Python library for serial and parallel optimization over awkward search spaces, we performed a manual grid search to find the best alpha and beta values that optimize our model's performance.
After iterating and fitting our model using these optimal parameters, the results showcased a significantly better R2 score, highlighting the model's enhanced capability to explain the variance in Sales.

#### Optimized Output
Cross Validation Data
- mean squared error: 384236.94
- mean absolute error: 493.28
- r2: 0.947

Test Data
- mean squared error: 256937.63
- mean absolute error: 413.19
- r2: 0.964


### Final Output

The optimized model gives a much strong prediction with a good fit over the time period. The addition of Adstock and Diminishing returns improved the model accuracy and helped better define the relationship between marketing channels and total sales.

![image](https://github.com/anuragmark/MarketingMixModeling/assets/135703523/95450988-5947-49c6-816f-2beed81babeb)






