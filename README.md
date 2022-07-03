
## GLM methods to deal with count data

## Table of Contents

 - [Bike sharing count prediction using GLM](#bike-sharing-count-prediction-using-glm)
- [1. Problem Statement](#1-problem-statement)
- [2. Data Description](#2-data-description)
  * [Attribute Information](#attribute-information)
    + [Inputs](#inputs)
    + [Output](#output)
- [3. Modelling Evaluation](#3-modelling-evaluation)
- [4. Results](#4-results)


### Bike sharing count prediction using GLM
Bike sharing systems are new generation of traditional bike rentals where whole process from membership, rental and return back has become automatic. Today, there exists great interest in these systems due to their important role in traffic, environmental and health issues. 
Thus, using GLM to predict the count of rented bikes could be useful in generating better systems

### 1. Problem Statement
Predicting the count of rented bikes given some qualitative and quantitative attribute.

### 2. Data Description
Data is obtained from UCI Machine Learning Repository.
https://archive.ics.uci.edu/ml/datasets/bike+sharing+dataset 

- Number of instances - 731
- Number of attributes - 16

    #### Attribute Information
    ##### Inputs
    - instant
    - dteday 
    - season
    - yr  
    - mnth
    - holiday
    - weekday
    - workingday
    - weathersit
    - temp 
    - atemp 
    - windspeed
    - casual 
    - registered
    ##### Output
    - cnt

    ### 3. Modelling Evaluation

    - Algorithms used
        - Poisson Regression
        - Quasi Poisson model
        - Negative Binomial model
    - Metrics - Since R2 is based on the sums of squares of our model, and so cannot be calculated for GLMs, we adopted the Deviance Explained, and RMSE (Root Mean Squared Error)

    ### 4. Results
        
