# 随机森林-变量重要性评分

## 导入数据

```r
library(tidyverse)
data <- read.csv('myopia.csv')
data <- data %>% 
  select(-1,-2) %>% 
  na.omit(data)
```

将分类变量处理成factor
分类变量处理或不处理成factor，不影响结果
多分类变量必须处理成factor


```r
data$MYOPIC <- factor(data$MYOPIC,levels = c(0,1),labels = c("非近视","近视"))
data$GENDER <- factor(data$GENDER,levels = c(0,1),labels = c("女性","男性"))
data$MOMMY <- factor(data$MOMMY,levels = c(0,1),labels = c("母亲不近视","母亲近视"))
data$DADMY <- factor(data$DADMY,levels = c(0,1),labels = c("父亲不近视","父亲近视"))
```


```r
# 查看数据
view(data)
# 查看数据集变量名称
names(data)
```

```
##  [1] "MYOPIC"    "AGE"       "GENDER"    "SPHEQ"     "AL"        "ACD"      
##  [7] "LT"        "VCD"       "SPORTHR"   "READHR"    "COMPHR"    "STUDYHR"  
## [13] "TVHR"      "DIOPTERHR" "MOMMY"     "DADMY"
```

```r
# 查看变量类型
str(data)
```

```
## 'data.frame':	618 obs. of  16 variables:
##  $ MYOPIC   : Factor w/ 2 levels "非近视","近视": 2 1 1 2 1 1 1 1 1 1 ...
##  $ AGE      : int  6 6 6 6 5 6 6 6 7 6 ...
##  $ GENDER   : Factor w/ 2 levels "女性","男性": 2 2 2 2 1 1 2 2 1 2 ...
##  $ SPHEQ    : num  -0.052 0.608 1.179 0.525 0.697 ...
##  $ AL       : num  21.9 22.4 22.5 22.2 23.3 ...
##  $ ACD      : num  3.69 3.7 3.46 3.86 3.68 ...
##  $ LT       : num  3.5 3.39 3.51 3.61 3.45 ...
##  $ VCD      : num  14.7 15.3 15.5 14.7 16.2 ...
##  $ SPORTHR  : int  45 4 14 18 14 10 12 12 4 30 ...
##  $ READHR   : int  8 0 0 11 0 6 7 0 0 5 ...
##  $ COMPHR   : int  0 1 2 0 0 2 2 0 3 1 ...
##  $ STUDYHR  : int  0 1 0 0 0 1 1 0 1 0 ...
##  $ TVHR     : int  10 7 10 4 4 19 8 8 3 10 ...
##  $ DIOPTERHR: int  34 12 14 37 4 44 36 8 12 27 ...
##  $ MOMMY    : Factor w/ 2 levels "母亲不近视","母亲近视": 2 2 1 1 2 1 1 1 1 1 ...
##  $ DADMY    : Factor w/ 2 levels "父亲不近视","父亲近视": 2 2 1 2 1 2 2 1 1 1 ...
```

```r
#查看数据data中各变量基本统计信息
summary(data)
```

```
##     MYOPIC         AGE         GENDER        SPHEQ               AL       
##  非近视:537   Min.   :5.000   女性:316   Min.   :-0.6990   Min.   :19.90  
##  近视  : 81   1st Qu.:6.000   男性:302   1st Qu.: 0.4562   1st Qu.:22.04  
##               Median :6.000              Median : 0.7290   Median :22.46  
##               Mean   :6.299              Mean   : 0.8010   Mean   :22.50  
##               3rd Qu.:6.000              3rd Qu.: 1.0340   3rd Qu.:22.97  
##               Max.   :9.000              Max.   : 4.3720   Max.   :24.56  
##       ACD              LT             VCD           SPORTHR     
##  Min.   :2.772   Min.   :2.960   Min.   :13.38   Min.   : 0.00  
##  1st Qu.:3.424   1st Qu.:3.436   1st Qu.:14.93   1st Qu.: 6.00  
##  Median :3.585   Median :3.542   Median :15.36   Median :10.00  
##  Mean   :3.579   Mean   :3.541   Mean   :15.38   Mean   :11.95  
##  3rd Qu.:3.730   3rd Qu.:3.640   3rd Qu.:15.84   3rd Qu.:16.00  
##  Max.   :4.250   Max.   :4.112   Max.   :17.30   Max.   :45.00  
##      READHR           COMPHR          STUDYHR           TVHR       
##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.00   Min.   : 0.000  
##  1st Qu.: 0.000   1st Qu.: 0.000   1st Qu.: 0.00   1st Qu.: 4.250  
##  Median : 2.000   Median : 1.000   Median : 1.00   Median : 8.000  
##  Mean   : 2.796   Mean   : 2.105   Mean   : 1.49   Mean   : 8.948  
##  3rd Qu.: 4.000   3rd Qu.: 3.000   3rd Qu.: 2.00   3rd Qu.:12.000  
##  Max.   :20.000   Max.   :30.000   Max.   :15.00   Max.   :31.000  
##    DIOPTERHR             MOMMY            DADMY    
##  Min.   :  2.00   母亲不近视:305   父亲不近视:310  
##  1st Qu.: 15.00   母亲近视  :313   父亲近视  :308  
##  Median : 23.00                                    
##  Mean   : 26.02                                    
##  3rd Qu.: 34.00                                    
##  Max.   :101.00
```

为什么要进行随机森林

为了显著提高模型的预测能力，我们可以生成多个树模型，然后将这些模型的结果组合起来。

随机森林涉及两个方法，一个是装袋，另外一个是变量随机。

在装袋法中，使用数据集的约2/3数据建立树模型，剩下的1/3称为袋外数据，这个过程重复N次，最后平均结果，其中每个树都任其生长，不进行任何基于测量误差的剪枝，这意味着每个树模型的方差都很大，但是对多个树模型的平均化，可以降低方差，同时又不增加偏差。

除了袋装时对样本进行随机选择，我们对自变量也是随机选择。对于分类问题，每次抽取的自变量数是自变量总数目的评分根，对于回归问题，每次抽取的自变量数目是自变量总数目1/3.

随机森林在这里面可以对变量的重要性进行评分及排序，对于分类问题并不能实现变量的筛选！！

## 拟合随机森林模型

```r
# install.packages("randomForest")
library(randomForest)
```

```
## Warning: 程辑包'randomForest'是用R版本4.1.3 来建造的
```

```
## randomForest 4.7-1.1
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```
## 
## 载入程辑包：'randomForest'
```

```
## The following object is masked from 'package:dplyr':
## 
##     combine
```

```
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

```r
set.seed(123)#如果不设置随机数，每次运行的结果都是不同的。
model <- randomForest(as.factor(MYOPIC)~.,data = data)#注意要把因变量因子化，否则会按连续型变量处理
model
```

```
## 
## Call:
##  randomForest(formula = as.factor(MYOPIC) ~ ., data = data) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 3
## 
##         OOB estimate of  error rate: 11.49%
## Confusion matrix:
##        非近视 近视 class.error
## 非近视    527   10  0.01862197
## 近视       61   20  0.75308642
```
生成了500棵不同的树，每次树分裂随机抽取3个变量
直接使用随机森林，袋外数据的预测误差，22.78%

## 画出误差和树数量的关系图

```r
plot(model)
```

<img src="06-randomforest_files/figure-html/unnamed-chunk-5-1.png" width="672" />
3条曲线

绿色表示近视组的误差

红色表示非近视组的误差

黑色表示总样本

## 找出总样本最小误差所对应的树的数量

```r
which.min(model$err.rate[,1])
```

```
## [1] 33
```


```r
#ntree参数表示生成多少棵树
#mtry参数，设置每次迭代时，随机选择的变量数量，分类问题通常时p的平方根。其中p是所有变量数量。
model2 <- randomForest(as.factor(MYOPIC)~.,ntree=47,data = data,mtry=4)
print(model2)
```

```
## 
## Call:
##  randomForest(formula = as.factor(MYOPIC) ~ ., data = data, ntree = 47,      mtry = 4) 
##                Type of random forest: classification
##                      Number of trees: 47
## No. of variables tried at each split: 4
## 
##         OOB estimate of  error rate: 11.65%
## Confusion matrix:
##        非近视 近视 class.error
## 非近视    521   16  0.02979516
## 近视       56   25  0.69135802
```
数据解读

## 变量重要性评分

```r
importance(model2)
```

```
##           MeanDecreaseGini
## AGE               2.970876
## GENDER            1.810331
## SPHEQ            44.855571
## AL                8.889826
## ACD              11.452451
## LT                8.375983
## VCD              10.905568
## SPORTHR          12.494139
## READHR            5.606006
## COMPHR            5.388757
## STUDYHR           4.169950
## TVHR              8.267268
## DIOPTERHR         9.299963
## MOMMY             2.082511
## DADMY             3.167217
```
## 可视化

```r
varImpPlot(model2)
```

<img src="06-randomforest_files/figure-html/unnamed-chunk-9-1.png" width="672" />

