# 变量筛选
## 先单后多
### 分类变量处理

```r
data <- read.csv('myopia.csv')
data$MYOPIC <- factor(data$MYOPIC,levels = c(0,1),labels = c("非近视","近视"))
data$GENDER <- factor(data$GENDER,levels = c(0,1),labels = c("女性","男性"))
data$MOMMY <- factor(data$MOMMY,levels = c(0,1),labels = c("母亲不近视","母亲近视"))
data$DADMY <- factor(data$DADMY,levels = c(0,1),labels = c("父亲不近视","父亲近视"))
#年龄不设标签
# str(data)
# summary(data)
```

### 制作table1

```r
#连续性自变量
x1 <- c("SPHEQ","AL","ACD","LT","VCD","SPORTHR","READHR","COMPHR","STUDYHR","TVHR","DIOPTERHR")
#分类变量
x2 <- c("MYOPIC","AGE","GENDER","MOMMY","DADMY")
```


```r
library(tableone)
```


```r
table1 <- CreateTableOne(vars = c(x1,x2),
                       data = data,
                       factorVars = x2,
                       strata=c("MYOPIC"),addOverall = F)
results1 <- print(table1,shouwAllLevels=FALSE)
```

```
##                        Stratified by MYOPIC
##                         非近视        近视           p      test
##   n                       537            81                     
##   SPHEQ (mean (SD))      0.89 (0.60)   0.20 (0.40)   <0.001     
##   AL (mean (SD))        22.49 (0.69)  22.56 (0.61)    0.349     
##   ACD (mean (SD))        3.57 (0.23)   3.64 (0.20)    0.007     
##   LT (mean (SD))         3.54 (0.16)   3.52 (0.14)    0.257     
##   VCD (mean (SD))       15.37 (0.67)  15.40 (0.61)    0.769     
##   SPORTHR (mean (SD))   12.26 (7.93)   9.94 (8.00)    0.015     
##   READHR (mean (SD))     2.71 (2.97)   3.37 (3.62)    0.071     
##   COMPHR (mean (SD))     2.07 (2.99)   2.31 (3.50)    0.521     
##   STUDYHR (mean (SD))    1.52 (2.29)   1.31 (1.68)    0.429     
##   TVHR (mean (SD))       8.96 (5.79)   8.89 (5.26)    0.920     
##   DIOPTERHR (mean (SD)) 25.79 (15.92) 27.54 (16.75)   0.359     
##   MYOPIC = 近视 (%)         0 ( 0.0)     81 (100.0)  <0.001     
##   AGE (%)                                             0.497     
##      5                     17 ( 3.2)      4 (  4.9)             
##      6                    398 (74.1)     58 ( 71.6)             
##      7                     73 (13.6)      9 ( 11.1)             
##      8                     45 ( 8.4)      8 (  9.9)             
##      9                      4 ( 0.7)      2 (  2.5)             
##   GENDER = 男性 (%)       256 (47.7)     46 ( 56.8)   0.158     
##   MOMMY = 母亲近视 (%)    258 (48.0)     55 ( 67.9)   0.001     
##   DADMY = 父亲近视 (%)    252 (46.9)     56 ( 69.1)  <0.001
```

```r
write.csv(results1,"table1.csv")
```



```r
#如果不服从正态分布秩和检验、卡方检验
table1 <- CreateTableOne(vars = c(x1,x2),
                       data = data,
                       factorVars = x2,
                       strata = "MYOPIC",addOverall = F)
results2 <- print(table1,shouwAllLevels=FALSE,
                  nonnormal=x1)#指定非阐述检验的变量
```

```
##                           Stratified by MYOPIC
##                            非近视               近视                 p     
##   n                          537                   81                      
##   SPHEQ (median [IQR])      0.79 [0.55, 1.10]    0.23 [-0.07, 0.50]  <0.001
##   AL (median [IQR])        22.45 [22.02, 22.97] 22.56 [22.07, 22.94]  0.360
##   ACD (median [IQR])        3.57 [3.41, 3.72]    3.68 [3.50, 3.74]    0.003
##   LT (median [IQR])         3.54 [3.44, 3.65]    3.51 [3.42, 3.63]    0.206
##   VCD (median [IQR])       15.36 [14.92, 15.83] 15.33 [14.96, 15.89]  0.741
##   SPORTHR (median [IQR])   10.00 [6.00, 16.00]   8.00 [3.00, 15.00]   0.005
##   READHR (median [IQR])     2.00 [0.00, 4.00]    3.00 [1.00, 5.00]    0.102
##   COMPHR (median [IQR])     1.00 [0.00, 3.00]    1.00 [0.00, 3.00]    0.892
##   STUDYHR (median [IQR])    1.00 [0.00, 2.00]    1.00 [0.00, 2.00]    0.967
##   TVHR (median [IQR])       8.00 [4.00, 12.00]   8.00 [5.00, 12.00]   0.820
##   DIOPTERHR (median [IQR]) 22.00 [14.00, 34.00] 24.00 [16.00, 36.00]  0.317
##   MYOPIC = 近视 (%)            0 ( 0.0)            81 (100.0)        <0.001
##   AGE (%)                                                             0.497
##      5                        17 ( 3.2)             4 (  4.9)              
##      6                       398 (74.1)            58 ( 71.6)              
##      7                        73 (13.6)             9 ( 11.1)              
##      8                        45 ( 8.4)             8 (  9.9)              
##      9                         4 ( 0.7)             2 (  2.5)              
##   GENDER = 男性 (%)          256 (47.7)            46 ( 56.8)         0.158
##   MOMMY = 母亲近视 (%)       258 (48.0)            55 ( 67.9)         0.001
##   DADMY = 父亲近视 (%)       252 (46.9)            56 ( 69.1)        <0.001
##                           Stratified by MYOPIC
##                            test   
##   n                               
##   SPHEQ (median [IQR])     nonnorm
##   AL (median [IQR])        nonnorm
##   ACD (median [IQR])       nonnorm
##   LT (median [IQR])        nonnorm
##   VCD (median [IQR])       nonnorm
##   SPORTHR (median [IQR])   nonnorm
##   READHR (median [IQR])    nonnorm
##   COMPHR (median [IQR])    nonnorm
##   STUDYHR (median [IQR])   nonnorm
##   TVHR (median [IQR])      nonnorm
##   DIOPTERHR (median [IQR]) nonnorm
##   MYOPIC = 近视 (%)               
##   AGE (%)                         
##      5                            
##      6                            
##      7                            
##      8                            
##      9                            
##   GENDER = 男性 (%)               
##   MOMMY = 母亲近视 (%)            
##   DADMY = 父亲近视 (%)
```

```r
#exact 可以指定确切概论检验的变量，这里忽略（数据大，不需要）
write.csv(results2,"results2.csv")
```

### 单因素logistic

```r
# 自变量
model <- glm(MYOPIC ~ SPHEQ,data = data,family = binomial())
# 查看模型结果
summary(model)$coefficients
```

```
##                Estimate Std. Error    z value     Pr(>|z|)
## (Intercept)  0.05397315  0.2067483  0.2610573 7.940483e-01
## SPHEQ       -3.83309762  0.4183696 -9.1619878 5.094998e-20
```

```r
# 计算OR及其可信区间
exp(cbind("OR"=coef(model),confint(model)))
```

```
## Waiting for profiling to be done...
```

```
##                     OR      2.5 %     97.5 %
## (Intercept) 1.05545626 0.70776986 1.59472261
## SPHEQ       0.02164247 0.00910867 0.04716271
```
取单因素P<0.1

### 多因素模型

```r
# 多因素模型
model_1<- glm(factor(MYOPIC)~MOMMY+DADMY+SPHEQ+ACD+SPORTHR+READHR,family = "binomial",data = data)
# 查看结果
summary(model_1)$coefficients
```

```
##                  Estimate Std. Error   z value     Pr(>|z|)
## (Intercept)   -3.53288934 2.42831871 -1.454871 1.457051e-01
## MOMMY母亲近视  0.74791347 0.31126257  2.402838 1.626840e-02
## DADMY父亲近视  0.86552437 0.30735580  2.816034 4.862053e-03
## SPHEQ         -3.79733834 0.43556193 -8.718251 2.825317e-18
## ACD            0.82711271 0.66485466  1.244050 2.134810e-01
## SPORTHR       -0.05461693 0.02041790 -2.674953 7.473971e-03
## READHR         0.06843900 0.04577874  1.494995 1.349157e-01
```

```r
# 计算OR及其可信区间
exp(cbind("OR"=coef(model),confint(model_1)))
```

```
## Waiting for profiling to be done...
```

```
## Warning in cbind(OR = coef(model), confint(model_1)): number of rows of result
## is not a multiple of vector length (arg 1)
```

```
##                       OR        2.5 %     97.5 %
## (Intercept)   1.05545626 0.0002380046 3.33425391
## MOMMY母亲近视 0.02164247 1.1604118328 3.95048116
## DADMY父亲近视 1.05545626 1.3157000566 4.41021361
## SPHEQ         0.02164247 0.0090945183 0.05041805
## ACD           1.05545626 0.6237053229 8.51268747
## SPORTHR       0.02164247 0.9077904406 0.98385439
## READHR        1.05545626 0.9784535086 1.17140573
```

#### PS: 引入变量
如果我们已经确定是研究自变量X 与 因变量Y之间的关系，这时候协变量应该如何筛选进入多因素模型呢？
如果某个协变量Z与因变量Y之间单因素分析的P值小于0.1，并且协变量Z与自变量X同时分析与因变量Y的关系，由于Z的存在，使X的系数较单因素分析时变化超过10%，这时候协变量Z应该纳入多因素分析中。

我们这里主要（关心）的X 变量时SPHEQ情况，其他自变量都是协变量Z

```r
uni_methods<-function(xvar){
  model<-glm(MYOPIC~SPHEQ,data = data,family = binomial())
  coef <- coef(model)[2]
  form <- as.formula(paste0("MYOPIC~SPHEQ+",xvar))
  model2 <- glm(form,data = data,family = binomial())
  coef2 <- coef(model2)[2]
  ratio <- abs(coef2-coef)/coef>0.1
  if(ratio){
    return(xvar)
  }
}
```


```r
xvar <- c(x1,x2)
xvar <- xvar[-which(xvar=="SPHEQ")]
xvar
```

```
##  [1] "AL"        "ACD"       "LT"        "VCD"       "SPORTHR"   "READHR"   
##  [7] "COMPHR"    "STUDYHR"   "TVHR"      "DIOPTERHR" "MYOPIC"    "AGE"      
## [13] "GENDER"    "MOMMY"     "DADMY"
```


```r
lapply(xvar,uni_methods)
```

```
## Warning in model.matrix.default(mt, mf, contrasts): 在公式右手的反应略过不用
```

```
## Warning in model.matrix.default(mt, mf, contrasts): 模型矩阵的2项有问题: 没有指
## 定的列
```

```
## [[1]]
## NULL
## 
## [[2]]
## NULL
## 
## [[3]]
## NULL
## 
## [[4]]
## NULL
## 
## [[5]]
## NULL
## 
## [[6]]
## NULL
## 
## [[7]]
## NULL
## 
## [[8]]
## NULL
## 
## [[9]]
## NULL
## 
## [[10]]
## NULL
## 
## [[11]]
## NULL
## 
## [[12]]
## NULL
## 
## [[13]]
## NULL
## 
## [[14]]
## NULL
## 
## [[15]]
## NULL
```
