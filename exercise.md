---
 assignment  : Practical Machine Learning - Course Project
 author      : Alicia Brown
---



# Exercise Prediction
#### by Alicia Brown, Spring 2014, Practical Machine Learning Course Project
========================================================

## Synopsis: 
Predict activity quality from activity monitors

#### Load libraries and setup working directory

```r
# getwd()
setwd("~/documents/courses/practical machine learning/project")

# software environment
sessionInfo()
```

```
## R version 3.0.3 (2014-03-06)
## Platform: x86_64-apple-darwin10.8.0 (64-bit)
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] knitr_1.5
## 
## loaded via a namespace (and not attached):
## [1] evaluate_0.5.3 formatR_0.10   stringr_0.6.2  tools_3.0.3
```


========================================================

## Data Processing

#### Load data

```r
trainDataURL <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
trainDataFile <- "./data/pml-training.csv"
testDataURL <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
testDataFile <- "./data/pml-testing.csv"

# Download the training and test data files
if (!file.exists(trainDataFile)) {
    download.file(trainDataURL, destfile = trainDataFile, method = "curl")
    dateDownloaded <- date()
}
if (!file.exists(testDataFile)) {
    download.file(testDataURL, destfile = testDataFile, method = "curl")
}

if (file.exists(trainDataFile)) {
    raw <- read.csv(trainDataFile, header = TRUE, nrows = 1000, stringsAsFactors = FALSE)
    # classes <- sapply(init,class) raw <- read.csv(trainDataFile, header =
    # TRUE, colClasses = classes, stringsAsFactors = FALSE)
}
```


#### Examine the data

```r
head(raw)
```

```
##   X user_name raw_timestamp_part_1 raw_timestamp_part_2   cvtd_timestamp
## 1 1  carlitos           1323084231               788290 05/12/2011 11:23
## 2 2  carlitos           1323084231               808298 05/12/2011 11:23
## 3 3  carlitos           1323084231               820366 05/12/2011 11:23
## 4 4  carlitos           1323084232               120339 05/12/2011 11:23
## 5 5  carlitos           1323084232               196328 05/12/2011 11:23
## 6 6  carlitos           1323084232               304277 05/12/2011 11:23
##   new_window num_window roll_belt pitch_belt yaw_belt total_accel_belt
## 1         no         11      1.41       8.07    -94.4                3
## 2         no         11      1.41       8.07    -94.4                3
## 3         no         11      1.42       8.07    -94.4                3
## 4         no         12      1.48       8.05    -94.4                3
## 5         no         12      1.48       8.07    -94.4                3
## 6         no         12      1.45       8.06    -94.4                3
##   kurtosis_roll_belt kurtosis_picth_belt kurtosis_yaw_belt
## 1                                                         
## 2                                                         
## 3                                                         
## 4                                                         
## 5                                                         
## 6                                                         
##   skewness_roll_belt skewness_roll_belt.1 skewness_yaw_belt max_roll_belt
## 1                                                                      NA
## 2                                                                      NA
## 3                                                                      NA
## 4                                                                      NA
## 5                                                                      NA
## 6                                                                      NA
##   max_picth_belt max_yaw_belt min_roll_belt min_pitch_belt min_yaw_belt
## 1             NA                         NA             NA             
## 2             NA                         NA             NA             
## 3             NA                         NA             NA             
## 4             NA                         NA             NA             
## 5             NA                         NA             NA             
## 6             NA                         NA             NA             
##   amplitude_roll_belt amplitude_pitch_belt amplitude_yaw_belt
## 1                  NA                   NA                   
## 2                  NA                   NA                   
## 3                  NA                   NA                   
## 4                  NA                   NA                   
## 5                  NA                   NA                   
## 6                  NA                   NA                   
##   var_total_accel_belt avg_roll_belt stddev_roll_belt var_roll_belt
## 1                   NA            NA               NA            NA
## 2                   NA            NA               NA            NA
## 3                   NA            NA               NA            NA
## 4                   NA            NA               NA            NA
## 5                   NA            NA               NA            NA
## 6                   NA            NA               NA            NA
##   avg_pitch_belt stddev_pitch_belt var_pitch_belt avg_yaw_belt
## 1             NA                NA             NA           NA
## 2             NA                NA             NA           NA
## 3             NA                NA             NA           NA
## 4             NA                NA             NA           NA
## 5             NA                NA             NA           NA
## 6             NA                NA             NA           NA
##   stddev_yaw_belt var_yaw_belt gyros_belt_x gyros_belt_y gyros_belt_z
## 1              NA           NA         0.00         0.00        -0.02
## 2              NA           NA         0.02         0.00        -0.02
## 3              NA           NA         0.00         0.00        -0.02
## 4              NA           NA         0.02         0.00        -0.03
## 5              NA           NA         0.02         0.02        -0.02
## 6              NA           NA         0.02         0.00        -0.02
##   accel_belt_x accel_belt_y accel_belt_z magnet_belt_x magnet_belt_y
## 1          -21            4           22            -3           599
## 2          -22            4           22            -7           608
## 3          -20            5           23            -2           600
## 4          -22            3           21            -6           604
## 5          -21            2           24            -6           600
## 6          -21            4           21             0           603
##   magnet_belt_z roll_arm pitch_arm yaw_arm total_accel_arm var_accel_arm
## 1          -313     -128      22.5    -161              34            NA
## 2          -311     -128      22.5    -161              34            NA
## 3          -305     -128      22.5    -161              34            NA
## 4          -310     -128      22.1    -161              34            NA
## 5          -302     -128      22.1    -161              34            NA
## 6          -312     -128      22.0    -161              34            NA
##   avg_roll_arm stddev_roll_arm var_roll_arm avg_pitch_arm stddev_pitch_arm
## 1           NA              NA           NA            NA               NA
## 2           NA              NA           NA            NA               NA
## 3           NA              NA           NA            NA               NA
## 4           NA              NA           NA            NA               NA
## 5           NA              NA           NA            NA               NA
## 6           NA              NA           NA            NA               NA
##   var_pitch_arm avg_yaw_arm stddev_yaw_arm var_yaw_arm gyros_arm_x
## 1            NA          NA             NA          NA        0.00
## 2            NA          NA             NA          NA        0.02
## 3            NA          NA             NA          NA        0.02
## 4            NA          NA             NA          NA        0.02
## 5            NA          NA             NA          NA        0.00
## 6            NA          NA             NA          NA        0.02
##   gyros_arm_y gyros_arm_z accel_arm_x accel_arm_y accel_arm_z magnet_arm_x
## 1        0.00       -0.02        -288         109        -123         -368
## 2       -0.02       -0.02        -290         110        -125         -369
## 3       -0.02       -0.02        -289         110        -126         -368
## 4       -0.03        0.02        -289         111        -123         -372
## 5       -0.03        0.00        -289         111        -123         -374
## 6       -0.03        0.00        -289         111        -122         -369
##   magnet_arm_y magnet_arm_z kurtosis_roll_arm kurtosis_picth_arm
## 1          337          516                NA                   
## 2          337          513                NA                   
## 3          344          513                NA                   
## 4          344          512                NA                   
## 5          337          506                NA                   
## 6          342          513                NA                   
##   kurtosis_yaw_arm skewness_roll_arm skewness_pitch_arm skewness_yaw_arm
## 1                                 NA                                    
## 2                                 NA                                    
## 3                                 NA                                    
## 4                                 NA                                    
## 5                                 NA                                    
## 6                                 NA                                    
##   max_roll_arm max_picth_arm max_yaw_arm min_roll_arm min_pitch_arm
## 1           NA            NA          NA           NA            NA
## 2           NA            NA          NA           NA            NA
## 3           NA            NA          NA           NA            NA
## 4           NA            NA          NA           NA            NA
## 5           NA            NA          NA           NA            NA
## 6           NA            NA          NA           NA            NA
##   min_yaw_arm amplitude_roll_arm amplitude_pitch_arm amplitude_yaw_arm
## 1          NA                 NA                  NA                NA
## 2          NA                 NA                  NA                NA
## 3          NA                 NA                  NA                NA
## 4          NA                 NA                  NA                NA
## 5          NA                 NA                  NA                NA
## 6          NA                 NA                  NA                NA
##   roll_dumbbell pitch_dumbbell yaw_dumbbell kurtosis_roll_dumbbell
## 1         13.05         -70.49       -84.87                     NA
## 2         13.13         -70.64       -84.71                     NA
## 3         12.85         -70.28       -85.14                     NA
## 4         13.43         -70.39       -84.87                     NA
## 5         13.38         -70.43       -84.85                     NA
## 6         13.38         -70.82       -84.47                     NA
##   kurtosis_picth_dumbbell kurtosis_yaw_dumbbell skewness_roll_dumbbell
## 1                      NA                                           NA
## 2                      NA                                           NA
## 3                      NA                                           NA
## 4                      NA                                           NA
## 5                      NA                                           NA
## 6                      NA                                           NA
##   skewness_pitch_dumbbell skewness_yaw_dumbbell max_roll_dumbbell
## 1                      NA                                      NA
## 2                      NA                                      NA
## 3                      NA                                      NA
## 4                      NA                                      NA
## 5                      NA                                      NA
## 6                      NA                                      NA
##   max_picth_dumbbell max_yaw_dumbbell min_roll_dumbbell min_pitch_dumbbell
## 1                 NA               NA                NA                 NA
## 2                 NA               NA                NA                 NA
## 3                 NA               NA                NA                 NA
## 4                 NA               NA                NA                 NA
## 5                 NA               NA                NA                 NA
## 6                 NA               NA                NA                 NA
##   min_yaw_dumbbell amplitude_roll_dumbbell amplitude_pitch_dumbbell
## 1               NA                      NA                       NA
## 2               NA                      NA                       NA
## 3               NA                      NA                       NA
## 4               NA                      NA                       NA
## 5               NA                      NA                       NA
## 6               NA                      NA                       NA
##   amplitude_yaw_dumbbell total_accel_dumbbell var_accel_dumbbell
## 1                     NA                   37                 NA
## 2                     NA                   37                 NA
## 3                     NA                   37                 NA
## 4                     NA                   37                 NA
## 5                     NA                   37                 NA
## 6                     NA                   37                 NA
##   avg_roll_dumbbell stddev_roll_dumbbell var_roll_dumbbell
## 1                NA                   NA                NA
## 2                NA                   NA                NA
## 3                NA                   NA                NA
## 4                NA                   NA                NA
## 5                NA                   NA                NA
## 6                NA                   NA                NA
##   avg_pitch_dumbbell stddev_pitch_dumbbell var_pitch_dumbbell
## 1                 NA                    NA                 NA
## 2                 NA                    NA                 NA
## 3                 NA                    NA                 NA
## 4                 NA                    NA                 NA
## 5                 NA                    NA                 NA
## 6                 NA                    NA                 NA
##   avg_yaw_dumbbell stddev_yaw_dumbbell var_yaw_dumbbell gyros_dumbbell_x
## 1               NA                  NA               NA                0
## 2               NA                  NA               NA                0
## 3               NA                  NA               NA                0
## 4               NA                  NA               NA                0
## 5               NA                  NA               NA                0
## 6               NA                  NA               NA                0
##   gyros_dumbbell_y gyros_dumbbell_z accel_dumbbell_x accel_dumbbell_y
## 1            -0.02             0.00             -234               47
## 2            -0.02             0.00             -233               47
## 3            -0.02             0.00             -232               46
## 4            -0.02            -0.02             -232               48
## 5            -0.02             0.00             -233               48
## 6            -0.02             0.00             -234               48
##   accel_dumbbell_z magnet_dumbbell_x magnet_dumbbell_y magnet_dumbbell_z
## 1             -271              -559               293               -65
## 2             -269              -555               296               -64
## 3             -270              -561               298               -63
## 4             -269              -552               303               -60
## 5             -270              -554               292               -68
## 6             -269              -558               294               -66
##   roll_forearm pitch_forearm yaw_forearm kurtosis_roll_forearm
## 1         28.4         -63.9        -153                      
## 2         28.3         -63.9        -153                      
## 3         28.3         -63.9        -152                      
## 4         28.1         -63.9        -152                      
## 5         28.0         -63.9        -152                      
## 6         27.9         -63.9        -152                      
##   kurtosis_picth_forearm kurtosis_yaw_forearm skewness_roll_forearm
## 1                                                                  
## 2                                                                  
## 3                                                                  
## 4                                                                  
## 5                                                                  
## 6                                                                  
##   skewness_pitch_forearm skewness_yaw_forearm max_roll_forearm
## 1                                                           NA
## 2                                                           NA
## 3                                                           NA
## 4                                                           NA
## 5                                                           NA
## 6                                                           NA
##   max_picth_forearm max_yaw_forearm min_roll_forearm min_pitch_forearm
## 1                NA                               NA                NA
## 2                NA                               NA                NA
## 3                NA                               NA                NA
## 4                NA                               NA                NA
## 5                NA                               NA                NA
## 6                NA                               NA                NA
##   min_yaw_forearm amplitude_roll_forearm amplitude_pitch_forearm
## 1                                     NA                      NA
## 2                                     NA                      NA
## 3                                     NA                      NA
## 4                                     NA                      NA
## 5                                     NA                      NA
## 6                                     NA                      NA
##   amplitude_yaw_forearm total_accel_forearm var_accel_forearm
## 1                                        36                NA
## 2                                        36                NA
## 3                                        36                NA
## 4                                        36                NA
## 5                                        36                NA
## 6                                        36                NA
##   avg_roll_forearm stddev_roll_forearm var_roll_forearm avg_pitch_forearm
## 1               NA                  NA               NA                NA
## 2               NA                  NA               NA                NA
## 3               NA                  NA               NA                NA
## 4               NA                  NA               NA                NA
## 5               NA                  NA               NA                NA
## 6               NA                  NA               NA                NA
##   stddev_pitch_forearm var_pitch_forearm avg_yaw_forearm
## 1                   NA                NA              NA
## 2                   NA                NA              NA
## 3                   NA                NA              NA
## 4                   NA                NA              NA
## 5                   NA                NA              NA
## 6                   NA                NA              NA
##   stddev_yaw_forearm var_yaw_forearm gyros_forearm_x gyros_forearm_y
## 1                 NA              NA            0.03            0.00
## 2                 NA              NA            0.02            0.00
## 3                 NA              NA            0.03           -0.02
## 4                 NA              NA            0.02           -0.02
## 5                 NA              NA            0.02            0.00
## 6                 NA              NA            0.02           -0.02
##   gyros_forearm_z accel_forearm_x accel_forearm_y accel_forearm_z
## 1           -0.02             192             203            -215
## 2           -0.02             192             203            -216
## 3            0.00             196             204            -213
## 4            0.00             189             206            -214
## 5           -0.02             189             206            -214
## 6           -0.03             193             203            -215
##   magnet_forearm_x magnet_forearm_y magnet_forearm_z classe
## 1              -17              654              476      A
## 2              -18              661              473      A
## 3              -18              658              469      A
## 4              -16              658              469      A
## 5              -17              655              473      A
## 6               -9              660              478      A
```

```r
summary(raw)
```

```
##        X         user_name         raw_timestamp_part_1
##  Min.   :   1   Length:1000        Min.   :1.32e+09    
##  1st Qu.: 251   Class :character   1st Qu.:1.32e+09    
##  Median : 500   Mode  :character   Median :1.32e+09    
##  Mean   : 500                      Mean   :1.32e+09    
##  3rd Qu.: 750                      3rd Qu.:1.32e+09    
##  Max.   :1000                      Max.   :1.32e+09    
##                                                        
##  raw_timestamp_part_2 cvtd_timestamp      new_window       
##  Min.   :   309       Length:1000        Length:1000       
##  1st Qu.:248288       Class :character   Class :character  
##  Median :500316       Mode  :character   Mode  :character  
##  Mean   :489992                                            
##  3rd Qu.:736338                                            
##  Max.   :996339                                            
##                                                            
##    num_window      roll_belt        pitch_belt        yaw_belt     
##  Min.   : 11.0   Min.   :  0.84   Min.   :-45.00   Min.   :-94.40  
##  1st Qu.: 49.0   1st Qu.:123.00   1st Qu.:-39.60   1st Qu.: -3.80  
##  Median : 59.0   Median :124.00   Median : 25.60   Median : -1.54  
##  Mean   : 87.9   Mean   :105.06   Mean   :  4.11   Mean   : 30.60  
##  3rd Qu.:177.0   3rd Qu.:128.00   3rd Qu.: 26.30   3rd Qu.:161.00  
##  Max.   :189.0   Max.   :130.00   Max.   : 28.90   Max.   :179.00  
##                                                                    
##  total_accel_belt kurtosis_roll_belt kurtosis_picth_belt
##  Min.   : 3.0     Length:1000        Length:1000        
##  1st Qu.:18.0     Class :character   Class :character   
##  Median :19.0     Mode  :character   Mode  :character   
##  Mean   :16.6                                           
##  3rd Qu.:20.0                                           
##  Max.   :21.0                                           
##                                                         
##  kurtosis_yaw_belt  skewness_roll_belt skewness_roll_belt.1
##  Length:1000        Length:1000        Length:1000         
##  Class :character   Class :character   Class :character    
##  Mode  :character   Mode  :character   Mode  :character    
##                                                            
##                                                            
##                                                            
##                                                            
##  skewness_yaw_belt  max_roll_belt   max_picth_belt max_yaw_belt      
##  Length:1000        Min.   :-94.3   Min.   : 3.0   Length:1000       
##  Class :character   1st Qu.: -3.2   1st Qu.:18.8   Class :character  
##  Mode  :character   Median : -1.7   Median :20.0   Mode  :character  
##                     Mean   : 32.0   Mean   :17.0                     
##                     3rd Qu.:161.0   3rd Qu.:20.0                     
##                     Max.   :177.0   Max.   :21.0                     
##                     NA's   :976     NA's   :976                      
##  min_roll_belt   min_pitch_belt min_yaw_belt       amplitude_roll_belt
##  Min.   :-94.4   Min.   : 3.0   Length:1000        Min.   :0.0        
##  1st Qu.: -4.1   1st Qu.:17.0   Class :character   1st Qu.:0.4        
##  Median : -3.0   Median :18.0   Mode  :character   Median :1.1        
##  Mean   : 30.3   Mean   :15.9                      Mean   :1.7        
##  3rd Qu.:160.2   3rd Qu.:19.0                      3rd Qu.:1.7        
##  Max.   :168.0   Max.   :20.0                      Max.   :9.0        
##  NA's   :976     NA's   :976                       NA's   :976        
##  amplitude_pitch_belt amplitude_yaw_belt var_total_accel_belt
##  Min.   :0            Length:1000        Min.   :0.0         
##  1st Qu.:1            Class :character   1st Qu.:0.0         
##  Median :1            Mode  :character   Median :0.2         
##  Mean   :1                               Mean   :0.1         
##  3rd Qu.:1                               3rd Qu.:0.2         
##  Max.   :4                               Max.   :0.4         
##  NA's   :976                             NA's   :976         
##  avg_roll_belt stddev_roll_belt var_roll_belt avg_pitch_belt 
##  Min.   :  1   Min.   :0.0      Min.   :0.0   Min.   :-41.5  
##  1st Qu.:122   1st Qu.:0.2      1st Qu.:0.0   1st Qu.:-39.6  
##  Median :124   Median :0.4      Median :0.2   Median : 21.6  
##  Mean   :105   Mean   :0.4      Mean   :0.3   Mean   :  3.5  
##  3rd Qu.:128   3rd Qu.:0.5      3rd Qu.:0.3   3rd Qu.: 26.1  
##  Max.   :130   Max.   :1.1      Max.   :1.3   Max.   : 28.1  
##  NA's   :976   NA's   :976      NA's   :976   NA's   :976    
##  stddev_pitch_belt var_pitch_belt  avg_yaw_belt   stddev_yaw_belt
##  Min.   :0.0       Min.   :0.0    Min.   :-94.4   Min.   :0.0    
##  1st Qu.:0.1       1st Qu.:0.0    1st Qu.: -3.5   1st Qu.:0.1    
##  Median :0.2       Median :0.0    Median : -2.2   Median :0.4    
##  Mean   :0.3       Mean   :0.1    Mean   : 31.1   Mean   :0.5    
##  3rd Qu.:0.3       3rd Qu.:0.1    3rd Qu.:160.9   3rd Qu.:0.6    
##  Max.   :1.0       Max.   :1.0    Max.   :172.2   Max.   :2.8    
##  NA's   :976       NA's   :976    NA's   :976     NA's   :976    
##   var_yaw_belt  gyros_belt_x     gyros_belt_y      gyros_belt_z   
##  Min.   :0.0   Min.   :-0.580   Min.   :-0.0600   Min.   :-0.520  
##  1st Qu.:0.0   1st Qu.:-0.430   1st Qu.:-0.0300   1st Qu.:-0.440  
##  Median :0.1   Median :-0.350   Median :-0.0200   Median :-0.410  
##  Mean   :0.7   Mean   :-0.199   Mean   : 0.0202   Mean   :-0.294  
##  3rd Qu.:0.3   3rd Qu.: 0.060   3rd Qu.: 0.1100   3rd Qu.:-0.160  
##  Max.   :8.1   Max.   : 0.500   Max.   : 0.3100   Max.   : 0.030  
##  NA's   :976                                                      
##   accel_belt_x    accel_belt_y   accel_belt_z  magnet_belt_x  
##  Min.   :-50.0   Min.   : 1.0   Min.   :-189   Min.   :-12.0  
##  1st Qu.:-42.0   1st Qu.:44.0   1st Qu.:-177   1st Qu.:  0.0  
##  Median :-37.0   Median :67.0   Median :-172   Median :  5.5  
##  Mean   :-12.8   Mean   :51.7   Mean   :-140   Mean   : 54.4  
##  3rd Qu.: 43.0   3rd Qu.:70.0   3rd Qu.:-162   3rd Qu.:171.0  
##  Max.   : 71.0   Max.   :90.0   Max.   :  30   Max.   :225.0  
##                                                               
##  magnet_belt_y magnet_belt_z     roll_arm        pitch_arm     
##  Min.   :555   Min.   :-414   Min.   :-180.0   Min.   :-84.20  
##  1st Qu.:579   1st Qu.:-381   1st Qu.:-129.2   1st Qu.: -9.14  
##  Median :584   Median :-366   Median : -30.7   Median : 14.50  
##  Mean   :587   Mean   :-357   Mean   : -20.9   Mean   : 12.06  
##  3rd Qu.:598   3rd Qu.:-336   3rd Qu.:  70.3   3rd Qu.: 32.20  
##  Max.   :620   Max.   :-289   Max.   : 179.0   Max.   : 88.50  
##                                                                
##     yaw_arm        total_accel_arm var_accel_arm    avg_roll_arm   
##  Min.   :-180.00   Min.   : 2.0    Min.   :  0.0   Min.   :-166.6  
##  1st Qu.:-135.00   1st Qu.:19.0    1st Qu.:  0.0   1st Qu.:-125.1  
##  Median :   0.56   Median :28.0    Median : 11.2   Median : -66.0  
##  Mean   : -10.78   Mean   :25.3    Mean   : 31.1   Mean   : -20.3  
##  3rd Qu.: 103.25   3rd Qu.:34.0    3rd Qu.: 58.6   3rd Qu.:  99.6  
##  Max.   : 180.00   Max.   :47.0    Max.   :178.5   Max.   : 160.8  
##                                    NA's   :976     NA's   :976     
##  stddev_roll_arm  var_roll_arm   avg_pitch_arm   stddev_pitch_arm
##  Min.   :  0.2   Min.   :    0   Min.   :-54.7   Min.   : 0.2    
##  1st Qu.:  3.9   1st Qu.:   15   1st Qu.:  0.7   1st Qu.: 5.0    
##  Median :  9.7   Median :   94   Median : 19.4   Median : 7.0    
##  Mean   : 23.0   Mean   : 1852   Mean   : 18.6   Mean   : 7.4    
##  3rd Qu.: 21.7   3rd Qu.:  476   3rd Qu.: 36.0   3rd Qu.: 8.6    
##  Max.   :161.5   Max.   :26067   Max.   : 70.7   Max.   :17.2    
##  NA's   :976     NA's   :976     NA's   :976     NA's   :976     
##  var_pitch_arm    avg_yaw_arm     stddev_yaw_arm   var_yaw_arm   
##  Min.   :  0.0   Min.   :-164.6   Min.   :  0.0   Min.   :    0  
##  1st Qu.: 24.9   1st Qu.:-113.7   1st Qu.:  6.6   1st Qu.:   44  
##  Median : 49.1   Median :  18.2   Median : 17.4   Median :  303  
##  Mean   : 77.7   Mean   :  -6.4   Mean   : 26.0   Mean   : 1730  
##  3rd Qu.: 75.1   3rd Qu.:  99.6   3rd Qu.: 31.7   3rd Qu.: 1008  
##  Max.   :297.4   Max.   : 140.9   Max.   :158.1   Max.   :24989  
##  NA's   :976     NA's   :976      NA's   :976     NA's   :976    
##   gyros_arm_x      gyros_arm_y     gyros_arm_z      accel_arm_x  
##  Min.   :-3.030   Min.   :-1.98   Min.   :-2.330   Min.   :-293  
##  1st Qu.:-0.370   1st Qu.:-0.53   1st Qu.:-0.020   1st Qu.:-276  
##  Median : 0.020   Median :-0.21   Median : 0.080   Median :-123  
##  Mean   :-0.161   Mean   :-0.23   Mean   : 0.231   Mean   : -45  
##  3rd Qu.: 0.310   3rd Qu.: 0.00   3rd Qu.: 0.610   3rd Qu.: 132  
##  Max.   : 2.340   Max.   : 1.00   Max.   : 1.690   Max.   : 434  
##                                                                  
##   accel_arm_y      accel_arm_z       magnet_arm_x     magnet_arm_y
##  Min.   :-122.0   Min.   :-165.00   Min.   :-441.0   Min.   :-57  
##  1st Qu.: -51.0   1st Qu.: -87.00   1st Qu.:-367.0   1st Qu.:227  
##  Median :  -4.0   Median :   4.50   Median :-285.0   Median :322  
##  Mean   :   2.8   Mean   :  -1.14   Mean   : -48.6   Mean   :280  
##  3rd Qu.:  37.0   3rd Qu.:  77.00   3rd Qu.: 210.5   3rd Qu.:343  
##  Max.   : 113.0   Max.   : 189.00   Max.   : 752.0   Max.   :583  
##                                                                   
##   magnet_arm_z  kurtosis_roll_arm kurtosis_picth_arm kurtosis_yaw_arm  
##  Min.   :-204   Min.   :-1.5      Length:1000        Length:1000       
##  1st Qu.: 453   1st Qu.:-1.4      Class :character   Class :character  
##  Median : 514   Median :-1.2      Mode  :character   Mode  :character  
##  Mean   : 449   Mean   :-0.1                                           
##  3rd Qu.: 560   3rd Qu.:-0.9                                           
##  Max.   : 676   Max.   :18.7                                           
##                 NA's   :976                                            
##  skewness_roll_arm skewness_pitch_arm skewness_yaw_arm    max_roll_arm  
##  Min.   :-1.7      Length:1000        Length:1000        Min.   :-32.8  
##  1st Qu.:-0.1      Class :character   Class :character   1st Qu.: 15.6  
##  Median : 0.1      Mode  :character   Mode  :character   Median : 22.5  
##  Mean   : 0.2                                            Mean   : 31.3  
##  3rd Qu.: 0.4                                            3rd Qu.: 47.9  
##  Max.   : 4.4                                            Max.   : 81.4  
##  NA's   :976                                             NA's   :976    
##  max_picth_arm     max_yaw_arm    min_roll_arm   min_pitch_arm   
##  Min.   :-164.0   Min.   : 4.0   Min.   :-77.2   Min.   :-179.0  
##  1st Qu.: -65.2   1st Qu.:27.2   1st Qu.: -5.3   1st Qu.:-152.0  
##  Median :  85.1   Median :30.5   Median : 14.8   Median : -24.1  
##  Mean   :  32.2   Mean   :32.5   Mean   :  8.3   Mean   : -44.8  
##  3rd Qu.: 129.8   3rd Qu.:40.5   3rd Qu.: 21.3   3rd Qu.:  24.5  
##  Max.   : 180.0   Max.   :47.0   Max.   : 63.5   Max.   : 120.0  
##  NA's   :976      NA's   :976    NA's   :976     NA's   :976     
##   min_yaw_arm   amplitude_roll_arm amplitude_pitch_arm amplitude_yaw_arm
##  Min.   : 2.0   Min.   : 0.7       Min.   :  0.0       Min.   : 0.0     
##  1st Qu.: 7.5   1st Qu.:17.7       1st Qu.: 20.0       1st Qu.: 1.0     
##  Median :22.0   Median :22.1       Median : 58.7       Median :10.0     
##  Mean   :19.6   Mean   :23.0       Mean   : 77.0       Mean   :12.9     
##  3rd Qu.:29.0   3rd Qu.:27.8       3rd Qu.:113.0       3rd Qu.:22.5     
##  Max.   :34.0   Max.   :47.3       Max.   :359.0       Max.   :42.0     
##  NA's   :976    NA's   :976        NA's   :976         NA's   :976      
##  roll_dumbbell     pitch_dumbbell     yaw_dumbbell   
##  Min.   :-101.61   Min.   :-102.97   Min.   :-116.3  
##  1st Qu.: -18.77   1st Qu.: -29.68   1st Qu.:-100.2  
##  Median :  11.16   Median :  12.12   Median : 106.9  
##  Mean   :   7.47   Mean   :  -5.58   Mean   :  23.4  
##  3rd Qu.:  36.92   3rd Qu.:  24.74   3rd Qu.: 124.5  
##  Max.   : 123.71   Max.   : 129.82   Max.   : 145.1  
##                                                      
##  kurtosis_roll_dumbbell kurtosis_picth_dumbbell kurtosis_yaw_dumbbell
##  Min.   :-1.3           Min.   :-1.1            Length:1000          
##  1st Qu.:-0.6           1st Qu.:-0.4            Class :character     
##  Median : 0.1           Median :-0.1            Mode  :character     
##  Mean   : 0.5           Mean   : 0.2                                 
##  3rd Qu.: 0.9           3rd Qu.: 0.5                                 
##  Max.   : 3.5           Max.   : 3.0                                 
##  NA's   :976            NA's   :976                                  
##  skewness_roll_dumbbell skewness_pitch_dumbbell skewness_yaw_dumbbell
##  Min.   :-1.0           Min.   :-1.4            Length:1000          
##  1st Qu.:-0.4           1st Qu.:-0.6            Class :character     
##  Median : 0.2           Median :-0.4            Mode  :character     
##  Mean   : 0.1           Mean   :-0.3                                 
##  3rd Qu.: 0.4           3rd Qu.: 0.1                                 
##  Max.   : 1.6           Max.   : 0.7                                 
##  NA's   :976            NA's   :976                                  
##  max_roll_dumbbell max_picth_dumbbell max_yaw_dumbbell min_roll_dumbbell
##  Min.   :-70.1     Min.   :-104.5     Min.   :-1.3     Min.   :-103.0   
##  1st Qu.:-26.6     1st Qu.: -98.8     1st Qu.:-0.6     1st Qu.: -34.7   
##  Median : 27.1     Median : 131.9     Median : 0.2     Median : -15.3   
##  Mean   : 12.0     Mean   :  33.8     Mean   : 0.5     Mean   : -20.5   
##  3rd Qu.: 48.6     3rd Qu.: 138.9     3rd Qu.: 0.9     3rd Qu.:  11.1   
##  Max.   :129.8     Max.   : 145.1     Max.   : 3.5     Max.   :  17.7   
##  NA's   :976       NA's   :976        NA's   :976      NA's   :976      
##  min_pitch_dumbbell min_yaw_dumbbell amplitude_roll_dumbbell
##  Min.   :-116.3     Min.   :-1.3     Min.   :  1.0          
##  1st Qu.:-108.3     1st Qu.:-0.6     1st Qu.:  6.9          
##  Median :  25.9     Median : 0.2     Median : 20.3          
##  Mean   :   3.5     Mean   : 0.5     Mean   : 32.5          
##  3rd Qu.: 104.0     3rd Qu.: 0.9     3rd Qu.: 36.2          
##  Max.   : 120.9     Max.   : 3.5     Max.   :232.8          
##  NA's   :976        NA's   :976      NA's   :976            
##  amplitude_pitch_dumbbell amplitude_yaw_dumbbell total_accel_dumbbell
##  Min.   :  1.0            Min.   :0              Min.   : 1.0        
##  1st Qu.:  8.1            1st Qu.:0              1st Qu.: 9.0        
##  Median : 18.9            Median :0              Median :10.0        
##  Mean   : 30.4            Mean   :0              Mean   :15.5        
##  3rd Qu.: 37.5            3rd Qu.:0              3rd Qu.:18.0        
##  Max.   :185.6            Max.   :0              Max.   :37.0        
##  NA's   :976              NA's   :976                                
##  var_accel_dumbbell avg_roll_dumbbell stddev_roll_dumbbell
##  Min.   :  0.0      Min.   :-35.3     Min.   : 0.2        
##  1st Qu.:  0.2      1st Qu.:-10.6     1st Qu.: 2.6        
##  Median :  0.4      Median :  8.7     Median : 6.9        
##  Mean   : 10.3      Mean   : 10.5     Mean   : 9.8        
##  3rd Qu.:  0.9      3rd Qu.: 35.7     3rd Qu.:13.4        
##  Max.   :230.4      Max.   : 71.7     Max.   :42.3        
##  NA's   :976        NA's   :976       NA's   :976         
##  var_roll_dumbbell avg_pitch_dumbbell stddev_pitch_dumbbell
##  Min.   :   0.0    Min.   :-70.7      Min.   : 0.2         
##  1st Qu.:   6.8    1st Qu.:-30.1      1st Qu.: 1.5         
##  Median :  48.2    Median :  3.4      Median : 4.1         
##  Mean   : 214.8    Mean   : -5.1      Mean   : 7.9         
##  3rd Qu.: 179.1    3rd Qu.: 27.7      3rd Qu.: 8.1         
##  Max.   :1790.0    Max.   : 35.5      Max.   :45.0         
##  NA's   :976       NA's   :976        NA's   :976          
##  var_pitch_dumbbell avg_yaw_dumbbell stddev_yaw_dumbbell var_yaw_dumbbell
##  Min.   :   0.1     Min.   :-109.6   Min.   : 0.3        Min.   :   0.1  
##  1st Qu.:   2.4     1st Qu.:-103.0   1st Qu.: 2.1        1st Qu.:   4.4  
##  Median :  16.5     Median : 106.5   Median : 4.2        Median :  17.9  
##  Mean   : 196.5     Mean   :  21.4   Mean   : 7.5        Mean   : 165.0  
##  3rd Qu.:  65.6     3rd Qu.: 124.3   3rd Qu.: 8.8        3rd Qu.:  77.1  
##  Max.   :2025.7     Max.   : 128.0   Max.   :51.1        Max.   :2614.5  
##  NA's   :976        NA's   :976      NA's   :976         NA's   :976     
##  gyros_dumbbell_x gyros_dumbbell_y gyros_dumbbell_z accel_dumbbell_x
##  Min.   :-0.430   Min.   :-0.430   Min.   :-0.690   Min.   :-237    
##  1st Qu.: 0.107   1st Qu.:-0.110   1st Qu.:-0.260   1st Qu.: -52    
##  Median : 0.190   Median :-0.020   Median :-0.130   Median :  10    
##  Mean   : 0.287   Mean   :-0.024   Mean   :-0.161   Mean   : -39    
##  3rd Qu.: 0.480   3rd Qu.: 0.050   3rd Qu.:-0.020   3rd Qu.:  20    
##  Max.   : 1.140   Max.   : 4.370   Max.   : 0.430   Max.   :  50    
##                                                                     
##  accel_dumbbell_y accel_dumbbell_z magnet_dumbbell_x magnet_dumbbell_y
##  Min.   :-54      Min.   :-273.0   Min.   :-600.0    Min.   :-586     
##  1st Qu.:-15      1st Qu.:-157.0   1st Qu.:-568.0    1st Qu.:-533     
##  Median :  7      Median :  48.5   Median : 475.0    Median :-504     
##  Mean   : 21      Mean   : -42.1   Mean   :   2.5    Mean   :-156     
##  3rd Qu.: 60      3rd Qu.:  85.2   3rd Qu.: 517.0    3rd Qu.: 259     
##  Max.   : 95      Max.   : 110.0   Max.   : 564.0    Max.   : 422     
##                                                                       
##  magnet_dumbbell_z  roll_forearm    pitch_forearm     yaw_forearm     
##  Min.   :-216.0    Min.   :-179.0   Min.   :-64.00   Min.   :-178.00  
##  1st Qu.: -88.2    1st Qu.:   0.0   1st Qu.: -0.39   1st Qu.:-104.25  
##  Median : -59.0    Median :  23.7   Median :  0.00   Median :   0.00  
##  Mean   : -52.4    Mean   :  31.6   Mean   :  3.60   Mean   :   4.08  
##  3rd Qu.:   0.0    3rd Qu.:  89.8   3rd Qu.: 19.32   3rd Qu.: 102.00  
##  Max.   :  60.0    Max.   : 179.0   Max.   : 86.70   Max.   : 177.00  
##                                                                       
##  kurtosis_roll_forearm kurtosis_picth_forearm kurtosis_yaw_forearm
##  Length:1000           Length:1000            Length:1000         
##  Class :character      Class :character       Class :character    
##  Mode  :character      Mode  :character       Mode  :character    
##                                                                   
##                                                                   
##                                                                   
##                                                                   
##  skewness_roll_forearm skewness_pitch_forearm skewness_yaw_forearm
##  Length:1000           Length:1000            Length:1000         
##  Class :character      Class :character       Class :character    
##  Mode  :character      Mode  :character       Mode  :character    
##                                                                   
##                                                                   
##                                                                   
##                                                                   
##  max_roll_forearm max_picth_forearm max_yaw_forearm    min_roll_forearm
##  Min.   :-63.7    Min.   :-151.0    Length:1000        Min.   :-64.0   
##  1st Qu.:  0.0    1st Qu.:   0.0    Class :character   1st Qu.: -0.2   
##  Median :  0.0    Median :  49.7    Mode  :character   Median :  0.0   
##  Mean   : 18.4    Mean   :  47.6                       Mean   : -3.7   
##  3rd Qu.: 73.2    3rd Qu.: 168.0                       3rd Qu.: 12.1   
##  Max.   : 86.9    Max.   : 178.0                       Max.   : 47.5   
##  NA's   :976      NA's   :976                          NA's   :976     
##  min_pitch_forearm min_yaw_forearm    amplitude_roll_forearm
##  Min.   :-176.0    Length:1000        Min.   : 0.0          
##  1st Qu.:-167.8    Class :character   1st Qu.: 0.0          
##  Median :-129.0    Mode  :character   Median : 1.4          
##  Mean   : -69.9                       Mean   :22.1          
##  3rd Qu.:   0.0                       3rd Qu.:44.2          
##  Max.   : 107.0                       Max.   :77.1          
##  NA's   :976                          NA's   :976           
##  amplitude_pitch_forearm amplitude_yaw_forearm total_accel_forearm
##  Min.   :  0             Length:1000           Min.   :11.0       
##  1st Qu.:  0             Class :character      1st Qu.:17.0       
##  Median :  2             Mode  :character      Median :34.0       
##  Mean   :118                                   Mean   :28.4       
##  3rd Qu.:341                                   3rd Qu.:36.0       
##  Max.   :352                                   Max.   :47.0       
##  NA's   :976                                                      
##  var_accel_forearm avg_roll_forearm stddev_roll_forearm var_roll_forearm
##  Min.   : 0.0      Min.   :-91.5    Min.   :  0.0       Min.   :    0   
##  1st Qu.: 0.3      1st Qu.:  0.0    1st Qu.:  0.0       1st Qu.:    0   
##  Median : 3.6      Median : 18.5    Median :  0.9       Median :    1   
##  Mean   : 9.1      Mean   : 26.1    Mean   : 31.9       Mean   : 3276   
##  3rd Qu.:14.3      3rd Qu.: 64.4    3rd Qu.: 82.3       3rd Qu.: 6773   
##  Max.   :58.5      Max.   :101.8    Max.   :128.1       Max.   :16420   
##  NA's   :976       NA's   :976      NA's   :976         NA's   :976     
##  avg_pitch_forearm stddev_pitch_forearm var_pitch_forearm avg_yaw_forearm 
##  Min.   :-63.9     Min.   : 0.0         Min.   :  0.0     Min.   :-151.4  
##  1st Qu.: -0.1     1st Qu.: 0.0         1st Qu.:  0.0     1st Qu.: -44.9  
##  Median :  0.0     Median : 0.4         Median :  0.2     Median :   0.0  
##  Mean   :  6.9     Mean   : 7.4         Mean   :140.9     Mean   :  -4.5  
##  3rd Qu.: 36.4     3rd Qu.:14.7         3rd Qu.:218.1     3rd Qu.:  56.8  
##  Max.   : 68.2     Max.   :26.7         Max.   :714.5     Max.   : 108.1  
##  NA's   :976       NA's   :976          NA's   :976       NA's   :976     
##  stddev_yaw_forearm var_yaw_forearm gyros_forearm_x  gyros_forearm_y 
##  Min.   :  0.0      Min.   :    0   Min.   :-4.950   Min.   :-5.560  
##  1st Qu.:  0.0      1st Qu.:    0   1st Qu.: 0.000   1st Qu.:-0.100  
##  Median :  0.7      Median :    0   Median : 0.140   Median : 0.020  
##  Mean   : 31.9      Mean   : 3053   Mean   : 0.315   Mean   : 0.061  
##  3rd Qu.: 80.1      3rd Qu.: 6411   3rd Qu.: 0.560   3rd Qu.: 0.740  
##  Max.   :119.7      Max.   :14316   Max.   : 3.970   Max.   : 3.070  
##  NA's   :976        NA's   :976                                      
##  gyros_forearm_z  accel_forearm_x  accel_forearm_y accel_forearm_z 
##  Min.   :-8.090   Min.   :-280.0   Min.   :-190    Min.   :-366.0  
##  1st Qu.:-0.080   1st Qu.:  18.0   1st Qu.:  99    1st Qu.:-197.0  
##  Median : 0.000   Median :  79.0   Median : 204    Median :-170.0  
##  Mean   : 0.063   Mean   :  62.7   Mean   : 178    Mean   :-125.1  
##  3rd Qu.: 0.302   3rd Qu.: 152.0   3rd Qu.: 238    3rd Qu.:  24.2  
##  Max.   : 4.310   Max.   : 227.0   Max.   : 413    Max.   :  71.0  
##                                                                    
##  magnet_forearm_x magnet_forearm_y magnet_forearm_z    classe         
##  Min.   :-716.0   Min.   :-419     Min.   :-593.0   Length:1000       
##  1st Qu.:-324.8   1st Qu.:-144     1st Qu.:  87.8   Class :character  
##  Median :-147.0   Median : 655     Median : 474.0   Mode  :character  
##  Mean   : -39.3   Mean   : 391     Mean   : 393.9                     
##  3rd Qu.: 134.0   3rd Qu.: 749     3rd Qu.: 634.0                     
##  Max.   : 663.0   Max.   : 911     Max.   : 792.0                     
## 
```

```r
str(raw)
```

```
## 'data.frame':	1000 obs. of  160 variables:
##  $ X                       : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ user_name               : chr  "carlitos" "carlitos" "carlitos" "carlitos" ...
##  $ raw_timestamp_part_1    : int  1323084231 1323084231 1323084231 1323084232 1323084232 1323084232 1323084232 1323084232 1323084232 1323084232 ...
##  $ raw_timestamp_part_2    : int  788290 808298 820366 120339 196328 304277 368296 440390 484323 484434 ...
##  $ cvtd_timestamp          : chr  "05/12/2011 11:23" "05/12/2011 11:23" "05/12/2011 11:23" "05/12/2011 11:23" ...
##  $ new_window              : chr  "no" "no" "no" "no" ...
##  $ num_window              : int  11 11 11 12 12 12 12 12 12 12 ...
##  $ roll_belt               : num  1.41 1.41 1.42 1.48 1.48 1.45 1.42 1.42 1.43 1.45 ...
##  $ pitch_belt              : num  8.07 8.07 8.07 8.05 8.07 8.06 8.09 8.13 8.16 8.17 ...
##  $ yaw_belt                : num  -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 ...
##  $ total_accel_belt        : int  3 3 3 3 3 3 3 3 3 3 ...
##  $ kurtosis_roll_belt      : chr  "" "" "" "" ...
##  $ kurtosis_picth_belt     : chr  "" "" "" "" ...
##  $ kurtosis_yaw_belt       : chr  "" "" "" "" ...
##  $ skewness_roll_belt      : chr  "" "" "" "" ...
##  $ skewness_roll_belt.1    : chr  "" "" "" "" ...
##  $ skewness_yaw_belt       : chr  "" "" "" "" ...
##  $ max_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_belt          : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_belt            : chr  "" "" "" "" ...
##  $ min_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_belt          : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_belt            : chr  "" "" "" "" ...
##  $ amplitude_roll_belt     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_pitch_belt    : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_yaw_belt      : chr  "" "" "" "" ...
##  $ var_total_accel_belt    : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_roll_belt        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_pitch_belt          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_pitch_belt       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_pitch_belt          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_yaw_belt            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_yaw_belt         : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_yaw_belt            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ gyros_belt_x            : num  0 0.02 0 0.02 0.02 0.02 0.02 0.02 0.02 0.03 ...
##  $ gyros_belt_y            : num  0 0 0 0 0.02 0 0 0 0 0 ...
##  $ gyros_belt_z            : num  -0.02 -0.02 -0.02 -0.03 -0.02 -0.02 -0.02 -0.02 -0.02 0 ...
##  $ accel_belt_x            : int  -21 -22 -20 -22 -21 -21 -22 -22 -20 -21 ...
##  $ accel_belt_y            : int  4 4 5 3 2 4 3 4 2 4 ...
##  $ accel_belt_z            : int  22 22 23 21 24 21 21 21 24 22 ...
##  $ magnet_belt_x           : int  -3 -7 -2 -6 -6 0 -4 -2 1 -3 ...
##  $ magnet_belt_y           : int  599 608 600 604 600 603 599 603 602 609 ...
##  $ magnet_belt_z           : int  -313 -311 -305 -310 -302 -312 -311 -313 -312 -308 ...
##  $ roll_arm                : num  -128 -128 -128 -128 -128 -128 -128 -128 -128 -128 ...
##  $ pitch_arm               : num  22.5 22.5 22.5 22.1 22.1 22 21.9 21.8 21.7 21.6 ...
##  $ yaw_arm                 : num  -161 -161 -161 -161 -161 -161 -161 -161 -161 -161 ...
##  $ total_accel_arm         : int  34 34 34 34 34 34 34 34 34 34 ...
##  $ var_accel_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_roll_arm         : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_pitch_arm        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_yaw_arm             : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_yaw_arm          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_yaw_arm             : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ gyros_arm_x             : num  0 0.02 0.02 0.02 0 0.02 0 0.02 0.02 0.02 ...
##  $ gyros_arm_y             : num  0 -0.02 -0.02 -0.03 -0.03 -0.03 -0.03 -0.02 -0.03 -0.03 ...
##  $ gyros_arm_z             : num  -0.02 -0.02 -0.02 0.02 0 0 0 0 -0.02 -0.02 ...
##  $ accel_arm_x             : int  -288 -290 -289 -289 -289 -289 -289 -289 -288 -288 ...
##  $ accel_arm_y             : int  109 110 110 111 111 111 111 111 109 110 ...
##  $ accel_arm_z             : int  -123 -125 -126 -123 -123 -122 -125 -124 -122 -124 ...
##  $ magnet_arm_x            : int  -368 -369 -368 -372 -374 -369 -373 -372 -369 -376 ...
##  $ magnet_arm_y            : int  337 337 344 344 337 342 336 338 341 334 ...
##  $ magnet_arm_z            : int  516 513 513 512 506 513 509 510 518 516 ...
##  $ kurtosis_roll_arm       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ kurtosis_picth_arm      : chr  "" "" "" "" ...
##  $ kurtosis_yaw_arm        : chr  "" "" "" "" ...
##  $ skewness_roll_arm       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ skewness_pitch_arm      : chr  "" "" "" "" ...
##  $ skewness_yaw_arm        : chr  "" "" "" "" ...
##  $ max_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_arm             : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_arm             : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_roll_arm      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_pitch_arm     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_yaw_arm       : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ roll_dumbbell           : num  13.1 13.1 12.9 13.4 13.4 ...
##  $ pitch_dumbbell          : num  -70.5 -70.6 -70.3 -70.4 -70.4 ...
##  $ yaw_dumbbell            : num  -84.9 -84.7 -85.1 -84.9 -84.9 ...
##  $ kurtosis_roll_dumbbell  : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ kurtosis_picth_dumbbell : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ kurtosis_yaw_dumbbell   : chr  "" "" "" "" ...
##  $ skewness_roll_dumbbell  : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ skewness_pitch_dumbbell : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ skewness_yaw_dumbbell   : chr  "" "" "" "" ...
##  $ max_roll_dumbbell       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_dumbbell      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_dumbbell        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_roll_dumbbell       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_dumbbell      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_dumbbell        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_roll_dumbbell : num  NA NA NA NA NA NA NA NA NA NA ...
##   [list output truncated]
```


#### Check for missing values

```r
sum(is.na(raw))
```

```
## [1] 74176
```

```r
nas <- format(sum(is.na(raw)), big.mark = ",", scientific = FALSE)
records <- format(nrow(raw), big.mark = ",", scientific = FALSE)
paste("Of", records, "records, there are", nas, "incomplete records!")
```

```
## [1] "Of 1,000 records, there are 74,176 incomplete records!"
```

