# 逐步回归

## 数据

```r
library(tidyverse)
```

```
## -- Attaching packages --------------------------------------- tidyverse 1.3.1 --
```

```
## v ggplot2 3.3.6     v purrr   0.3.4
## v tibble  3.1.7     v dplyr   1.0.7
## v tidyr   1.1.4     v stringr 1.4.0
## v readr   2.1.2     v forcats 0.5.1
```

```
## -- Conflicts ------------------------------------------ tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```


```r
data <- read.csv('myopia.csv')
data <- data %>% 
  select(-1,-2) %>% 
  na.omit(data)
```

将分类变量处理成factor

二分类变量处理或不处理成factor，不影响结果

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

## 逐步回归

逐步回归：基本思想是将变量一个一个引入或删除，引入的条件是其偏回归平方和经检验是显著的，同时每引入一个新变量，对已入选回归模型的旧变量逐个进行检验，将不显著的变量剔除，以保证回归模型中的自变量每一个都显著，此过程经过若干步直到不引入新变量为止。


逐步回归包含三种，分别是向前、向后法，向前向后法。


```r
# 将所有自变量均纳入模型中
model <- glm(MYOPIC~.,data = data,family = binomial())
# 查看模型结果
summary(model)$coefficients
```

```
##                   Estimate  Std. Error    z value     Pr(>|z|)
## (Intercept)     4.44120968  8.24375350  0.5387364 5.900688e-01
## AGE            -0.03881442  0.22541323 -0.1721923 8.632864e-01
## GENDER男性      0.61717965  0.34236629  1.8026881 7.143721e-02
## SPHEQ          -4.10532498  0.46638861 -8.8023696 1.339571e-18
## AL            -30.48167492 39.07930854 -0.7799952 4.353937e-01
## ACD            31.62630058 39.13026518  0.8082312 4.189575e-01
## LT             29.62122512 39.16352324  0.7563473 4.494410e-01
## VCD            30.10871116 39.10057254  0.7700325 4.412806e-01
## SPORTHR        -0.05114162  0.02121883 -2.4101999 1.594378e-02
## READHR          0.07954877  0.04939061  1.6106051 1.072658e-01
## COMPHR          0.04247663  0.04579193  0.9276009 3.536146e-01
## STUDYHR        -0.17344836  0.09730372 -1.7825461 7.466023e-02
## TVHR           -0.01553866  0.02865065 -0.5423493 5.875779e-01
## MOMMY母亲近视   0.71466823  0.32101882  2.2262503 2.599742e-02
## DADMY父亲近视   0.77707257  0.32040311  2.4252966 1.529589e-02
```

```r
# 计算OR及其可信区间
exp(cbind("OR"=coef(model),confint(model)))
```

```
## Waiting for profiling to be done...
```

```
##                         OR        2.5 %       97.5 %
## (Intercept)   8.487755e+01 8.359489e-06 1.013864e+09
## AGE           9.619292e-01 6.173921e-01 1.500563e+00
## GENDER男性    1.853693e+00 9.523985e-01 3.662207e+00
## SPHEQ         1.648466e-02 6.254623e-03 3.916984e-02
## AL            5.780651e-14 1.589338e-47 9.207650e+19
## ACD           5.434103e+13 3.124619e-20 2.207870e+47
## LT            7.317026e+12 3.860676e-21 3.105307e+46
## VCD           1.191371e+13 7.171267e-21 4.515572e+46
## SPORTHR       9.501441e-01 9.098254e-01 9.891419e-01
## READHR        1.082798e+00 9.822655e-01 1.192778e+00
## COMPHR        1.043392e+00 9.484491e-01 1.136872e+00
## STUDYHR       8.407606e-01 6.826446e-01 9.979320e-01
## TVHR          9.845814e-01 9.291900e-01 1.040117e+00
## DIOPTERHR               NA           NA           NA
## MOMMY母亲近视 2.043509e+00 1.100632e+00 3.894270e+00
## DADMY父亲近视 2.175095e+00 1.172768e+00 4.139950e+00
```


```r
# 使用stargzaer 参考 https://zhuanlan.zhihu.com/p/24511106
# install.packages('stargazer')
library(stargazer)
```

```
## Warning: 程辑包'stargazer'是用R版本4.1.2 来建造的
```

```
## 
## Please cite as:
```

```
##  Hlavac, Marek (2022). stargazer: Well-Formatted Regression and Summary Statistics Tables.
```

```
##  R package version 5.2.3. https://CRAN.R-project.org/package=stargazer
```

```r
stargazer(model)
```


% Table created by stargazer v.5.2.3 by Marek Hlavac, Social Policy Institute. E-mail: marek.hlavac at gmail.com
% Date and time: 周一, 7月 25, 2022 - 21:27:09
\begin{table}[!htbp] \centering 
  \caption{} 
  \label{} 
\begin{tabular}{@{\extracolsep{5pt}}lc} 
\\[-1.8ex]\hline 
\hline \\[-1.8ex] 
 & \multicolumn{1}{c}{\textit{Dependent variable:}} \\ 
\cline{2-2} 
\\[-1.8ex] & MYOPIC \\ 
\hline \\[-1.8ex] 
 AGE & $-$0.039 \\ 
  & (0.225) \\ 
  & \\ 
 GENDER男性 & 0.617$^{*}$ \\ 
  & (0.342) \\ 
  & \\ 
 SPHEQ & $-$4.105$^{***}$ \\ 
  & (0.466) \\ 
  & \\ 
 AL & $-$30.482 \\ 
  & (39.079) \\ 
  & \\ 
 ACD & 31.626 \\ 
  & (39.130) \\ 
  & \\ 
 LT & 29.621 \\ 
  & (39.164) \\ 
  & \\ 
 VCD & 30.109 \\ 
  & (39.101) \\ 
  & \\ 
 SPORTHR & $-$0.051$^{**}$ \\ 
  & (0.021) \\ 
  & \\ 
 READHR & 0.080 \\ 
  & (0.049) \\ 
  & \\ 
 COMPHR & 0.042 \\ 
  & (0.046) \\ 
  & \\ 
 STUDYHR & $-$0.173$^{*}$ \\ 
  & (0.097) \\ 
  & \\ 
 TVHR & $-$0.016 \\ 
  & (0.029) \\ 
  & \\ 
 DIOPTERHR &  \\ 
  &  \\ 
  & \\ 
 MOMMY母亲近视 & 0.715$^{**}$ \\ 
  & (0.321) \\ 
  & \\ 
 DADMY父亲近视 & 0.777$^{**}$ \\ 
  & (0.320) \\ 
  & \\ 
 Constant & 4.441 \\ 
  & (8.244) \\ 
  & \\ 
\hline \\[-1.8ex] 
Observations & 618 \\ 
Log Likelihood & $-$149.834 \\ 
Akaike Inf. Crit. & 329.668 \\ 
\hline 
\hline \\[-1.8ex] 
\textit{Note:}  & \multicolumn{1}{r}{$^{*}$p$<$0.1; $^{**}$p$<$0.05; $^{***}$p$<$0.01} \\ 
\end{tabular} 
\end{table} 

结果的解释略，一般就是罗列 各个自变量的OR值及其可信区间，当然还有P值。


逐步回归stepAIC()函数在MASS包中
安装及导入MASS包


```r
# install.packages('MASS')
library(MASS)
```

```
## 
## 载入程辑包：'MASS'
```

```
## The following object is masked from 'package:dplyr':
## 
##     select
```


```r
#向前法逐步回归
model.forward <- stepAIC(model,direction = "forward")
```

```
## Start:  AIC=329.67
## MYOPIC ~ AGE + GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + 
##     READHR + COMPHR + STUDYHR + TVHR + DIOPTERHR + MOMMY + DADMY
```
 向前法的结果与model的结果相同


```r
#查看模型结果
summary(model.forward)$coefficients
```

```
##                   Estimate  Std. Error    z value     Pr(>|z|)
## (Intercept)     4.44120968  8.24375350  0.5387364 5.900688e-01
## AGE            -0.03881442  0.22541323 -0.1721923 8.632864e-01
## GENDER男性      0.61717965  0.34236629  1.8026881 7.143721e-02
## SPHEQ          -4.10532498  0.46638861 -8.8023696 1.339571e-18
## AL            -30.48167492 39.07930854 -0.7799952 4.353937e-01
## ACD            31.62630058 39.13026518  0.8082312 4.189575e-01
## LT             29.62122512 39.16352324  0.7563473 4.494410e-01
## VCD            30.10871116 39.10057254  0.7700325 4.412806e-01
## SPORTHR        -0.05114162  0.02121883 -2.4101999 1.594378e-02
## READHR          0.07954877  0.04939061  1.6106051 1.072658e-01
## COMPHR          0.04247663  0.04579193  0.9276009 3.536146e-01
## STUDYHR        -0.17344836  0.09730372 -1.7825461 7.466023e-02
## TVHR           -0.01553866  0.02865065 -0.5423493 5.875779e-01
## MOMMY母亲近视   0.71466823  0.32101882  2.2262503 2.599742e-02
## DADMY父亲近视   0.77707257  0.32040311  2.4252966 1.529589e-02
```

```r
#计算OR及其可信区间
exp(cbind("OR"=coef(model.forward),confint(model.forward)))
```

```
## Waiting for profiling to be done...
```

```
##                         OR        2.5 %       97.5 %
## (Intercept)   8.487755e+01 8.359489e-06 1.013864e+09
## AGE           9.619292e-01 6.173921e-01 1.500563e+00
## GENDER男性    1.853693e+00 9.523985e-01 3.662207e+00
## SPHEQ         1.648466e-02 6.254623e-03 3.916984e-02
## AL            5.780651e-14 1.589338e-47 9.207650e+19
## ACD           5.434103e+13 3.124619e-20 2.207870e+47
## LT            7.317026e+12 3.860676e-21 3.105307e+46
## VCD           1.191371e+13 7.171267e-21 4.515572e+46
## SPORTHR       9.501441e-01 9.098254e-01 9.891419e-01
## READHR        1.082798e+00 9.822655e-01 1.192778e+00
## COMPHR        1.043392e+00 9.484491e-01 1.136872e+00
## STUDYHR       8.407606e-01 6.826446e-01 9.979320e-01
## TVHR          9.845814e-01 9.291900e-01 1.040117e+00
## DIOPTERHR               NA           NA           NA
## MOMMY母亲近视 2.043509e+00 1.100632e+00 3.894270e+00
## DADMY父亲近视 2.175095e+00 1.172768e+00 4.139950e+00
```



```r
# 向后法逐步回归
model.backward <- stepAIC(model,direction = 'backward')
```

```
## Start:  AIC=329.67
## MYOPIC ~ AGE + GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + 
##     READHR + COMPHR + STUDYHR + TVHR + DIOPTERHR + MOMMY + DADMY
## 
## 
## Step:  AIC=329.67
## MYOPIC ~ AGE + GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + 
##     READHR + COMPHR + STUDYHR + TVHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - AGE      1   299.70 327.70
## - TVHR     1   299.97 327.97
## - LT       1   300.24 328.24
## - VCD      1   300.26 328.26
## - AL       1   300.28 328.28
## - ACD      1   300.32 328.32
## - COMPHR   1   300.48 328.48
## <none>         299.67 329.67
## - READHR   1   302.24 330.24
## - GENDER   1   302.97 330.97
## - STUDYHR  1   303.61 331.61
## - MOMMY    1   304.81 332.81
## - DADMY    1   305.78 333.78
## - SPORTHR  1   305.98 333.98
## - SPHEQ    1   430.41 458.41
## 
## Step:  AIC=327.7
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + READHR + 
##     COMPHR + STUDYHR + TVHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - TVHR     1   300.01 326.01
## - LT       1   300.28 326.28
## - VCD      1   300.30 326.30
## - AL       1   300.32 326.32
## - ACD      1   300.36 326.36
## - COMPHR   1   300.51 326.51
## <none>         299.70 327.70
## - READHR   1   302.24 328.24
## - GENDER   1   303.11 329.11
## - STUDYHR  1   304.59 330.59
## - MOMMY    1   305.00 331.00
## - DADMY    1   306.02 332.02
## - SPORTHR  1   306.11 332.11
## - SPHEQ    1   431.42 457.42
## 
## Step:  AIC=326.01
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + READHR + 
##     COMPHR + STUDYHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - LT       1   300.53 324.53
## - VCD      1   300.55 324.55
## - AL       1   300.57 324.57
## - ACD      1   300.61 324.61
## - COMPHR   1   300.66 324.66
## <none>         300.01 326.01
## - READHR   1   302.58 326.58
## - GENDER   1   303.26 327.26
## - STUDYHR  1   304.77 328.77
## - MOMMY    1   305.22 329.22
## - DADMY    1   306.86 330.86
## - SPORTHR  1   307.04 331.04
## - SPHEQ    1   431.63 455.63
## 
## Step:  AIC=324.53
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + VCD + SPORTHR + READHR + 
##     COMPHR + STUDYHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - VCD      1   300.76 322.76
## - COMPHR   1   301.10 323.10
## - AL       1   301.16 323.16
## <none>         300.53 324.53
## - READHR   1   303.00 325.00
## - GENDER   1   303.59 325.59
## - ACD      1   303.65 325.65
## - STUDYHR  1   305.13 327.13
## - MOMMY    1   305.57 327.57
## - DADMY    1   307.45 329.45
## - SPORTHR  1   307.78 329.78
## - SPHEQ    1   431.77 453.77
## 
## Step:  AIC=322.76
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + SPORTHR + READHR + COMPHR + 
##     STUDYHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - COMPHR   1   301.32 321.32
## <none>         300.76 322.76
## - AL       1   302.93 322.93
## - READHR   1   303.12 323.12
## - GENDER   1   303.97 323.97
## - STUDYHR  1   305.29 325.29
## - ACD      1   305.36 325.36
## - MOMMY    1   305.81 325.81
## - DADMY    1   307.73 327.73
## - SPORTHR  1   307.97 327.97
## - SPHEQ    1   431.80 451.80
## 
## Step:  AIC=321.32
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + SPORTHR + READHR + STUDYHR + 
##     MOMMY + DADMY
## 
##           Df Deviance    AIC
## <none>         301.32 321.32
## - AL       1   303.57 321.57
## - READHR   1   303.83 321.83
## - GENDER   1   304.04 322.04
## - STUDYHR  1   305.56 323.56
## - ACD      1   305.90 323.90
## - MOMMY    1   306.45 324.45
## - DADMY    1   308.43 326.43
## - SPORTHR  1   308.66 326.66
## - SPHEQ    1   432.60 450.60
```



```r
#查看模型结果
summary(model.backward)$coefficients
```

```
##                  Estimate Std. Error    z value     Pr(>|z|)
## (Intercept)    2.56033979 5.60093665  0.4571271 6.475797e-01
## GENDER男性     0.53028307 0.32273622  1.6430851 1.003653e-01
## SPHEQ         -4.03868027 0.45649567 -8.8471382 8.979219e-19
## AL            -0.39543894 0.26557339 -1.4890006 1.364872e-01
## ACD            1.63424559 0.77313774  2.1137832 3.453379e-02
## SPORTHR       -0.05452022 0.02106352 -2.5883724 9.643065e-03
## READHR         0.07741287 0.04863429  1.5917343 1.114444e-01
## STUDYHR       -0.16987635 0.09078855 -1.8711208 6.132833e-02
## MOMMY母亲近视  0.70567925 0.31745051  2.2229583 2.621861e-02
## DADMY父亲近视  0.82019377 0.31453933  2.6076032 9.117858e-03
```

```r
#计算OR及其可信区间
exp(cbind("OR"=coef(model.backward),confint(model.backward)))
```

```
## Waiting for profiling to be done...
```

```
##                        OR        2.5 %       97.5 %
## (Intercept)   12.94021350 0.0002360486 8.839295e+05
## GENDER男性     1.69941329 0.9059489839 3.224560e+00
## SPHEQ          0.01762071 0.0068264244 4.111080e-02
## AL             0.67338440 0.3962988206 1.126951e+00
## ACD            5.12558972 1.1473016027 2.396792e+01
## SPORTHR        0.94693936 0.9070408144 9.854784e-01
## READHR         1.08048809 0.9816314207 1.188469e+00
## STUDYHR        0.84376914 0.6965302517 9.925322e-01
## MOMMY母亲近视  2.02522185 1.0985399665 3.832492e+00
## DADMY父亲近视  2.27093985 1.2392732094 4.273781e+00
```



```r
#向前向后法逐步回归
model.both <- stepAIC(model,direction = 'both')
```

```
## Start:  AIC=329.67
## MYOPIC ~ AGE + GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + 
##     READHR + COMPHR + STUDYHR + TVHR + DIOPTERHR + MOMMY + DADMY
## 
## 
## Step:  AIC=329.67
## MYOPIC ~ AGE + GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + 
##     READHR + COMPHR + STUDYHR + TVHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - AGE      1   299.70 327.70
## - TVHR     1   299.97 327.97
## - LT       1   300.24 328.24
## - VCD      1   300.26 328.26
## - AL       1   300.28 328.28
## - ACD      1   300.32 328.32
## - COMPHR   1   300.48 328.48
## <none>         299.67 329.67
## - READHR   1   302.24 330.24
## - GENDER   1   302.97 330.97
## - STUDYHR  1   303.61 331.61
## - MOMMY    1   304.81 332.81
## - DADMY    1   305.78 333.78
## - SPORTHR  1   305.98 333.98
## - SPHEQ    1   430.41 458.41
## 
## Step:  AIC=327.7
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + READHR + 
##     COMPHR + STUDYHR + TVHR + MOMMY + DADMY
## 
##           Df Deviance    AIC
## - TVHR     1   300.01 326.01
## - LT       1   300.28 326.28
## - VCD      1   300.30 326.30
## - AL       1   300.32 326.32
## - ACD      1   300.36 326.36
## - COMPHR   1   300.51 326.51
## <none>         299.70 327.70
## - READHR   1   302.24 328.24
## - GENDER   1   303.11 329.11
## + AGE      1   299.67 329.67
## - STUDYHR  1   304.59 330.59
## - MOMMY    1   305.00 331.00
## - DADMY    1   306.02 332.02
## - SPORTHR  1   306.11 332.11
## - SPHEQ    1   431.42 457.42
## 
## Step:  AIC=326.01
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + LT + VCD + SPORTHR + READHR + 
##     COMPHR + STUDYHR + MOMMY + DADMY
## 
##             Df Deviance    AIC
## - LT         1   300.53 324.53
## - VCD        1   300.55 324.55
## - AL         1   300.57 324.57
## - ACD        1   300.61 324.61
## - COMPHR     1   300.66 324.66
## <none>           300.01 326.01
## - READHR     1   302.58 326.58
## - GENDER     1   303.26 327.26
## + DIOPTERHR  1   299.70 327.70
## + TVHR       1   299.70 327.70
## + AGE        1   299.97 327.97
## - STUDYHR    1   304.77 328.77
## - MOMMY      1   305.22 329.22
## - DADMY      1   306.86 330.86
## - SPORTHR    1   307.04 331.04
## - SPHEQ      1   431.63 455.63
## 
## Step:  AIC=324.53
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + VCD + SPORTHR + READHR + 
##     COMPHR + STUDYHR + MOMMY + DADMY
## 
##             Df Deviance    AIC
## - VCD        1   300.76 322.76
## - COMPHR     1   301.10 323.10
## - AL         1   301.16 323.16
## <none>           300.53 324.53
## - READHR     1   303.00 325.00
## - GENDER     1   303.59 325.59
## - ACD        1   303.65 325.65
## + LT         1   300.01 326.01
## + TVHR       1   300.28 326.28
## + DIOPTERHR  1   300.28 326.28
## + AGE        1   300.48 326.48
## - STUDYHR    1   305.13 327.13
## - MOMMY      1   305.57 327.57
## - DADMY      1   307.45 329.45
## - SPORTHR    1   307.78 329.78
## - SPHEQ      1   431.77 453.77
## 
## Step:  AIC=322.76
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + SPORTHR + READHR + COMPHR + 
##     STUDYHR + MOMMY + DADMY
## 
##             Df Deviance    AIC
## - COMPHR     1   301.32 321.32
## <none>           300.76 322.76
## - AL         1   302.93 322.93
## - READHR     1   303.12 323.12
## - GENDER     1   303.97 323.97
## + TVHR       1   300.50 324.50
## + DIOPTERHR  1   300.50 324.50
## + VCD        1   300.53 324.53
## + LT         1   300.55 324.55
## + AGE        1   300.73 324.73
## - STUDYHR    1   305.29 325.29
## - ACD        1   305.36 325.36
## - MOMMY      1   305.81 325.81
## - DADMY      1   307.73 327.73
## - SPORTHR    1   307.97 327.97
## - SPHEQ      1   431.80 451.80
## 
## Step:  AIC=321.32
## MYOPIC ~ GENDER + SPHEQ + AL + ACD + SPORTHR + READHR + STUDYHR + 
##     MOMMY + DADMY
## 
##             Df Deviance    AIC
## <none>           301.32 321.32
## - AL         1   303.57 321.57
## - READHR     1   303.83 321.83
## - GENDER     1   304.04 322.04
## + COMPHR     1   300.76 322.76
## + VCD        1   301.10 323.10
## + LT         1   301.12 323.12
## + TVHR       1   301.20 323.20
## + DIOPTERHR  1   301.22 323.22
## + AGE        1   301.29 323.29
## - STUDYHR    1   305.56 323.56
## - ACD        1   305.90 323.90
## - MOMMY      1   306.45 324.45
## - DADMY      1   308.43 326.43
## - SPORTHR    1   308.66 326.66
## - SPHEQ      1   432.60 450.60
```



```r
#查看模型结果
summary(model.both)
```

```
## 
## Call:
## glm(formula = MYOPIC ~ GENDER + SPHEQ + AL + ACD + SPORTHR + 
##     READHR + STUDYHR + MOMMY + DADMY, family = binomial(), data = data)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.6771  -0.4037  -0.2113  -0.0670   3.2545  
## 
## Coefficients:
##               Estimate Std. Error z value Pr(>|z|)    
## (Intercept)    2.56034    5.60094   0.457  0.64758    
## GENDER男性     0.53028    0.32274   1.643  0.10037    
## SPHEQ         -4.03868    0.45650  -8.847  < 2e-16 ***
## AL            -0.39544    0.26557  -1.489  0.13649    
## ACD            1.63425    0.77314   2.114  0.03453 *  
## SPORTHR       -0.05452    0.02106  -2.588  0.00964 ** 
## READHR         0.07741    0.04863   1.592  0.11144    
## STUDYHR       -0.16988    0.09079  -1.871  0.06133 .  
## MOMMY母亲近视  0.70568    0.31745   2.223  0.02622 *  
## DADMY父亲近视  0.82019    0.31454   2.608  0.00912 ** 
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 480.08  on 617  degrees of freedom
## Residual deviance: 301.32  on 608  degrees of freedom
## AIC: 321.32
## 
## Number of Fisher Scoring iterations: 7
```

```r
#计算OR及其可信区间
exp(cbind("OR"=coef(model.both),confint(model.both)))
```

```
## Waiting for profiling to be done...
```

```
##                        OR        2.5 %       97.5 %
## (Intercept)   12.94021350 0.0002360486 8.839295e+05
## GENDER男性     1.69941329 0.9059489839 3.224560e+00
## SPHEQ          0.01762071 0.0068264244 4.111080e-02
## AL             0.67338440 0.3962988206 1.126951e+00
## ACD            5.12558972 1.1473016027 2.396792e+01
## SPORTHR        0.94693936 0.9070408144 9.854784e-01
## READHR         1.08048809 0.9816314207 1.188469e+00
## STUDYHR        0.84376914 0.6965302517 9.925322e-01
## MOMMY母亲近视  2.02522185 1.0985399665 3.832492e+00
## DADMY父亲近视  2.27093985 1.2392732094 4.273781e+00
```




