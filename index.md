--- 
title: "近视预测模型开发与验证"
author: "张献伟"
date: "2022-07-25"
documentclass: ctexbook
bibliography: [book.bib, packages.bib]
biblio-style: apalike
link-citations: yes
colorlinks: yes
lot: yes
lof: yes
geometry: [b5paper, tmargin=2.5cm, bmargin=2.5cm, lmargin=3.5cm, rmargin=2.5cm]
site: bookdown::bookdown_site
description: "预测模型学习"
github-repo: yihui/bookdown-chinese
#cover-image: images/cover.jpg
---



# 前言 {-}

你好，世界。我写了一本书。这本书是这样的，第 \@ref(intro) 章介绍了见第\@ref(intro)，第 \@ref(wind) 章说见\@ref(wind)，然后是请自己阅读吧！！！

我用了两个 R 包编译这本书，分别是 **knitr**\index{knitr} [@xie2015] 和 **bookdown**\index{bookdown} [@R-bookdown]。以下是我的 R 进程信息：


```r
sessionInfo()
```

```
## R version 4.1.1 (2021-08-10)
## Platform: x86_64-w64-mingw32/x64 (64-bit)
## Running under: Windows 7 x64 (build 7601) Service Pack 1
## 
## Matrix products: default
## 
## locale:
## [1] LC_COLLATE=Chinese (Simplified)_People's Republic of China.936 
## [2] LC_CTYPE=Chinese (Simplified)_People's Republic of China.936   
## [3] LC_MONETARY=Chinese (Simplified)_People's Republic of China.936
## [4] LC_NUMERIC=C                                                   
## [5] LC_TIME=Chinese (Simplified)_People's Republic of China.936    
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets 
## [6] methods   base     
## 
## loaded via a namespace (and not attached):
##  [1] compiler_4.1.1  magrittr_2.0.1  fastmap_1.1.0  
##  [4] bookdown_0.24   cli_3.3.0       htmltools_0.5.2
##  [7] tools_4.1.1     rstudioapi_0.13 yaml_2.2.1     
## [10] stringi_1.7.4   rmarkdown_2.11  knitr_1.34     
## [13] stringr_1.4.0   digest_0.6.27   xfun_0.25      
## [16] rlang_1.0.4     evaluate_0.14
```

## 致谢 {-}

感谢我自己的努力。

\BeginKnitrBlock{flushright}
张献伟  
于 某角落
\EndKnitrBlock{flushright}

