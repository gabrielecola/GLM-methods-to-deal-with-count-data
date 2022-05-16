---
title: 'GLM methods to deal with count data '
author: "Gabriele Cola"
date: "25/3/2022"
output:
  html_document:
    keep_md: true
---





#### 1. IMPORT DATASET

This dataset contains the daily count of rental bikes between years 2011 and 2012 in Capital bikeshare system, which operates on some of the major cities of the State of Washington D.C, Virginia and Maryland.
The dataset is given by the Laboratory of Artificial Intelligence and Decision support of the University of Porto.

The main attributes : \

- $\textbf{instant}$: record index 
- $\textbf{dteday}$ : date 
- $\textbf{season}$ : 1 = spring, 2 = summer, 3 = fall, 4 = winter
- $\textbf{yr}$ : Year (0: 2011, 1:2012) 
- $\textbf{mnth}$ : Month ( 1 to 12) 
- $\textbf{holiday}$ : whether the day is considered a holiday or not
- $\textbf{workingday}$: if day is neither weekend nor holiday is 1, otherwise is 0
- $\textbf{weekday}$: day of the week 
- $\textbf{weathersit}$: Weather ( 1:Clear,2: Cloudy,3:Rain)
- $\textbf{temp}$: temperature in Celsius (normalized)
- $\textbf{atemp}$:"feels like" temperature in Celsius(normalized)
- $\textbf{hum}$: relative humidity
- $\textbf{windspeed}$: normalized wind speed.
- $\textbf{casual}$: count of casual users
- $\textbf{registered}$: count of registered users 
- $\textbf{cnt}$: count of total rental bikes including both casual and registered 

<img src="https://render.githubusercontent.com/render/math?math=e^{i \pi} = -1">


```r
day_data=read_csv('/Users/gabrielecola/Downloads/Bike-Sharing-Dataset/day.csv')
head(day_data)
```

```
## # A tibble: 6 x 16
##   instant dteday     season    yr  mnth holiday weekday workingday weathersit
##     <dbl> <date>      <dbl> <dbl> <dbl>   <dbl>   <dbl>      <dbl>      <dbl>
## 1       1 2011-01-01      1     0     1       0       6          0          2
## 2       2 2011-01-02      1     0     1       0       0          0          2
## 3       3 2011-01-03      1     0     1       0       1          1          1
## 4       4 2011-01-04      1     0     1       0       2          1          1
## 5       5 2011-01-05      1     0     1       0       3          1          1
## 6       6 2011-01-06      1     0     1       0       4          1          1
## # … with 7 more variables: temp <dbl>, atemp <dbl>, hum <dbl>, windspeed <dbl>,
## #   casual <dbl>, registered <dbl>, cnt <dbl>
```

```r
summary(day_data)
```

```
##     instant          dteday               season            yr        
##  Min.   :  1.0   Min.   :2011-01-01   Min.   :1.000   Min.   :0.0000  
##  1st Qu.:183.5   1st Qu.:2011-07-02   1st Qu.:2.000   1st Qu.:0.0000  
##  Median :366.0   Median :2012-01-01   Median :3.000   Median :1.0000  
##  Mean   :366.0   Mean   :2012-01-01   Mean   :2.497   Mean   :0.5007  
##  3rd Qu.:548.5   3rd Qu.:2012-07-01   3rd Qu.:3.000   3rd Qu.:1.0000  
##  Max.   :731.0   Max.   :2012-12-31   Max.   :4.000   Max.   :1.0000  
##       mnth          holiday           weekday        workingday   
##  Min.   : 1.00   Min.   :0.00000   Min.   :0.000   Min.   :0.000  
##  1st Qu.: 4.00   1st Qu.:0.00000   1st Qu.:1.000   1st Qu.:0.000  
##  Median : 7.00   Median :0.00000   Median :3.000   Median :1.000  
##  Mean   : 6.52   Mean   :0.02873   Mean   :2.997   Mean   :0.684  
##  3rd Qu.:10.00   3rd Qu.:0.00000   3rd Qu.:5.000   3rd Qu.:1.000  
##  Max.   :12.00   Max.   :1.00000   Max.   :6.000   Max.   :1.000  
##    weathersit         temp             atemp              hum        
##  Min.   :1.000   Min.   :0.05913   Min.   :0.07907   Min.   :0.0000  
##  1st Qu.:1.000   1st Qu.:0.33708   1st Qu.:0.33784   1st Qu.:0.5200  
##  Median :1.000   Median :0.49833   Median :0.48673   Median :0.6267  
##  Mean   :1.395   Mean   :0.49538   Mean   :0.47435   Mean   :0.6279  
##  3rd Qu.:2.000   3rd Qu.:0.65542   3rd Qu.:0.60860   3rd Qu.:0.7302  
##  Max.   :3.000   Max.   :0.86167   Max.   :0.84090   Max.   :0.9725  
##    windspeed           casual         registered        cnt      
##  Min.   :0.02239   Min.   :   2.0   Min.   :  20   Min.   :  22  
##  1st Qu.:0.13495   1st Qu.: 315.5   1st Qu.:2497   1st Qu.:3152  
##  Median :0.18097   Median : 713.0   Median :3662   Median :4548  
##  Mean   :0.19049   Mean   : 848.2   Mean   :3656   Mean   :4504  
##  3rd Qu.:0.23321   3rd Qu.:1096.0   3rd Qu.:4776   3rd Qu.:5956  
##  Max.   :0.50746   Max.   :3410.0   Max.   :6946   Max.   :8714
```

#### 2. PRE-PROCESSING 

###### 2.1 CHECKING THE MISSING  VALUE

```r
sum(is.na(day_data))
```

```
## [1] 0
```

###### 2.2 ENCODING VARIABLE

We encode some variables as factors because initially they are encoded as dbl for practical reason so we reestablish the correct type of them.

```r
cols <- c("season", "yr", "weathersit", "workingday",'holiday','mnth','weekday')


new_data<-day_data %>%
       mutate_each_(funs(factor(.)),cols)

glimpse(new_data)
```

```
## Rows: 731
## Columns: 16
## $ instant    <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, …
## $ dteday     <date> 2011-01-01, 2011-01-02, 2011-01-03, 2011-01-04, 2011-01-05…
## $ season     <fct> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ yr         <fct> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ mnth       <fct> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ holiday    <fct> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0,…
## $ weekday    <fct> 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4,…
## $ workingday <fct> 0, 0, 1, 1, 1, 1, 1, 0, 0, 1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1,…
## $ weathersit <fct> 2, 2, 1, 1, 1, 1, 2, 2, 1, 1, 2, 1, 1, 1, 2, 1, 2, 2, 2, 2,…
## $ temp       <dbl> 0.3441670, 0.3634780, 0.1963640, 0.2000000, 0.2269570, 0.20…
## $ atemp      <dbl> 0.3636250, 0.3537390, 0.1894050, 0.2121220, 0.2292700, 0.23…
## $ hum        <dbl> 0.805833, 0.696087, 0.437273, 0.590435, 0.436957, 0.518261,…
## $ windspeed  <dbl> 0.1604460, 0.2485390, 0.2483090, 0.1602960, 0.1869000, 0.08…
## $ casual     <dbl> 331, 131, 120, 108, 82, 88, 148, 68, 54, 41, 43, 25, 38, 54…
## $ registered <dbl> 654, 670, 1229, 1454, 1518, 1518, 1362, 891, 768, 1280, 122…
## $ cnt        <dbl> 985, 801, 1349, 1562, 1600, 1606, 1510, 959, 822, 1321, 126…
```





#### 3. EDA

$HeatMap$ \
We use a heatmap to see clearly the correlation matrix: \
1. $temp$ and $atemp$ are high correllated , close to 1 so we can remove it one of them. \
2. $Registered$/ $Casual$ and $Count$ are highly correlated which indicates that most of the bikes that are rented are registered , one of them we can eliminate.


```r
new_data2<- new_data %>% dplyr:: select(-season,-yr,-weathersit,-workingday,-holiday,-mnth,-weekday,-dteday)
cor_matrix<-cor(new_data2)
corrplot(cor_matrix, method="number",tl.cex=0.5,number.digits = 1)
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

$Scatterplot$ \
1. $Temperature$ are positively correlated with $Count$, if $Temperature$ rise also $Count$.\
2. $WindSpeed$ are negatively correlated with $Count$, if $Wind$ rise  $Count$ will diminish.\
3. $Temperature \ feeling$ follows the same pattern of $Temperature$ , because they are highly correlated.

```r
temp_scatter<-ggplot(new_data, aes(x=temp, y=cnt)) +
  geom_jitter(aes(colour = temp,width = 0.15))+
  scale_color_gradient(low="blue", high="red")+
  xlab('temperature')+
  ylab('count')

humidity_scatter<-ggplot(new_data, aes(x=hum, y=cnt)) +
  geom_jitter(aes(colour = hum))+
  xlab('humidity')+
  ylab('count')

windspeed_scatter<-ggplot(new_data, aes(x=windspeed, y=cnt)) +
  geom_jitter(aes(colour=windspeed))+
  scale_color_gradient(low="grey", high="black")+
  xlab('windspeed')+
  ylab('count')
  

atemp_scatter<-ggplot(new_data, aes(x=atemp, y=cnt)) +
  geom_jitter(aes(colour=atemp))+
  scale_color_gradient(low="yellow", high="orange")+
  xlab('temperature feeled')+
  ylab('count')
  
  
temp_scatter+humidity_scatter+windspeed_scatter+atemp_scatter
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


Here we create a Dataframe that reports the total count for each $weathersit$.


```r
data_weather<-new_data  %>% group_by(weathersit) %>% distinct(weathersit,cnt) %>% summarize(count=sum(cnt)) %>% arrange(desc(count))

glimpse(data_weather)
```

```
## Rows: 3
## Columns: 2
## $ weathersit <fct> 1, 2, 3
## $ count      <dbl> 2194133, 984804, 37869
```


In this two graph we want to analyze the variable $weathersit$, firstly with geom_bar so we look to the frequency of each $weathersit$,and then with geom_col to see the overall count for each $weathersit$.


```r
j<-ggplot() + geom_col(data =data_weather, aes(x = weathersit, y = count,fill=weathersit))+
  scale_fill_discrete(name = "weathersit")+
  xlab('Weathersit')+
  labs(title = "The total count for each Weather ", caption = "1: Sunny,2: Cloudy,3:Rainy")
  
m<-ggplot(data = new_data) +
  geom_bar(mapping = aes(x = weathersit,fill=weathersit))+
  scale_fill_discrete(name = "weathersit")+
  xlab('Weathersit')+
  labs(title = "Bar chart ", caption = "1: Sunny,2: Cloudy,3:Rainy")

m+j
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-7-1.png)<!-- -->


Now our focus is on variable $Season$,so we decide to choose a boxplot for each $Season$.
We have observed that the highest median is registered in Summer and followed by Spring.
Furthermore, we spot that there is an outlier in $Winter$ that may be caused by a very hot day where people used bikes, and also another one in Fall, where there is a day with 0 count.


```r
ggplot(aes(x=season,y = cnt),data=new_data)+geom_boxplot(aes(fill=season)) + theme_bw()+
  xlab('Season')+
  ylab('Count')+
  scale_fill_discrete(name = "season")+
  labs(title = "Boxplot of Season ", caption = "1: Winter,2: Spring,3:Summer,4:Fall")
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-8-1.png)<!-- -->



We want to see the Trends across the $Year$ 2011 and 2012, in order to spot the peaks and downs of each $month$ per $Year$.
In 2011 we notice that with the beginning of spring and summer the $count$ of the bike has increased while with the incoming of autumn/winter the trend has decreased, same thing in 2012.
Furthermore, we notice the in 2012 the trend has increased so much rather than 2011.


```r
ggplot(data = new_data, aes(mnth, cnt)) +
  geom_line(color = "steelblue") +
  geom_point(color="steelblue") +
  xlab('Month')+
  ylab('Count')+
  facet_wrap(~yr)+
    labs(title = "Trend ", caption = "0: 2011,1: 2012")
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-9-1.png)<!-- -->




In this graph we analyze in depth the variable $Holiday$, firstly with a bar chart in order to
understand if the bike are rented more in holiday or not and we notice that people do not prefer using it
on holiday days.


```r
ggplot(data = new_data) +
  geom_bar(mapping = aes(x = holiday,fill=holiday))+
  scale_fill_discrete(name = "holiday")+
  xlab('holiday')
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

Furthermore, with the second graph we spot that during the weekend there are the most peaks of renting bikes, while when there is holiday we have few data so we cannot draw a conclusion.

```r
ggplot(data = new_data, aes(weekday, cnt)) +
  geom_line(color = "steelblue") +
  geom_point(color="steelblue") +
  xlab('Day')+
  ylab('Count')+
  facet_wrap(~holiday)+
    labs(title = "Trend ", caption = "0: Sunday,1: Monday,2: Tuesday,3:Wednesday,4:Thursday,5:Friday,6:Saturday")
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-11-1.png)<!-- -->




To see how our $Y$ is distributed in order to choose which models we have to applied, we notice that the
variable $cnt$ is distributed like a Normal, and we know that even if our Y is count but if it is large enough it can be approximately ~ N.


```r
ggplot(new_data, aes(x=cnt))+
  geom_histogram(color="darkblue", fill="lightblue",bins=30)+
  geom_vline(aes(xintercept=mean(cnt)),
            color="blue", linetype="dashed", size=1)
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-12-1.png)<!-- -->



### 4. Feature Engineering

1. As wee see in Correlation matrix, we can delete $casual$ or $registered$.
2. We remove $atemp$  because is highly correllated with $temp$
3. We remove $dteday$ because is not useful
4. We remove $workingday$ because gives the same information of $weekdays$

```r
new_data5<- new_data %>% dplyr:: select(-casual,-atemp,-dteday,-workingday)
glimpse(new_data5)
```

```
## Rows: 731
## Columns: 12
## $ instant    <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, …
## $ season     <fct> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ yr         <fct> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
## $ mnth       <fct> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
## $ holiday    <fct> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0,…
## $ weekday    <fct> 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4, 5, 6, 0, 1, 2, 3, 4,…
## $ weathersit <fct> 2, 2, 1, 1, 1, 1, 2, 2, 1, 1, 2, 1, 1, 1, 2, 1, 2, 2, 2, 2,…
## $ temp       <dbl> 0.3441670, 0.3634780, 0.1963640, 0.2000000, 0.2269570, 0.20…
## $ hum        <dbl> 0.805833, 0.696087, 0.437273, 0.590435, 0.436957, 0.518261,…
## $ windspeed  <dbl> 0.1604460, 0.2485390, 0.2483090, 0.1602960, 0.1869000, 0.08…
## $ registered <dbl> 654, 670, 1229, 1454, 1518, 1518, 1362, 891, 768, 1280, 122…
## $ cnt        <dbl> 985, 801, 1349, 1562, 1600, 1606, 1510, 959, 822, 1321, 126…
```



### 5. Model



```r
set.seed(123)
split.bike<-initial_split(new_data5,0.7)
train.bike<-training(split.bike)
test.bike<-testing(split.bike)
```





$POISSON\ MODEL$

We adopt for a Poisson model because $Y$ is count, but when we look at the summary we spot that there
is big difference between residual deviance and degrees of freedom so probably it is caused by overdispersion.
However, count data  are often overdispersed.This violates the assumption of a Poisson GLM, so our model
has understimated the standard errors, which has resulted in too low p-values.


```r
pois_mod<- glm(cnt~.,data=train.bike,family='poisson')
summary(pois_mod)
```

```
## 
## Call:
## glm(formula = cnt ~ ., family = "poisson", data = train.bike)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -35.756   -4.500    0.574    5.025   18.609  
## 
## Coefficients:
##               Estimate Std. Error  z value Pr(>|z|)    
## (Intercept)  7.268e+00  6.542e-03 1111.053  < 2e-16 ***
## instant     -1.185e-03  7.718e-05  -15.351  < 2e-16 ***
## season2      1.021e-01  4.541e-03   22.495  < 2e-16 ***
## season3      1.462e-01  5.331e-03   27.418  < 2e-16 ***
## season4      1.643e-01  5.491e-03   29.911  < 2e-16 ***
## yr1          4.517e-01  2.847e-02   15.867  < 2e-16 ***
## mnth2        1.421e-01  5.046e-03   28.156  < 2e-16 ***
## mnth3        2.275e-01  6.823e-03   33.346  < 2e-16 ***
## mnth4        2.091e-01  9.653e-03   21.657  < 2e-16 ***
## mnth5        2.378e-01  1.173e-02   20.269  < 2e-16 ***
## mnth6        1.608e-01  1.383e-02   11.628  < 2e-16 ***
## mnth7        1.296e-01  1.589e-02    8.153 3.54e-16 ***
## mnth8        1.615e-01  1.800e-02    8.971  < 2e-16 ***
## mnth9        2.539e-01  1.993e-02   12.738  < 2e-16 ***
## mnth10       3.252e-01  2.171e-02   14.976  < 2e-16 ***
## mnth11       4.258e-01  2.381e-02   17.882  < 2e-16 ***
## mnth12       3.835e-01  2.586e-02   14.831  < 2e-16 ***
## holiday1     8.692e-02  4.328e-03   20.084  < 2e-16 ***
## weekday1    -2.121e-01  2.809e-03  -75.508  < 2e-16 ***
## weekday2    -2.482e-01  2.919e-03  -85.034  < 2e-16 ***
## weekday3    -2.632e-01  2.969e-03  -88.621  < 2e-16 ***
## weekday4    -2.582e-01  2.968e-03  -86.975  < 2e-16 ***
## weekday5    -1.931e-01  2.832e-03  -68.183  < 2e-16 ***
## weekday6     1.441e-02  2.568e-03    5.610 2.03e-08 ***
## weathersit2 -1.495e-02  1.951e-03   -7.665 1.79e-14 ***
## weathersit3 -2.253e-01  7.014e-03  -32.124  < 2e-16 ***
## temp         6.824e-01  1.070e-02   63.785  < 2e-16 ***
## hum         -1.401e-01  7.771e-03  -18.026  < 2e-16 ***
## windspeed   -2.014e-01  1.029e-02  -19.567  < 2e-16 ***
## registered   2.414e-04  1.203e-06  200.701  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for poisson family taken to be 1)
## 
##     Null deviance: 485309  on 511  degrees of freedom
## Residual deviance:  33528  on 482  degrees of freedom
## AIC: 38767
## 
## Number of Fisher Scoring iterations: 4
```

```r
exp(coef(pois_mod))
```

```
##  (Intercept)      instant      season2      season3      season4          yr1 
## 1434.0448798    0.9988158    1.1075415    1.1573884    1.1785145    1.5709180 
##        mnth2        mnth3        mnth4        mnth5        mnth6        mnth7 
##    1.1526553    1.2554966    1.2325094    1.2685182    1.1744288    1.1383597 
##        mnth8        mnth9       mnth10       mnth11       mnth12     holiday1 
##    1.1752337    1.2890704    1.3842901    1.5308075    1.4674424    1.0908093 
##     weekday1     weekday2     weekday3     weekday4     weekday5     weekday6 
##    0.8088781    0.7801657    0.7686235    0.7724618    0.8244056    1.0145094 
##  weathersit2  weathersit3         temp          hum    windspeed   registered 
##    0.9851585    0.7982684    1.9786034    0.8692918    0.8176175    1.0002414
```

```r
# Every time I increase temp by 1 lambda will increase by 1.978
# Every time I increase hum by 1 lambda will decrease by 0.8692
```



#### 5.1 DIAGNOSTIC OF THE MODEL

Before we try to spot if there is overdispersion or not , we firstly check the goodness of fit and we have
to reject the $H_{0}$ , so our model doesn't fit data well.


```r
#probability to observed extreme value of deviance that we observed
1-pchisq(pois_mod$deviance,pois_mod$df.residual)
```

```
## [1] 0
```


There are two ways to check overdispersion: \
1.The Pearson <img src="https://render.githubusercontent.com/render/math?math=chi^{2}"> dispersion statistic \
2. Plot the approximations of Mean vs Variance  \

$The \ Pearson$ $\chi^2$ $dispersion\ statistic$

Firstly, we do an overdispersion test to check if there is any presence of overdispersion and we notice that $\phi$, the dispersion parameter, is far away from 1, so there is a great problem of overdispersion.


```r
P__disp(pois_mod)
```

```
## pearson.chi2   dispersion 
##  31571.60912     65.50126
```


$Plot\ Mean\ vs \ Variance$ \
We want to check if the assumption of $\mu$ = $\sigma^2$, so if they are equal they should stand in that following line.
We notice that there is a systematic variation that line then it means that there is a hidden relationship between variance and expectation.

```r
#Check if mean is equal to variance
#approximate the expected value with your predicted
E_hat=predict(pois_mod,type="response")
# response directly give the lambda
# what you observed and the expected value
V_hat=(train.bike$cnt-E_hat)^2
# the log shift ups because the product of log is the sum of log
plot(log(E_hat),log(V_hat))
abline(0,1)
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-18-1.png)<!-- -->


$Outlier$ \
Furthermore, we try to detect some $outlier$ to see if it can also cause some troubles to the model and
we spot the observation 48 of training set but it don't seem to be a real outlier because it is  a low count but in winter so we would expect it, so it  not probably the cause.

```r
# we make an half normal plot of studentized residuals 
halfnorm(rstudent(pois_mod))
```

![](Applied_Linear_Model_Project_files/figure-html/unnamed-chunk-19-1.png)<!-- -->

```r
# we want  see the value of count of that observation
train.bike$cnt[48]
```

```
## [1] 605
```

```r
#inspect the see the feature of that particular observation
train.bike %>% filter(cnt==605)
```

```
## # A tibble: 1 x 12
##   instant season yr    mnth  holiday weekday weathersit  temp   hum windspeed
##     <dbl> <fct>  <fct> <fct> <fct>   <fct>   <fct>      <dbl> <dbl>     <dbl>
## 1      65 1      0     3     0       0       2          0.377 0.948     0.343
## # … with 2 more variables: registered <dbl>, cnt <dbl>
```




There are two main approaches that we can take to deal with over-dispersed count data in GLMs: \

1. Fit a quasi-Poisson GLM \
2. Fit a negative binomial GLM


$Quasi Poisson$

We have to deal with $overdispersion$,so we adopted the Quasi Poisson model that will result in the same coefficients as The Poisson model  but with different Standard errors and p-values thanks to his dispersion  parameter $\phi$, that tells how much the variance linearly changes in relation to the mean.
Furthermore, we can’t obtain an AIC value for quasi-Poisson models, because these models use quasi-likelihood rather than true likelihood.


```r
quasipois_mod<- glm(cnt~.,data=train.bike,family=quasipoisson)
summary(quasipois_mod)
```

```
## 
## Call:
## glm(formula = cnt ~ ., family = quasipoisson, data = train.bike)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -35.756   -4.500    0.574    5.025   18.609  
## 
## Coefficients:
##               Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  7.268e+00  5.294e-02 137.281  < 2e-16 ***
## instant     -1.185e-03  6.247e-04  -1.897 0.058450 .  
## season2      1.021e-01  3.675e-02   2.779 0.005658 ** 
## season3      1.462e-01  4.315e-02   3.388 0.000762 ***
## season4      1.643e-01  4.444e-02   3.696 0.000244 ***
## yr1          4.517e-01  2.304e-01   1.961 0.050509 .  
## mnth2        1.421e-01  4.084e-02   3.479 0.000549 ***
## mnth3        2.275e-01  5.522e-02   4.120 4.46e-05 ***
## mnth4        2.091e-01  7.812e-02   2.676 0.007706 ** 
## mnth5        2.378e-01  9.497e-02   2.504 0.012594 *  
## mnth6        1.608e-01  1.119e-01   1.437 0.151439    
## mnth7        1.296e-01  1.286e-01   1.007 0.314245    
## mnth8        1.615e-01  1.457e-01   1.109 0.268194    
## mnth9        2.539e-01  1.613e-01   1.574 0.116160    
## mnth10       3.252e-01  1.757e-01   1.850 0.064856 .  
## mnth11       4.258e-01  1.927e-01   2.210 0.027606 *  
## mnth12       3.835e-01  2.093e-01   1.833 0.067490 .  
## holiday1     8.692e-02  3.503e-02   2.482 0.013419 *  
## weekday1    -2.121e-01  2.273e-02  -9.330  < 2e-16 ***
## weekday2    -2.482e-01  2.363e-02 -10.507  < 2e-16 ***
## weekday3    -2.632e-01  2.403e-02 -10.950  < 2e-16 ***
## weekday4    -2.582e-01  2.402e-02 -10.747  < 2e-16 ***
## weekday5    -1.931e-01  2.292e-02  -8.425 4.19e-16 ***
## weekday6     1.441e-02  2.078e-02   0.693 0.488561    
## weathersit2 -1.495e-02  1.579e-02  -0.947 0.344086    
## weathersit3 -2.253e-01  5.676e-02  -3.969 8.31e-05 ***
## temp         6.824e-01  8.658e-02   7.881 2.18e-14 ***
## hum         -1.401e-01  6.289e-02  -2.227 0.026394 *  
## windspeed   -2.014e-01  8.329e-02  -2.418 0.015992 *  
## registered   2.414e-04  9.733e-06  24.798  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for quasipoisson family taken to be 65.50127)
## 
##     Null deviance: 485309  on 511  degrees of freedom
## Residual deviance:  33528  on 482  degrees of freedom
## AIC: NA
## 
## Number of Fisher Scoring iterations: 4
```

As we notice, there is still a big difference between residual Deviance and degrees of freedom.
From the dispersion test we spot that there is a severe dispersion, so probably we have to adopt a 
Negative Binomial model.
Furthermore, we have to guess that there is non linear relationship between the variance and the mean,
therefore the quasi Poisson model would not be appropriate because it assumes that the variance increases linearly as a function of the mean.



$Negative \ Binomial$

The Negative Binomial is Poisson regression model with an extra parameter $\theta$, which may account for overdispersion. \
$Var=\mu+\frac{\mu^2}{\theta}$ \
In comparison to The Quasi-Poisson regression model, the negative binomial models assumes that the variance can increase by the square of the mean.



```r
negative_bin <- glm.nb(cnt ~., data=train.bike)
summary(negative_bin)
```

```
## 
## Call:
## glm.nb(formula = cnt ~ ., data = train.bike, init.theta = 39.88797352, 
##     link = log)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -5.3964  -0.5444   0.0618   0.5740   2.7254  
## 
## Coefficients:
##               Estimate Std. Error z value Pr(>|z|)    
## (Intercept)  7.184e+00  6.269e-02 114.587  < 2e-16 ***
## instant     -1.435e-03  8.106e-04  -1.770 0.076744 .  
## season2      1.034e-01  4.601e-02   2.248 0.024602 *  
## season3      1.736e-01  5.446e-02   3.187 0.001436 ** 
## season4      1.831e-01  4.964e-02   3.689 0.000225 ***
## yr1          5.408e-01  2.995e-01   1.806 0.070966 .  
## mnth2        1.403e-01  4.291e-02   3.271 0.001072 ** 
## mnth3        2.091e-01  6.383e-02   3.276 0.001052 ** 
## mnth4        1.945e-01  9.569e-02   2.032 0.042122 *  
## mnth5        2.295e-01  1.190e-01   1.929 0.053765 .  
## mnth6        1.213e-01  1.423e-01   0.852 0.393977    
## mnth7        8.051e-02  1.665e-01   0.484 0.628650    
## mnth8        1.156e-01  1.893e-01   0.611 0.541355    
## mnth9        2.622e-01  2.104e-01   1.246 0.212601    
## mnth10       3.656e-01  2.318e-01   1.577 0.114720    
## mnth11       4.740e-01  2.548e-01   1.860 0.062868 .  
## mnth12       4.351e-01  2.760e-01   1.576 0.114981    
## holiday1     5.684e-02  4.315e-02   1.317 0.187817    
## weekday1    -1.898e-01  2.860e-02  -6.637 3.20e-11 ***
## weekday2    -2.304e-01  2.930e-02  -7.865 3.70e-15 ***
## weekday3    -2.383e-01  2.979e-02  -8.001 1.24e-15 ***
## weekday4    -2.367e-01  3.020e-02  -7.837 4.61e-15 ***
## weekday5    -1.601e-01  2.898e-02  -5.524 3.32e-08 ***
## weekday6    -3.079e-03  2.681e-02  -0.115 0.908543    
## weathersit2 -8.835e-03  2.033e-02  -0.435 0.663879    
## weathersit3 -2.152e-01  5.573e-02  -3.861 0.000113 ***
## temp         8.383e-01  1.103e-01   7.599 2.99e-14 ***
## hum         -2.257e-01  7.972e-02  -2.831 0.004640 ** 
## windspeed   -2.978e-01  1.050e-01  -2.836 0.004565 ** 
## registered   2.661e-04  1.197e-05  22.234  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for Negative Binomial(39.888) family taken to be 1)
## 
##     Null deviance: 5269.84  on 511  degrees of freedom
## Residual deviance:  519.15  on 482  degrees of freedom
## AIC: 8119
## 
## Number of Fisher Scoring iterations: 1
## 
## 
##               Theta:  39.89 
##           Std. Err.:  2.54 
## 
##  2 x log-likelihood:  -8056.953
```



We notice that the distance between Residual Deviance and degrees of freedom has been reduce so much.



#### 5.2 MODEL SELECTION
We want to choose the model with lower AIC and possibly less feature in order to have a simpler method
so we compute the function step.
After computed it, we notice that we can remove variable $holiday$. \
Furthermore, we want to spot if there any differences between the model with holiday and without it so we
compute anova and we notice that the models have no differences.


```r
final_model<-step(negative_bin)
```



```r
final_model
```

```
## 
## Call:  glm.nb(formula = cnt ~ instant + season + yr + mnth + weekday + 
##     weathersit + temp + hum + windspeed + registered, data = train.bike, 
##     init.theta = 39.7485615, link = log)
## 
## Coefficients:
## (Intercept)      instant      season2      season3      season4          yr1  
##   7.1882672   -0.0014890    0.1055122    0.1760005    0.1857996    0.5707561  
##       mnth2        mnth3        mnth4        mnth5        mnth6        mnth7  
##   0.1418003    0.2114111    0.1999511    0.2376462    0.1272657    0.0863576  
##       mnth8        mnth9       mnth10       mnth11       mnth12     weekday1  
##   0.1235627    0.2792348    0.3862739    0.4957345    0.4568133   -0.1765777  
##    weekday2     weekday3     weekday4     weekday5     weekday6  weathersit2  
##  -0.2237705   -0.2311693   -0.2286784   -0.1529260   -0.0011872   -0.0116079  
## weathersit3         temp          hum    windspeed   registered  
##  -0.2242120    0.8648798   -0.2336641   -0.3086467    0.0002605  
## 
## Degrees of Freedom: 511 Total (i.e. Null);  483 Residual
## Null Deviance:	    5252 
## Residual Deviance: 519.1 	AIC: 8119
```

```r
anova(final_model,negative_bin)
```

```
## Likelihood ratio tests of Negative Binomial Models
## 
## Response: cnt
##                                                                                                 Model
## 1           instant + season + yr + mnth + weekday + weathersit + temp + hum + windspeed + registered
## 2 instant + season + yr + mnth + holiday + weekday + weathersit + temp + hum + windspeed + registered
##      theta Resid. df    2 x log-lik.   Test    df LR stat.   Pr(Chi)
## 1 39.74856       483       -8058.688                                
## 2 39.88797       482       -8056.953 1 vs 2     1 1.735436 0.1877182
```




$The\ Exploratory \ Power$

When we ran linear models, we used the coefficient of determination, or R2 to assess how much of the variability in our response variable is explained by a given model. R2 is based on the sums of squares of our model, and so cannot be calculated for GLMs. Instead, we can calculate the analogous “deviance explained” by our model:


```r
nb_dev.null <- final_model$null.deviance
nb_dev.resid <- final_model$deviance
nb_dev.explained <- (nb_dev.null-nb_dev.resid)/nb_dev.null
# Same formula 1 - residual deviance/null deviance
nb_dev.explained
```

```
## [1] 0.9011535
```







