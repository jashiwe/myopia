---
output:
  pdf_document: default
  html_document: default
---
\mainmatter

# 临床预测模型

常见的思路总结为三步：

1.模型构建 

2.模型评价 

3.模型验证

## 模型构建

最熟悉的方法是：先单后多。大多数可行。但当变量数过多,变量之间存在共线性或缺失值过多而又不愿舍弃掉缺失值的样本， 有局限性。 

如何解决？ 

1.共线性问题------岭回归、*lasso*、弹性网络模型（正则技术） 

2.缺失值问题------随机森林模型 

要解决的主要问题：变量筛选

方法1：逐步回归（向后法、向前法、向前向后法） 

方法2：正则技术（岭回归、lasso、弹性网络模型) 

方法3：树模型 

方法4：随机森林模型（树模型的扩展）

方法5：主成分分析

## 模型评价

为什么要评价模型？

* 欠拟合
* 过拟合

 常见的评价指标主要有以下几种 
 1.拟合优度检验（涉及卡方值及P值） 
 
 2.ROC（涉及AUC,sen,spe,accuracy等指标） 
 
 3.calibration（涉及C-index的计算） 
 
 4.终极指标MSE的计算 
 
 5.其他 
 
 通常来说，完成模型评价已经可以称之为"完整"的研究。


**过拟合呢？或者是外推性如何？**

## 模型验证

1.cross validation(*简单交叉*;K-fold corss validation;N-fold cross validation) 
2.bootstrap 
3.crossvalidation+bootstrap（最常用）

ps:三个过程可能需要多次操作，才可以得到最终的结果。

