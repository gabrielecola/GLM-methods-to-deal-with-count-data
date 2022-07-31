
## The Bike Sharing system analyzed by GLM

## Table of Contents

 - [Bike sharing count prediction using GLM](#bike-sharing-count-prediction-using-glm)
- [1. Problem Statement](#1-problem-statement)
- [2. Data Description](#2-data-description)
  * [Attribute Information](#attribute-information)
    + [Inputs](#inputs)
    + [Output](#output)
- [3. EDA](#3-eda)
- [4. Modelling Evaluation](#3-modelling-evaluation)
- [5. Results](#4-results)


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
    
    ### 3. EDA
        <p float="left">
  <img src="https://user-images.githubusercontent.com/103529789/177041218-70db5e89-d15e-46be-861d-0565fc51c03a.png" width="350"/>
  <img src="https://user-images.githubusercontent.com/103529789/177041253-e2a02a1b-c0d5-414a-bc99-f3e03c75a1b3.png" width="350" /> 
  <img src="https://user-images.githubusercontent.com/103529789/177041256-74d2ad9c-5a9e-4037-8ad0-751a58dc9c91.png" width="350"/>
  <img src="https://user-images.githubusercontent.com/103529789/177041257-4d39716f-1a4e-4d05-a093-e55d386d776f.png" width="350" /> 
</p>
    

    ### 4. Modelling Evaluation

    - Algorithms used
        - Poisson Regression
        - Quasi Poisson model
        - Negative Binomial model
    - Metrics: RMSE (Root Mean Squared Error)

    ### 5. Results
    
      <img src="https://user-images.githubusercontent.com/103529789/182046949-22d38172-ce40-4076-bac6-f2238b2d78ab.png" width="350"/>
   
        
